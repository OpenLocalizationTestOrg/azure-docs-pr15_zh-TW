<properties
    pageTitle="使用可靠的集合 |Microsoft Azure"
    description="瞭解使用可靠的集合的最佳作法。"
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>使用可靠的集合

服務布料的轉印圖樣會透過可靠的集合.NET 開發人員提供的狀態程式設計模型。 具體來說，服務布料的轉印圖樣提供可靠的字典和可靠的佇列中的類別。 當您使用這些類別時，州分割 （適用於延展性）、 （適用於顯示狀態） 加以複製，和交易內的資料分割 （ACID 語意）。 現在就讓我們看看可靠的字典物件的一般的用法，並查看哪些其實際執行。

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

（除了 ClearAsync 這是無法復原)，可靠的字典物件上的所有作業都需要 ITransaction 物件。 這個物件並相關的任何及所有變更您正在嘗試進行任何可靠的字典，及/或可靠都佇列中單一磁碟分割的物件。 擷取 ITransaction 物件，則可電話磁碟分割的 StateManager 的 CreateTransaction 方法。
 
在上述的程式碼，ITransaction 物件傳遞至可靠的字典 AddAsync 方法。 在內部，接受索引鍵的字典方法需要與索引鍵關聯的讀取裝置/寫入鎖定。 如果這個方法修改索引鍵的值，方法使用寫入鎖定索引鍵，如果方法只會讀取索引鍵的值，然後讀取的鎖定採取索引鍵。 由於 AddAsync 修改鍵值至新的傳入的值，就會採取的金鑰寫入鎖定。 因此，如果 2 （或多個） 執行緒嘗試同時新增相同的金鑰的值，一個執行緒會取得寫入鎖定和其他執行緒會封鎖。 根據預設，方法封鎖 4 秒鐘取得鎖定;4 秒鐘之後，這些方法會引發 「 逾時。 方法多載存在可讓您將明確逾時值傳遞如果您偏好。
 
通常您撰寫程式碼來擷取它和 （如下所示上述的程式碼），重試整個作業回應 「 逾時。 在我簡單的程式碼，只要您撥號 Task.Delay 傳遞 100 毫秒每次。 但事實上，您可能會改為使用某種指數返回關閉延遲。
 
一旦鎖定，則 AddAsync 會將內部暫時字典 ITransaction 物件相關聯的索引鍵和值物件參照。 這是可讓您讀取-您-擁有-寫入語意。 就是您連絡 AddAsync 之後，更新呼叫 TryGetValueAsync （使用相同的 ITransaction 物件） 會傳回值即使尚未認可交易。 接下來，AddAsync 序列化金鑰和值物件為位元組陣列，並將這些位元組陣列附加到記錄檔中的本機節點。 最後，AddAsync 會傳送位元組陣列給所有次要複本讓他們擁有相同的鍵值資訊。 即使鍵值資訊寫入記錄檔，資訊並不是字典的一部分，直到其與相關聯的交易已經認可。 

在上述的程式碼，CommitAsync 通話認可所有交易的作業。 具體來說，它會附加認可資訊至本機節點的記錄檔，並也會認可記錄傳送給所有次要的複本。 一旦回覆仲裁 （大部分） 的複本，所有的資料變更視為永久性且任何已透過 ITransaction 物件操作的按鍵相關聯的鎖定發行以便其他執行緒/交易可以處理相同的索引鍵和其值。

如果沒有呼叫 CommitAsync （通常是因為例外狀況），取得處置 ITransaction 物件。 當處置未認可的 ITransaction 物件，服務布料的轉印圖樣附加中止資訊至本機節點的記錄檔，並不需要傳送給任何第二個複本。 然後釋放任何已透過交易操作的按鍵相關聯的鎖定。

## <a name="common-pitfalls-and-how-to-avoid-them"></a>常見的錯誤，以及如何避免
現在您瞭解可靠的集合內部運作的方式，讓我們來看看這些一些常見第三人。 請參閱以下的程式碼︰

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

使用一般的.NET 字典工作時，您可以將鍵值新增至字典，然後變更 [屬性 （例如 LastLogin) 的值。 不過，將此程式碼將無法使用可靠的字典正確運作。 請記住從舊的討論，AddAsync 通話序列化位元組陣列鍵值物件，然後儲存至本機檔案的陣列也次要複本傳送給他們。 如果您稍後變更屬性，這會變更屬性的值，在記憶體它不會影響本機的檔案或傳送至複本的資料。 如果程序損毀，功能記憶體離開狀況。 新的程序啟動時或另一個複本會變成主要，舊的屬性值是會以所提供功能。 

我一再不夠如何輕鬆進行如上所示的錯誤的類型。 並，您會只瞭解錯誤 if/時當機的程序。 撰寫程式碼正確的方式是只是反向的兩行︰

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

以下是另一個範例顯示常見的錯誤︰

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

同樣地，一般.NET 字典，以上的程式碼運作正常與是常見的模式︰ 開發人員使用金鑰來查閱值。 如果值存在，開發人員變更屬性的值。 不過，使用可靠的集合，將此程式碼展示如上文所討論的相同問題︰__您必須修改物件，一旦您所可靠的集合給。__
 
取得至現有的值的參照，請考慮不變此參照所參照的物件，是正確的方式更新可靠的集合中的值。 然後，建立新的物件是原始物件的確切複本。 現在，您可以修改這個新物件的狀態，並撰寫新集合內的物件，讓它取得序列化位元組陣列附加到本機的檔案，傳送給複本。 先測試變更之後, 記憶體內的物件、 本機檔案及所有複本都有相同的確切狀態。 只是很好 ！

下列程式碼會顯示正確的方式更新可靠的集合中的值︰

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>定義固定的資料類型，以防止程式設計錯誤

理想的情況下，我們希望編譯器報告錯誤時您不小心產生目前狀態的物件，您應該考慮不變的程式碼。 但 C# 編譯器沒有可執行此動作。 因此，若要避免潛在的程式設計錯誤，我們強烈建議您定義類型使用可靠的集合，不變的類型。 具體來說，這表示您讓核心值類型 （例如 [Int32、 UInt64 等] 的數字、 日期時間、 Guid、 時段和等）。 然後，您也可以使用字串。 最好避免為序列化集合內容，並還原會經常可以破壞效能。 不過，如果您想要使用的集合屬性，我們強烈建議使用。網路的不變集合文件庫 (System.Collections.Immutable)。 使用從 http://nuget.org 下載此文件庫。 我們也建議課程並進行盡可能欄位為唯讀。

使用者資訊類型下列示範如何定義不變類型利用上述建議。

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

項目識別碼類型也是不變的類型，如下所示︰

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>結構描述版本設定 （升級）

在內部，可靠的集合序列化您使用的物件。網路的 DataContractSerializer。 序列的物件會保存主要複本的本機磁碟，也會傳送至次要複本。 為您的服務成熟時，很可能會想要變更您的服務要求的資料 （結構描述） 種類。 您必須接近小心資料的版本設定。 首先和最重要，您必須永遠能夠還原序列化舊的資料。 具體來說，這表示您還原序列化代碼必須是無限回溯相容︰ 服務程式碼的版本 333 必須為能處理放可靠集合中的服務程式碼的第 1 版 5 年之內的資料。

此外，服務代碼是升級一個升級的網域一次。 因此，在升級期間，必須同時執行服務程式碼的兩個不同版本。 您必須避免服務程式碼的新版本，使用新的結構描述，如服務程式碼的舊版本可能無法處理新的結構描述。 可能的話，您應該設計您的服務，由 1 版本相容的每個版本。 具體來說，這表示您的服務程式碼 V1 應該能夠只要略過不明確地處理任何結構描述元素。 不過，必須能夠儲存的任何資料，明確並不知道和只要將它寫回更新的字典索引鍵或值時。 

>[AZURE.WARNING] 雖然您可以修改索引鍵的結構描述，您必須先確定您的金鑰雜湊程式碼和等於演算法會穩定。 如果您變更這些演算法其中一項操作方式，您將無法再查閱可靠的字典內的索引鍵。
  
或者，您可以執行什麼通常稱為 「 2 階段升級。 升級至 2 階段版本與升級您的服務 V1 V2: V2 包含的程式碼，瞭解如何處理新的結構描述變更，但不會執行這個程式碼。 當 V2 程式碼會讀取 V1 資料時，它會在其上的運作方式，並將 V1 資料。 然後，升級完成所有升級的網域後，您可以設法通知執行 V2 執行個體升級完畢。 （這是推行設定升級其中一個方法訊號，這就是讓此 2 階段升級）。現在，V2 執行個體可以讀取 V1 資料、 將它轉換成 V2 資料、 操作，並做為 V2 資料寫出。 當其他執行個體讀取 V2 資料時，不需要將其轉換時，他們只要操作，然後撰寫出 V2 資料。

## <a name="next-steps"></a>後續步驟
若要瞭解如何建立合約轉寄相容的資料，請參閱[往後相容資料合約](https://msdn.microsoft.com/library/ms731083.aspx)。

在 [版本設定資料合約的最佳作法，請參閱[資料合約版本設定](https://msdn.microsoft.com/library/ms731138.aspx)。 

若要瞭解如何實作版本容錯資料合約，請參閱[版本相容序列化回撥](https://msdn.microsoft.com/library/ms733734.aspx)。 

若要瞭解如何提供資料結構，可以相互操作多個版本，請參閱[IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)。
