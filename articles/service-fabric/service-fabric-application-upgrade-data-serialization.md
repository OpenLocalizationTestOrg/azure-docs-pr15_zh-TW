<properties
   pageTitle="應用程式升級︰ 資料序列化 |Microsoft Azure"
   description="最佳作法資料序列化和它會循環應用程式升級的影響。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>


# <a name="how-data-serialization-affects-an-application-upgrade"></a>資料序列化如何影響應用程式升級

[循環應用程式升級](service-fabric-application-upgrade.md)升級會套用至子節點，一次一個升級的網域。 在過程中，某些升級的網域會在您的應用程式的新版本，某些升級的網域會出現在您的應用程式的舊版本上。 首展期間新版本的應用程式必須能閱讀舊版本的資料，且舊版本的應用程式必須為能閱讀您資料的新版本。 如果資料格式不是轉寄並回溯相容，可能會失敗升級，或資料糟的是，可能會遺失或損毀。 本文將告訴您什麼構成您資料的格式，並提供確保您的資料上下的最佳作法相容。


## <a name="what-makes-up-your-data-format"></a>為何設定您的資料格式？

Azure 服務布料的轉印圖樣，在保存及複製的資料是來自 C# 類別。 使用的應用程式[可靠的集合](service-fabric-reliable-services-reliable-collections.md)，這是可靠的字典和佇列中的物件。 使用的應用程式[可靠的動作項目](service-fabric-reliable-actors-introduction.md)，這是支援狀態動作項目。 這些 C# 類別必須序列化保存及複製。 因此，資料格式所定義之欄位和序列化，以及如何他們序列化的屬性。 例如，在`IReliableDictionary<int, MyClass>`的資料是序列`int`和序列`MyClass`。

### <a name="code-changes-that-result-in-a-data-format-change"></a>程式碼會變更結果中的資料格式變更

由於資料格式由 C# 類別，類別的變更可能會導致的資料格式變更。 小心，以確保輪流升級可處理資料的格式變更。 可能會導致資料格式變更的範例︰

- 新增或移除欄位] 或 [摘要資訊
- 重新命名欄位或屬性
- 變更欄位的內容類型
- 變更類別名稱或命名空間

### <a name="data-contract-as-the-default-serializer"></a>預設序列化程式的資料合約

序列化程式負責通常讀取資料，以及其序列化至目前的版本，即使資料是較舊版本或*更新*版本。 預設序列化工具是[資料合約序列化程式](https://msdn.microsoft.com/library/ms733127.aspx)，有明確定義的版本設定規則。 可靠的集合允許序列化程式覆寫，但不是可靠的動作項目目前執行。 資料序列化程式會播放啟用循環升級重要的角色。 資料合約序列化是我們建議服務布料的轉印圖樣應用程式的序列化工具。


## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>資料格式如何影響輪流升級

循環升級期間，有兩種主要的案例序列化程式可能會遇到的較舊版本或*較新*版本的資料︰

1. 新的序列化節點升級，並開始備份後，會載入保存的資料到舊版本的磁碟。
2. 循環升級時，叢集會包含您的程式碼的舊和新版本的組合。 由於複本可能會放在不同的升級網域，而且複本傳送資料至彼此，您的資料的新及/或舊版本可能遇到您序列化新及/或舊版本。

> [AZURE.NOTE] 「 新的版本 」 和 「 舊 」 以下參照到您正在執行的程式碼的版本。 「 新序列化程式 」 指的是在您的應用程式的新版本中執行的序列化程式碼。 「 新資料 」 指的是序列 C# 類別從您的應用程式的新版本。

兩個版本的程式碼和資料的格式必須是轉寄和回溯相容。 如果不相容，輪流升級可能會失敗，或可能會遺失資料。 循環升級可能會失敗，因為程式碼或序列化可能引發例外狀況或錯誤時遇到其他版本。 如果，例如新增新屬性，但是舊序列化還原序列化期間捨棄此，可能會遺失資料。

資料合約是建議的解決方案，以確保您的資料相容。 有明確定義的版本設定新增、 移除及變更欄位的規則。 它也有支援處理未知的欄位，將序列化和還原序列化程序，將連結和處理類別繼承。 如需詳細資訊，請參閱[使用資料合約](https://msdn.microsoft.com/library/ms733127.aspx)。


## <a name="next-steps"></a>後續步驟

[您的應用程式使用 Visual Studio Uprading](service-fabric-application-upgrade-tutorial.md)會引導您使用 Visual Studio 應用程式升級。

[您的應用程式使用 Powershell Uprading](service-fabric-application-upgrade-tutorial-powershell.md)會引導您使用 PowerShell 應用程式升級。

控制您的應用程式使用[升級參數](service-fabric-application-upgrade-parameters.md)升級的方式。

瞭解如何時參考[進階主題](service-fabric-application-upgrade-advanced.md)升級您的應用程式使用進階的功能。

參照中[的疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)的步驟，在應用程式升級中修正常見問題的解決辦法。
