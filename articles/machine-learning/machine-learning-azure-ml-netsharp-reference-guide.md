<properties 
    pageTitle="網路 # 類神經網路規格語言指南 |Microsoft Azure" 
    description="語法網路 # 類神經網路規格語言，以及如何使用網路的 Microsoft Azure 毫升中建立自訂的類神經網路模型的範例#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Azure 電腦學習的指南網路 # 類神經網路規格語言

## <a name="overview"></a>概觀
網路 # 是用來在 Microsoft Azure 電腦學習定義類神經網路模組類神經網路架構的 Microsoft 開發的語言。 在本文中，您將學習︰  

-   相關類神經網路的基本概念
-   類神經網路需求，以及如何定義的主要元件
-   語法和網路 # 規格語言的關鍵字
-   使用網路所建立的自訂類神經網路的範例# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>類神經網路的基本概念
節點之間***節點***的組織***圖層***，然後加權的***連線***（或***邊***） 中包含的類神經網路結構。 連線的方向，且每個連線的***來源***節點並***目的***節點。  

每個***trainable 層級***（隱藏或輸出層） 有一或多個***連線配搭***。 連線套件包含來源圖層和規格從來源圖層的連線。 指定的套件中的所有連線都共用相同的***來源層級***和相同***目的地的圖層***。 在網路 # 連線套件會被視為屬於套件的目的地圖層。  
 
網路 # 支援各種配搭，可讓您自訂的方法輸入會對應到隱藏的圖層，而且對應到輸出連線。   

預設或標準套件組合是**完整套件**，在其中來源圖層中的每個節點已連線至目的地圖層中每個節點。  

此外，網路 # 支援進階的連線配搭下列四種︰  

-   **篩選配搭**。 使用者可以使用來源層級節點和目的層級節點的位置，以定義述詞。 述詞為 True 時，會連接節點。
-   **Convolutional 配搭**。 使用者可以在來源圖層中定義節點的小型鄰近的地區。 在目的地圖層中的每個節點已連線至一個鄰近來源圖層中的節點。
-   **共用配搭**和**回應正規化配搭**。 在該使用者定義的節點的小型鄰近地區來源圖層中，這些是 convolutional 配搭類似。 差異是在這些配搭邊緣的粗細不會 trainable。 不過，預先定義的函數會套用至來源節點值，以決定目的地節點的值。  

使用網路 # 定義的結構類神經網路的可以讓您定義複雜的結構，例如深類神經網路或 convolutions 的已知會改善學習資料，例如圖像、 音訊或視訊中的任意維度。  

## <a name="supported-customizations"></a>支援的自訂項目
使用網路 # 可以全面自訂類神經網路模型中 Azure 電腦學習建立架構。 您可以︰  

-   建立隱藏圖層，並控制各個圖層中的節點的數字。
-   指定連線到其他圖層的方式。
-   定義特殊連線結構，例如 convolutions 和共用配搭的寬度。
-   指定不同的啟動函數。  

如需規格語言的語法的詳細資訊，請參閱[結構規格](#Structure-specifications)。  
 
如需範例定義類神經網路的一些常見的電腦學習任務，請從單工到複雜，請參閱[範例](#Examples-of-Net#-usage)。  

## <a name="general-requirements"></a>一般需求
-   必須要有一個輸出圖層，至少有一個輸入層級]，或多個隱藏的圖層。 
-   各個圖層具有固定的數目的概念排列任意尺寸的矩形陣列中的節點。 
-   輸入圖層沒有相關聯的訓練參數，而且代表執行個體資料輸入網路的位置的點。 
-   Trainable 層級 （隱藏與輸出圖層） 都有相關聯的訓練的參數，稱為 [線寬] 與 biases。 
-   來源與目的地節點必須在另一個圖層。 
-   連線必須非循環;換句話說，無法連線引導回初始來源節點的鏈結。
-   在 [圖層輸出無法連線套件的來源圖層。  

## <a name="structure-specifications"></a>結構規格
類神經網路結構規格由三個區段所組成︰**常數宣告**、**圖層宣告****連線宣告**。 此外，還有選擇性**共用宣告**] 區段。 任何順序，可以指定節。  

## <a name="constant-declaration"></a>常數宣告 
常數宣告為選用步驟。 它會提供一種方法來定義類神經網路定義中的其他地方使用的值。 宣告陳述式包含識別項後面加上等號和數值運算式。   

例如，下列陳述式定義常數**x**:  


    Const X = 28;  

若要同時定義兩個或多個常數，大括號括住的識別碼名稱和值，並使用分號隔開。 例如︰  

    Const { X = 28; Y = 4; }  

在右邊的 [工作分派的每個運算式可以是整數、 實數、 布林值 （True 或 False） 或的數學運算式。 例如︰  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>圖層宣告
需要的圖層宣告。 它會定義的大小和圖層，包括其連線配搭及屬性的資料來源。 宣告陳述式開頭的圖層] （輸入、 隱藏，或輸出） 名稱後面的圖層 (的正整數，表示 tuple) 的大小。 例如︰  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   維度的產品是在 [圖層中的節點數目。 在此範例中，有兩個維度 [5,20]，這表示圖層中有 100 節點。
-   圖層可以宣告任何順序，有一個例外狀況︰ 如果已定義一個以上的輸入圖層，在宣告的順序必須符合的順序，輸入資料中的功能。  


若要指定的圖層中的節點數目會自動判定，使用 [**自動**關鍵字。 **自動**關鍵字具有不同的效果，根據圖層︰  

-   輸入圖層宣告中的節點數目是輸入資料中的功能數目。
-   隱藏圖層宣告中的節點數目是**隱藏節點的數字**的指定參數值的數目。 
-   輸出圖層宣告中的節點數目是 2 的兩個類別分類，迴歸分析，和 multiclass 分類的輸出節點的數等於 1。   

例如，下列網路定義可讓所有的圖層來自動判定的大小︰  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


圖層宣告 trainable 的圖層 （隱藏或輸出圖層） 可以選擇性地包含輸出函數 （也稱為啟動函數）、 預設為**sigmoid**分類模型和**線性**迴歸模型。 （即使您使用預設值，您可以明確狀態啟動函數，如果您為了避免混淆。）

支援下列輸出函數︰  

-   sigmoid
-   線性
-   softmax
-   rlinear
-   方形
-   sqrt
-   srlinear
-   abs
-   tanh 
-   brlinear  

例如，下列宣告使用**softmax**函數︰  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>連線宣告
緊接定義 trainable 的圖層，您必須宣告您所定義的層級之間的連線。 連線套件宣告開頭的關鍵字**從**，後面接著套件的來源圖層，若要建立的連線套件類型的名稱。   

目前，支援連線配搭的五種︰  

-   **完整**配搭，以關鍵字**所有**表示
-   **篩選**配搭，以關鍵字**位置**，後面接著述詞的運算式
-   **Convolutional**配搭，以關鍵字**convolve**，後面接著迴旋屬性
-   **共用**配搭的關鍵字**最大的資料庫**或**資料庫的意思**
-   **回應正規化**配搭，以關鍵字**回應非線性**      

## <a name="full-bundles"></a>完整配搭  

完整連線套件包含目的地圖層中的每個節點的來源圖層中的每個節點的連線。 這是預設的網路連線類型。  

## <a name="filtered-bundles"></a>篩選的配搭
篩選的連線套件規格包含述詞、 語法，表示很多，例如 C# lambda 運算式。 下列範例會定義兩個篩選的配搭︰  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   在_ByRow_的述詞， **s**代表索引輸入圖層，_像素為單位_的節點的矩形陣列中的參數，而**d**為參數，代表節點的隱藏圖層， _ByRow_陣列中的索引。 **S**和**d**的類型是長度的，表示 tuple 兩的整數。 概念， **s**範圍的整數，且所有成對透過_0 < = s [0] < 10_和_0 < = s[1] < 20_，和**d**範圍的整數、 所有組與_0 < = d [0] < 10_和_0 < = d[1] < 12_。 
-   在右側的述詞運算式，沒有條件。 在此範例中，每個值的**s**和**d** ，例如條件為 True，有至目的地層級節點從來源層級節點邊緣。 因此，此篩選運算式會指出套件包含與所定義的**s** **d**中所有的情況下 s [0] 等於 d [0] 所定義的節點的節點的連線。  

或者，您可以指定權重篩選的套件的一的組。 **線寬]**屬性的值必須符合所定義的套件連線數目的長度浮動點值，表示 tuple。 根據預設，隨機產生線寬]。  

目的節點索引的分組，寬度值。 如果第一份目的地節點連線到 K 來源節點，**加權**tuple 的第一個_K_元素是第一份目的地節點，順序來源索引的權重。 相同適用於其餘的目的地節點。  

若要將指定粗細直接常數值可能是。 例如，如果您先前已經瞭解線寬]，您可以指定其為常數使用此語法︰

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Convolutional 配搭
當訓練資料時的同類型的結構，convolutional 連線通常用來瞭解資料的高層級的功能。 例如中圖像、 音訊或視訊資料，空間或暫時維度可以是相當統一。  

Convolutional 配搭採用矩形**核心**slid 透過維度。 基本上，每個核心定義一組的權重套用在本機鄰近地區，稱為 「**核心應用程式**。 每個核心應用程式會對應到**中央節點**指在來源圖層中的節點。 連線數目之間共用核心的權重。 在 convolutional 套件中，每個核心矩形，而所有的核心應用程式與相同的大小。  

Convolutional 配搭支援下列屬性︰

**InputShape**定義此 convolutional 套件的來源層級的維度。 值必須是正數的整數，表示 tuple。 之整數必須等於來源圖層中的節點數目，但是否則它不需要以符合維度宣告來源圖層。 這表示 tuple 的長度會變成 convolutional 套件的**引數數目**值。 （通常是引數數目參照引數或運算元函數可採取的數目。）  

若要定義的圖形及核心的位置，請使用屬性**KernelShape**、**分散**、**與邊框距離**、 **LowerPad**及**UpperPad**:   

-   **KernelShape**: （必要） 定義的每個核心 convolutional 套件的維度。 值必須是正數的整數，等於套件的引數數目的長度，表示 tuple。 這表示 tuple 的每個元件都必須是不大於**InputShape**的對應元素。 
-   **分散**: （選擇性） 定義滑動步驟的大小迴旋 （一個步驟大小為每個維度） 的中央節點之間的距離。 值必須是，表示 tuple 的正整數長度是套件的引數數目。 這表示 tuple 的每個元件都必須是不大於**KernelShape**的對應元素。 預設值是等於一的所有元件，表示 tuple。 
-   **共用**: （選擇性） 定義共用迴旋的每個維度的權重。 值可以是單一布林值或的是套件的引數數目的長度，則為 True 值，表示 tuple。 為正確的長度與所有元件等於指定的值，表示 tuple 延伸單一布林值。 預設值是包含所有，則為 True 的值，表示 tuple。 
-   **MapCount**: （選擇性） 定義之 convolutional 套件地圖服務的功能數目。 值可以是單一或正整數，表示 tuple 的正整數長度是套件的引數數目。 單一整數值延伸至是正確的長度與第一個元件等於指定的值，表示 tuple，等於到其中的所有剩餘的元件。 預設值為 1。 功能地圖的總數是 tuple 的元件。 此總數分解元件會決定目的地節點中分組的功能對應值的方式。 
-   **線寬]**: （選擇性） 定義套件初始權重。 值必須是，表示 tuple 浮動長度是核心次數點值的權重核心，每本文稍後的定義。 隨機產生預設粗細。  

有兩組常用屬性可控制邊框正在互斥的屬性︰

-   **與邊框距離**: （選擇性） 決定是否要使用**預設邊框配置**填補輸入。 值可以是單一布林值，或的是套件的引數數目的長度，則為 True 值，表示 tuple。 為正確的長度與所有元件等於指定的值，表示 tuple 延伸單一布林值。 如果維度的值為 True，來源邏輯填補中的維度與支援額外的核心應用程式的零值的儲存格的中央節點的該維度中的第一個和最後一個核心名字和姓氏中節點的來源圖層中的維度。 因此，每個維度 」 dummy 」 的節點數目會自動決定，以完全符合_(InputShape [d]-1) / 分散 [d] + 1_核心到填補的來源圖層。 如果維度的值為 False，使每一邊會保留取出的節點數目 （進位到 1 的差異） 相同定義核心。 此屬性的預設值是與所有元件等於為 False，表示 tuple。
-   **UpperPad**和**LowerPad**: （選擇性） 提供更好地控制留白，若要使用的量。 **重要︰**您可以定義這些屬性，只有上方的 [**與邊框距離**屬性***未***定義。 此值必須是套件的引數數目的長度的整數值 tuple。 這些屬性指定時，輸入圖層的每個維度上下結尾新增 「 dummy 」 的節點。 新增至每個維度上下端點的節點數目取決於**LowerPad**[i] 和**UpperPad**[i] 分別。 若要確保核心對應只節點 「 真正 」，而不要到 「 dummy 」 的節點，必須符合下列條件︰
    -   **LowerPad**的每個元件都必須是絕對小於 KernelShape [d] / 2。 
    -   **UpperPad**的每個元件都必須是不大於 KernelShape [d] / 2。 
    -   這些屬性的預設值是與所有元件等於 0，表示 tuple。 

設定**邊框距離**= true，則可讓視是 」 的中心保持 「 核心 」 即時 」 內輸入無邊框距離。 這會變更運算輸出大小的位元的數學運算。 一般而言，輸出_D_ ，會計算大小為_D = (I-K) / S + 1__我_在哪裡輸入的大小， _k 核心大小_、 _S_是分散，及_/_是整數除法 （圓形趨近於零）。 如果您設定 UpperPad = [1，1]，輸入的大小_我_實際上是 29，也就是_D = (29-5) / 2 + 1 = 13_。 不過，當**與邊框距離**= true，基本上_我_取得兩點_K-1_;因此_D = ((28 + 4)-5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_。 **UpperPad**和**LowerPad**指定的值，您取得更多控制邊框比如果您只要設定**與邊框距離**= true。

如需有關 convolutional 網路和相關應用程式的詳細資訊，請參閱下列文章︰  

-   [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html )
-   [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>共用配搭
**共用套件**適用於幾何類似 convolutional 連線，但它會使用預先定義的函數，來來源節點值的目的節點值。 因此，共用配搭有沒有 trainable 狀態 （[線寬] 或 [biases）。 共用配搭支援**共用**、 **MapCount**和**粗細**以外的所有 convolutional 屬性。  

一般而言，彙總相鄰的共用單位核心不會重疊。 分散 [d] 等於 KernelShape [d] 中的每個維度，取得的圖層是傳統本機共用層級]，這通常會採用 convolutional 類神經網路中。 每個項目的節點會計算最大值或，其核心來源圖層中的活動的平均值。  

下列範例會示範共用的套件︰ 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   引數套件的數目為 3 （tuple **InputShape** **KernelShape**，與**分散**的長度）。 
-   在來源層級節點的數字是_5 *24* 24 = 2880_。 
-   這是傳統的本機共用圖層，因為**KernelShape**及**分散**等於。 
-   在目的地圖層中的節點的數字是_5 *12* 12 = 1440_。  
    
如需共用的圖層的詳細資訊，請參閱下列文章︰  

-   [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(區段 3.4)
-   [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>回應正規化配搭
**回應正規化**是本機的正規化配置以 Geoffrey Hinton 推出第一次 set al，紙張[ImageNet Classiﬁcation 與深 Convolutional 類神經網路](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)中。 回應正規化用來協助一般化類神經網路中。 當一個 neuron 已經引發更高的啟動層級時，本機回應正規化圖層會隱藏周圍 neurons 啟動層級。 這是使用三個參數 （***α***， ***β***和***k***） 和 convolutional 結構 （或鄰近圖形）。 在目的地層***y***中的每個 neuron 會對應到來源層級 neuron ***x*** 。 啟動層級的***y***由下列公式，其中***f***是 neuron，啟動層級，而***Nx***是核心 （或包含***x***的 neurons 設定），提供由下列 convolutional 結構定義如下︰  

![][1]  

回應正規化配搭支援**共用**、 **MapCount**和**粗細**以外的所有 convolutional 屬性。  
 
-   如果核心包含 neurons 中***x***的相同地圖，正規化配置稱為**相同地圖正規化**。 若要定義相同地圖正規化， **InputShape**中的第一個座標必須值 1。
-   如果核心包含 neurons 中的空間的同一個位置，以***x***，但 neurons 其他地圖，正規化配置稱為**跨地圖正規化**。 此類型的回應正規化實作側面 inhibition 激發以實數 neurons 中, 找到的類型，建立競爭在 neuron 輸出計算不同地圖上顯示較大的啟動層級的表單。 若要定義跨地圖正規化，第一個座標必須及不大於地圖數的整數，其餘的座標必須值 1。  

回應正規化配搭來源節點值，以決定目的地節點的值套用預先定義的函數，因為他們擁有沒有 trainable 狀態 （[線寬] 或 [biases）。   

**通知**︰ 目的地圖層中的節點會對應到 neurons 的中央的核心節點。 例如，如果 KernelShape [d] 為奇數，則_KernelShape [d] / 2_對應到中央核心節點。 中央節點_KernelShape [d]_為偶數，如果是在_KernelShape [d] / 2-1_。 因此，如果**與邊框距離**[d] 是 False，第一個和最後一個_KernelShape [d] / 2_節點目的地圖層中沒有相對應的節點。 若要避免這種情況下，定義為 [**與邊框距離**[為 true，則為 true，...，true]。  

除了稍早所述的四個屬性，回應正規化配搭也支援下列屬性︰  

-   **Alpha**: （必要） 指定對應到上一個公式中的***α***的浮點數值。 
-   **測試版**: （必要） 指定對應到上一個公式中的***β***的浮點數值。 
-   **位移**: （選擇性） 指定對應到上一個公式中的***k***浮點數值。 預設會為 1。  

下列範例會定義的回應正規化集結使用這些屬性︰  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   在 [圖層來源包含五個地圖，以很少維度 12 x 12，為 1440年節點中加總的每個。 
-   **KernelShape**表示這是相同地圖正規化圖層，鄰近 3x3 矩形] 位於何處。 
-   **與邊框距離**的預設值為 False，因此目的地圖層只 10 節點中每個維度。 若要在包含一個節點相對於來源圖層中每個節點的目的地圖層中，新增 [與邊框距離 = [true、 true、 true]。並將 RN1 大小變更為 [5、 12、 12]。  

## <a name="share-declaration"></a>共用宣告 
網路 # 您也可以選擇支援定義多個配搭與共用的權重。 如果其結構相同，可以共用的任何兩個配搭粗細。 下列語法定義配搭與共用線寬]:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

例如，下列的共用宣告指定的圖層的名稱，指出 [線寬] 與 [biases 應該會共用︰  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   輸入的功能被分割成兩個相等的尺寸輸入圖層。 
-   隱藏的圖層然後計算兩個輸入圖層上更高等級的功能。 
-   共用宣告指定_H1_與_H2_必須計算從分別輸入相同的方式。  
 
或者，這可能會以指定兩個不同的共用宣告，如下所示︰  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

只有在圖層包含單一的套件時，您可以使用簡短的表單。 一般而言，共用，就可能只是相同，表示有相同的大小、 相同的 convolutional 幾何，等的相關的結構。  

## <a name="examples-of-net-usage"></a>網路 # 用法的範例
本節將提供一些範例，告訴您可以使用網路 # 新增隱藏的圖層，定義隱藏圖層其他圖層]，與之互動，以及建立 convolutional 網路的方式。   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>定義簡單的自訂類神經網路:"Hello World 」 範例
這個簡單的範例會示範如何建立具有單一隱藏圖層的類神經網路模型。  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

本範例會示範一些基本命令，如下所示︰  

-   第一行定義 （稱為_資料_） 輸入圖層。 當您使用**自動**關鍵字時，類神經網路會自動包含功能的所有資料行中輸入的範例。 
-   第二行建立隱藏圖層。 名稱_H_會指派到有 200 節點的隱藏圖層。 此圖層完全連線到輸入圖層。
-   第三行定義輸出層級] （稱為 「 _O_」），其中包含 10 輸出節點。 如果類神經網路用於分類，則表示每個類別的一個輸出節點。 關鍵字**sigmoid**表示輸出函數會套用至輸出的圖層。   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>定義多個隱藏的圖層︰ 電腦提供了一些範例
下列範例會示範如何定義稍微複雜的類神經網路，使用多個自訂隱藏圖層。  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

此範例說明類神經網路規格語言的數個功能︰  

-   此結構中有兩個輸入圖層]，_像素_和_中繼資料_。
-   在 [圖層_像素為單位_是來源圖層的兩個連接配搭，與目的地圖層， _ByRow_和_ByCol_。
-   _收集_並_結果_的圖層會在多個連線配搭目的地圖層。
-   輸出圖層，_結果_，是在兩個連接配搭; 目的地圖層與第二個層級隱藏 （收集） 作為目的地圖層，輸入圖層 （中繼資料） 作為目的地圖層與另一個。
-   隱藏的圖層， _ByRow_和_ByCol_，使用述詞運算式來指定篩選的連線。 更精確地在中的節點_ByRow_在 [x，y] 已連線至_像素為單位_的第一個索引座標節點的第一個座標，等於節點 x。 同樣地，在 [x，y] 的 _ByCol 節點已連線至_像素為單位_，有一個節點的第二個座標中, 進行共同作業的第二個索引的節點 y。  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>定義 convolutional multiclass 分類的網路︰ 數字辨識範例
下列網路的定義設計用來識別數字，並將其說明的自訂類神經網路某些進階的技巧。  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   此結構中有單一輸入圖層，_圖像_。
-   關鍵字**convolve**表示 「 _Conv1_ _Conv2_圖層 convolutional 的圖層。 每個這些圖層宣告後面迴旋屬性的清單。
-   網路第三個已隱藏圖層， _Hid3_，完全連接連線到第二個隱藏圖層， _Conv2_。
-   輸出圖層，_數字_，第三個隱藏圖層， _Hid3_，才能連線。 關鍵字**所有**指出輸出圖層的完整已連線至_Hid3_。
-   引數數目的迴旋是三個 （tuple **InputShape**、 **KernelShape**、**分散**，及**共用**的長度）。 
-   核心每加權平均的數字是_1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26。或 26 * 50 = 1300_。
-   您可以計算每個隱藏的圖層中的節點，如下所示︰
    -   **NodeCount**\[0] = (5-1) / 1 + 1 = 5。
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5。 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5。 
-   節點的總數，可以使用宣告的圖層，維度導出 [50，5，5]，如下所示︰ _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   **共用**[d] 為 False，因為僅適用於_d = = 0_，核心數目_**MapCount** * * *NodeCount**\[0] = 10* 5 = 50_。 


## <a name="acknowledgements"></a>通知

以自訂類神經網路架構的網路 # 語言 microsoft 已開發 Shon Katzenberger （設計師、 電腦學習） 和 Alexey Kamenev （軟體工程、 Microsoft 參考資料）。 將其用於內部電腦學習專案和應用程式，從圖像偵測到文字分析。 如需詳細資訊，請參閱[類神經網路中 Azure 毫升-網路 # 簡介](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
