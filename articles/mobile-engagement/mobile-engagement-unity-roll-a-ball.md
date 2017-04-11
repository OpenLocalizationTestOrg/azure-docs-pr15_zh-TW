<properties
    pageTitle="凝聚力相簿的球教學課程"
    description="建立傳統的凝聚力就緒球遊戲這是所有行動互動凝聚力教學課程的必要條件的步驟"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>建立凝聚力就緒球遊戲

本教學課程中會引導的主要步驟也經過小幅修改[凝聚力相簿的球教學課程](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)的空間。 此範例遊戲包含控制應用程式使用者球面 「 播放 」 物件，以及遊戲的目標是 「 收集' collectible 物件互相衝突這些 collectible 物件的播放程式物件。 這是假設熟悉凝聚力編輯器環境。 如果您遇到任何問題您應該參考完整的教學課程。 

### <a name="setting-up-the-game"></a>設定遊戲
下列步驟會從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. 開啟**凝聚力編輯器**，然後按一下 [**新增**]。 
    
    ![][51] 
    
2. 提供**專案名稱** & **位置**，選取**3D** ，然後按一下 [**建立專案**。
    
    ![][52]

3. 儲存剛建立新專案的一部分就跟名稱**MiniGame**中新的預設場景**\_場景****資產**資料夾下︰
    
    ![][53]

4. 建立**3D 物件]-> [平面**為遊戲欄位，並重新命名平面物件為**基本**

    ![][1]

5. 重設此**基本**物件的轉換元件，使其位於原點。 

    ![][3]

6. **基本**物件取消核取**Gizmos**功能表**顯示格線**。

    ![][4]

7. 更新的**基本**物件的**比例**元件 [X = 2，Y = 1，Z = 2]。 

    ![][5]

8. 加入新**3D 物件]-> [球體**的專案，並重新命名球體物件為**播放程式**。 

    ![][6]

9. 選取 [**播放程式**物件，然後按一下 [**重設轉換**至平面物件類似。 

10. 更新**轉換]-> [位置]-> [Y 座標**以 0.5 來播放程式 Y 元件。  

    ![][7]

11. 建立新資料夾專案中名為**材料**我們將在其中建立以色彩播放程式的資料。 

12. 建立新的**材料**此資料夾中名為**背景**。 

    ![][8]

13. 藉由更新其**屑**屬性更新材質的色彩。 您可以選取 [0,32,64] 的 RGB 值。 

    ![][9]

14. 將此材料拖曳到 [場景檢視套用色彩至**基本**的物件。 

    ![][10]

17. 最後更新**轉換]-> [旋轉]-> [Y**到 60 上清楚的方向燈物件。 

    ![][12]

### <a name="moving-the-player"></a>移動播放程式
下列步驟會從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. 新增**RigidBody**元件至**播放程式**的物件。 

    ![][13]

2. 建立新的資料夾，稱為專案中的**指令碼**。 

3. 按一下 [**新增元件]-> [新的指令碼 C# 指令碼]-> [**。 將其命名為 [ **PlayerController**，然後按一下 [**建立和新增**。 這會建立，並將指令碼附加至播放程式的物件。  

    ![][14]

5. 在專案中的**指令碼**資料夾下移動這個指令碼。 

6. 開啟您最愛的指令碼編輯器中編輯的指令碼，更新指令碼下列程式碼並將其儲存。 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. 請注意，上述的指令碼使用**速度**屬性。 在凝聚力編輯器中，更新為 10 的 [速度] 屬性。  

    ![][15]

9. 按下**播放**凝聚力編輯器中。 現在您應該可以控制球使用鍵盤，它應該旋轉及移動。 

### <a name="moving-the-camera"></a>移動攝影機
下列步驟會從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141)和繫結至**播放程式**的物件**主要相機**。 

1. 更新**Transform.Position**是 X = 0，Y = 10.5，Z =-10。  
2. 更新**Transform.Rotation**是 X = 45，Y = 0，Z = 0。  

    ![][16]

2. 新增稱為**CameraController** **MainCamera**至新的指令碼，然後將其移到資料夾底下的指令碼]。 

    ![][17]

3. 開啟指令碼進行編輯，並在其中新增下列程式碼︰

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. 在凝聚力環境-請將播放程式變數拖曳至主要相機物件的播放程式位置，使兩個與另一個相關聯。 

    ![][18]

6. 現在當您按凝聚力編輯器] 中的 [播放且播放程式球物件的旋轉您會看到追蹤移動的相機鏡頭。  

### <a name="setting-up-the-play-area"></a>設定 [播放] 區域
下列步驟是從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141)。 我們會建立周圍地上，以便播放程式球物件不會關閉 [播放] 區域，其移動放在牆面。 

1. 按一下 [**建立-> [建立空白的遊戲物件]-> [**並將其命名為 [**牆**

    ![][19]

2. 在此牆物件-建立**3D 物件]-> [Cube**新並將其命名 「 西部牆 」。 

    ![][20]

3. 更新此西部牆物件的 [**轉換]-> [位置**] 和 [**轉換]-> [縮放比例**。 

    ![][21]

4. 複製西部牆建立**東亞牆面上**的更新的轉換位置和小數位數。 

    ![][22]

5. 複製東亞牆建立**北美牆**利用更新的轉換位置和小數位數。 

    ![][23]

6. 複製北美牆並建立**南部牆**利用更新的轉換位置和小數位數。 

    ![][24]

### <a name="creating-collectible-objects"></a>建立 Collectible 物件
下列步驟是從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141)。 我們會建立一些美觀的外觀物件形成 collectible 物件的播放程式球物件需要 「 收集' 與其互相衝突的設定。 

1. 建立新的**3D Cube 物件**，並將其命名為 [收取。 

2. 調整 [**轉換]-> [旋轉** & 收取物件的 [**轉換]-> [縮放比例**。 

    ![][25]

3. 建立並附加至收取物件稱為**旋轉****新 C# 指令碼**。 請務必將指令碼，指令碼資料夾下。 

    ![][26]

4. 開啟此指令碼進行編輯，並更新來進行下列動作︰ 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. 現在點擊其座標軸上旋轉凝聚力編輯器和收取物件放映中的播放模式。

6. 建立新的資料夾，稱為**Prefabs** 

    ![][27]

7. 拖曳**收取**物件，並將其放在 [Prefabs] 資料夾。

    ![][28]

8. 建立新的**空白遊戲物件**稱為**Pickups**。 重設為原始的位置，然後拖曳收取物件在此遊戲的物件。  

    ![][29]

9. 複製**收取**物件，它在**播放程式**物件周圍的**基本**物件上適當地更新**Transform.Position 的 X 與 Z**值。 

    ![][30]

10. 建立**新的材料**稱為**收取**並更新為紅色色彩，藉由更新**屑屬性**類似我們更新基本物件。 

    ![][31]

11. 套用到所有 4 的收取物件的材質。

    ![][32]

### <a name="collecting-the-pickup-objects"></a>收集收取的物件
下列步驟是從[凝聚力教學課程](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141)。 我們會更新播放程式，使其能 '收集' 收取的物件與其互相衝突。 

1. 開啟附加至編輯的播放程式物件的**PlayerController**指令碼，並將其更新為下列︰  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. 建立新**標籤**稱為**挑選設定**（它必須符合功能指令碼）  

    ![][33]
    
    ![][34]

3. 您可以套用此**標籤**至 Prefab 收取的物件。 

    ![][35]

4. 啟用**IsTrigger** Prefab 物件] 核取方塊。

    ![][36]

5. 新增收取 Prefab 物件嚴謹的本文。 效能最佳化我們將會更新，我們使用靜態 collider 至動態 collider。 

    ![][37]
  
6. 最後，請核取 prefab 物件**IsKinematic**屬性]。 

    ![][38]

7. 點擊**播放**凝聚力編輯器，而且您會無法播放**就緒球**遊戲移動方向輸入使用您的鍵盤按鍵的播放程式物件。 

### <a name="updating-the-game-for-mobile-play"></a>更新行動裝置的播放的遊戲
上述各節歸納出凝聚力從基本教學課程。 現在，我們要修改的遊戲，使其行動裝置易記。 請注意，我們會使用鍵盤輸入遊戲到目前為止，以進行測試。 現在我們會加以修改，以便我們可以控制播放程式，也就是使用電話的影片 使用 Accelerometer 做為輸入。 

開啟**PlayerController**指令碼進行編輯，並更新**FixedUpdate**来使用的方法從 accelerometer 移動移動播放程式物件。 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

本教學課程結束凝聚力基本遊戲所建立，您可以將此部署遊戲您所選擇的裝置上。 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
