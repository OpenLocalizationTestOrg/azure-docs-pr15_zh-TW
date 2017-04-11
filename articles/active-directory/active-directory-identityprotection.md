<properties
    pageTitle="Azure Active Directory 身分識別保護 |Microsoft Azure"
    description="瞭解如何 Azure AD 身分識別保護可讓您利用識別或裝置與身分識別或先前可疑或洩漏已知的裝置的安全性攻擊的能力。"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則的雲端應用程式探索"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Azure Active Directory 身分識別保護 

Azure Active Directory 身分識別保護是 Azure AD Premium P2 版本提供您將風險事件與可能會影響您組織的身分識別的弱點合併檢視的功能。 Microsoft 具有已保護雲端身分識別的十透過和 Azure AD 身分識別保護 Microsoft 會提供這些相同的保護系統供企業的客戶。 身分識別保護運用現有 Azure AD 的異常偵測功能 （可透過 Azure AD 異常活動報表），並會介紹新即時偵測異常的風險事件類型。



##<a name="getting-started"></a>快速入門

大部分的安全性漏洞進行時攻擊存取環境竊取使用者的身分識別。 攻擊變得逐漸有效地使用協力廠商遭侵害等，並使用複雜的網路釣魚攻擊。 一旦攻擊即使最低權限的使用者帳戶的存取權，是相當簡單，才能存取重要的公司資源透過橫向移動。 因此，這是為了保護所有的身分識別與身分識別，危害，主動防止識別被濫用。 

探索洩漏的身分識別並不容易。 所幸，身分識別保護可以幫助︰ 身分識別保護使用自動調整電腦學習演算法和 heuristics 偵測異常行為及風險可能表示身分識別已洩露的事件。
 
使用此資料，身分識別保護會產生報表和通知，可讓您調查這類風險事件，然後拍攝適當補救或降低動作。
 
但 Azure Active Directory 身分識別保護是一個以上的監控和報表工具。 根據風險事件，識別保護計算每個使用者，讓您設定自動保護貴組織的身分識別的風險原則使用者風險層級。  這些風險型原則，除了 Azure Active Directory 和 EM 提供的其他條件存取控制項可以自動封鎖，或提供調整補救密碼重設及多重因素驗證強制執行的動作。  

####<a name="explore-identity-protections-capabilities"></a>探索身分識別保護功能 

**偵測風險事件與風險的帳戶︰**  

- 偵測 6 的風險事件類型，使用電腦的學習及探索的規則 

- 計算使用者風險層級

- 提供自訂以醒目提示的弱點提升整體安全性狀況的建議



**調查風險事件︰**

- 傳送通知風險事件

- 使用內容的相關資訊的調查風險事件

- 提供基本的工作流程來追蹤調查

- 讓您輕鬆存取修復動作，例如密碼重設



**風險條件存取原則︰**

- 若要封鎖簽署增益集，或需要多重因素驗證挑戰來降低風險簽署增益集的原則。

- 若要封鎖或安全高風險的使用者帳戶的原則

- 原則，要求使用者註冊使用多重因素驗證


## <a name="detection-and-risk"></a>偵測與風險

### <a name="risk-events"></a>風險事件

風險事件的身分識別保護歸類為可疑郵件已標幟的事件，表示的身分識別可能已經修改。 風險事件的完整清單，請參閱[Azure Active Directory 身分識別保護偵測到的風險事件的類型](active-directory-identityprotection-risk-events-types.md)。 


### <a name="risk-level"></a>風險層級

風險事件的風險層級會指出 （[高]、 [中] 或 [低） 的風險事件的嚴重性。 風險層級有助於識別保護使用者排列優先順序，降低風險組織必須採取的動作。 風險事件的嚴重性表示身分識別入侵，通常會介紹的雜訊數量與合併的預測訊號強度。 

- **高**︰ 高信賴和嚴重性高風險活動。 下列事件是使用者的身分識別已洩露，及所有使用者帳戶，影響應立即都在於強式標記。

- **中型**︰ 高重要性，但較低的信賴風險事件，或反向操作。 這些事件可能有風險，而應該在於影響所有使用者帳戶。

- **低**︰ 低信賴和低重要性風險事件。 此事件可能不需要立即引起其他動作，但時加上其他風險事件，可能會提供強式的指示受到身分識別。 


![風險層級](./media/active-directory-identityprotection/01.png "風險層級")

 

在 [識別風險事件**即時**，或在後續處理已經發生風險事件了之後將 （離線）。 目前大部分的風險事件，識別保護，計算並顯示身分識別保護 2-4 小時內。 評估時在即時即時風險事件會顯示在身分識別保護主控台 5 到 10 分鐘內。

多個舊版的用戶端目前不支援即時風險事件偵測並防止。 如此一來，無法偵測到或無法即時登增益集這些用戶端。


## <a name="investigation"></a>調查
透過身分識別保護路通常是從開始的身分識別保護儀表板。 

![修復](./media/active-directory-identityprotection/1000.png "修復")

儀表板可讓您存取權︰
 
- 例如**使用者已標幟的風險**、**風險事件**和**弱點**的報表
- 設定您的**安全性原則**，**通知**和**多重因素驗證註冊**的設定例如
 

通常是檢閱活動、 記錄和其他風險事件，以決定是否需要補救或補救步驟與相關的相關資訊的程序的調查的起點，如何身分識別已洩露，並瞭解如何使用識別。

您可以連接至 Azure Active Directory 保護傳送給每個電子郵件[通知](active-directory-identityprotection-notifications.md)您調查的活動。

下列各節提供更多詳細資料和相關法律調查證據的步驟。  



## <a name="what-is-a-user-risk-level"></a>什麼是使用者風險層級？

使用者風險層級會指出 （[高]、 [中] 或 [低） 的使用者的身分識別已洩露的可能性。 它會根據計算使用者的身分識別相關聯的使用者風險事件。 

風險事件的狀態是 [**作用中**] 或 [**已關閉**。 **作用中**的風險事件參與使用者風險計算。 

使用者風險層級的計算是採用下列輸入︰

- 影響使用者的作用中的風險事件
- 這些事件的風險層級 
- 是否已採取任何補救動作 

![使用者風險](./media/active-directory-identityprotection/1001.png "使用者風險")



您可以使用使用者風險層級建立條件存取原則，以封鎖高風險的使用者，使登入，或強迫他們安全地變更他們的密碼。 


## <a name="closing-risk-events-manually"></a>手動關閉風險事件

在大部分情況下，您會需要補救動作，例如安全密碼重設為自動關閉風險事件。 不過，這可能不一律會。  
這是，例如大小寫，時︰

- 作用中的風險事件使用者刪除的郵件
- 法律調查證據顯示了合法的使用者已執行報告的風險事件

參與使用者風險計算**使用中**的風險事件，因為您可能必須手動手動關閉風險事件降低風險層級。  
調查的過程中，您可以選擇採取下列動作，變更風險事件的狀態︰

![動作](./media/active-directory-identityprotection/34.png "動作")

- **解決**-如果調查風險事件之後, 您原本外身分識別保護適當補救動作，而且您認為應風險事件，關閉，事件標示為已解決。 解決事件會將風險事件的狀態設定為 [已關閉和風險事件將不再參與使用者風險。

- **標示為誤判**-在某些情況下，您可能會調查風險事件，並找出不正確已標示為高風險。 您可以協助降低這類次數標示為誤判風險事件。 這將可協助學習未來改善類似的事件分類演算法的電腦。 誤判事件的狀態是**已關閉**，而且他們會不再參與使用者風險。

- **略過**-如果您有不執行任何補救動作，但想要從清單中移除風險事件，而且可以標示風險事件略過]，將會關閉事件狀態。 忽略的事件不會包含使用者風險。 只應該異常的情況下使用此選項。 

- **重新啟動**-手動關閉 （了選擇**解決**、**誤判**、 或**略過**） 的風險事件可以重新啟動，回到 [**作用中**設定事件狀態。 重新啟動的風險事件參與使用者風險層級計算。 風險事件 （例如安全密碼重設 」），透過補救關閉無法再重新啟動。 




**若要開啟 [相關的設定] 對話方塊**︰

1. **Azure AD 身分識別保護**防禦，以在**調查**下的 [**風險事件**。

    ![手動密碼重設](./media/active-directory-identityprotection/1002.png "手動密碼重設")

2. 在 [**風險事件**] 清單中，按一下 [風險。

    ![手動密碼重設](./media/active-directory-identityprotection/1003.png "手動密碼重設")

2. 在風險刀中，以滑鼠右鍵按一下使用者。

    ![手動密碼重設](./media/active-directory-identityprotection/1004.png "手動密碼重設")



### <a name="closing-all-risk-events-for-a-user-manually"></a>手動關閉所有使用者的風險事件

而不是手動關閉個別使用者的風險事件，Azure Active Directory 身分識別保護也提供您要關閉所有的事件，按一下使用者的方法。


![動作](./media/active-directory-identityprotection/2222.png "動作")

當您按一下 [**關閉所有事件**] 時，關閉所有事件，受影響的使用者無法再風險。



## <a name="remediating-user-risk-events"></a>Idfix 修正使用者風險事件

解決方法是安全性身分識別或裝置先前可疑或洩漏已知的動作。 補救動作會的身分識別或裝置還原為安全的狀態，以及解決先前的身分識別或裝置相關聯的風險事件。

若要重新進行使用者風險事件，您可以︰

- 執行 [安全密碼重設為手動補救使用者風險事件 

- 設定使用者風險的安全性原則，以減少或自動修復使用者風險事件

- 重新圖像受感染的裝置  


### <a name="manual-secure-password-reset"></a>手動安全密碼重設

安全密碼重設為許多風險事件，有效補救，而執行時，會自動關閉這些風險事件，並重新計算的使用者風險層級。 您可以使用身分識別保護儀表板進行風險性使用者重設密碼。 

相關的對話方塊提供兩種不同的方法可以重設密碼︰

**重設密碼**-選取**需要重設密碼的使用者**，允許使用者自我復原如果使用者已註冊的多重因素驗證。 在使用者的下一個登入時，使用者會才能成功解決多重因素驗證挑戰，然後強制變更密碼。 這個選項，如果無法使用的使用者帳戶還不是已註冊的多重因素驗證。

**暫時密碼**-立即使現有的密碼，並建立新的臨時密碼，使用者選取**產生暫時密碼**。 備用電子郵件地址的使用者或使用者的管理員，請傳送新的臨時密碼。 因為暫時密碼，使用者就會提示您在登入變更密碼。


![原則](./media/active-directory-identityprotection/1005.png "原則")


**若要開啟 [相關的設定] 對話方塊**︰

1. 在 [ **Azure AD 身分識別保護**刀中，按一下 [**標幟為風險的使用者**]。

    ![手動密碼重設](./media/active-directory-identityprotection/1006.png "手動密碼重設")


2. 從清單中的使用者，選取至少有一個風險事件的使用者。

    ![手動密碼重設](./media/active-directory-identityprotection/1007.png "手動密碼重設")


2. 在 [使用者刀中，按一下 [**重設密碼**]。

    ![手動密碼重設](./media/active-directory-identityprotection/1008.png "手動密碼重設")





## <a name="user-risk-security-policy"></a>使用者風險的安全性原則

使用者風險安全性原則會評估特定使用者的風險層級，並套用補救並減少錯誤的動作，根據預先定義的條件和規則的條件存取原則。


![使用者 ridk 原則](./media/active-directory-identityprotection/1009.png "使用者 ridk 原則")


Azure AD 身分識別保護可協助您管理降低和補救的使用者可以讓您已標幟的風險︰

- 設定使用者和群組原則適用於︰ 

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1010.png "使用者 ridk 原則")


- 設定使用者風險層級臨界值 （低、 中、 或由高） 會觸發原則︰ 

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1011.png "使用者 ridk 原則")


- 設定要觸發原則時，會強制執行的控制項︰

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1012.png "使用者 ridk 原則")


- 切換原則的狀態︰

    ![使用者 ridk 原則](./media/active-directory-identityprotection/403.png "MFA 註冊")


- 檢閱並評估之前啟動它變更的影響︰

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1013.png "使用者 ridk 原則")


選擇 [**高**臨界值減少的次數原則觸發] 和 [最小化對使用者的影響。
不過，其排除**低**和**媒體**使用者標幟為的原則，可能不安全的身分識別或裝置先前可疑或已知會洩漏的風險。

設定原則時,

- 排除可能會產生 false 測 （[安全性分析師中的 [開發人員） 很多使用者

- 排除其中啟用原則不是實用的地區設定中的使用者 （例如沒有技術服務的存取）

- 使用**高**臨界值期間初始原則部署，或如果您必須最小化使用者所看到的挑戰。

- 如果您的組織要求較高的安全性，請使用**低**臨界值。 選取**[低**臨界值會介紹額外的使用者登入的挑戰，但可以增加安全性。

大部分的企業組織的建議使用預設值是設定合用性與安全性之間平衡**中型**臨界值的規則。

如需相關的使用者體驗的概觀，請參閱︰

- [Compromised 帳戶復原流程](active-directory-identityprotection-flows.md#compromised-account-recovery)。  

- [Compromised 帳戶封鎖流量](active-directory-identityprotection-flows.md#compromised-account-blocked)。  


**若要開啟 [相關的設定] 對話方塊**︰

1. 在**Azure AD 身分識別保護**刀，在 [**設定**] 區段中，按一下 [**使用者風險原則**]。

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1009.png "使用者 ridk 原則")






## <a name="mitigating-user-risk-events"></a>降低使用者風險事件
管理員可以在 [登入根據風險層級的封鎖使用者設定使用者風險的安全性原則。 

封鎖登入︰
 
- 防止產生的受影響使用者的新使用者風險事件

- 可讓系統管理員，若要手動重新進行會影響使用者的身分識別的風險事件，並將它還原為安全的狀態



## <a name="what-is-a-sign-in-risk-level"></a>什麼是登入風險層級？

登入風險層級會指出 （[高]、 [中] 或 [低） 的特定登入的其他人正在嘗試驗證使用者的身分識別的可能性。 登入風險層級會評估一次登入的並將風險事件和指標中偵測到即時的特定的登入。 

## <a name="mitigating-sign-in-risk-events"></a>減輕風險登入事件 
降低是以限制攻擊可利用識別或裝置不安全的狀態回復的身分識別或裝置的動作。 降低無法解決與身分識別或裝置相關聯的上一個登入風險事件。

您可以使用 Azure AD 身分識別保護條件存取自動降低登入風險事件。 使用這些原則，請考慮風險層級的使用者的登入封鎖風險性簽署增益集，或要求使用者執行多因素驗證。 這些動作可能會防止攻擊利用造成損害，竊取身分識別，並且可能會提供您一些時間以安全身分識別。 


## <a name="sign-in-risk-security-policy"></a>登入風險的安全性原則

登入風險原則會評估特定的登入的風險，適用於減輕根據預先定義的條件和規則的條件的存取原則。

![登入風險原則](./media/active-directory-identityprotection/1014.png "登入風險原則")


Azure AD 身分識別保護可協助您管理風險登增益集的降低讓您可以︰

- 設定使用者和群組原則適用於︰ 

    ![登入風險原則](./media/active-directory-identityprotection/1015.png "登入風險原則")


- 設定登入風險層級臨界值 （低、 中、 或由高） 會觸發原則︰ 

    ![登入風險原則](./media/active-directory-identityprotection/1016.png "登入風險原則")


- 設定要觸發原則時，會強制執行的控制項︰  

    ![登入風險原則](./media/active-directory-identityprotection/1017.png "登入風險原則")
    

- 切換原則的狀態︰

    ![MFA 註冊](./media/active-directory-identityprotection/403.png "MFA 註冊")

- 檢閱並評估之前啟動它變更的影響︰ 

    ![登入風險原則](./media/active-directory-identityprotection/1018.png "登入風險原則")


### <a name="what-you-need-to-know"></a>您需要知道什麼

您可以設定多重因素驗證的登入風險安全性原則︰

![登入風險原則](./media/active-directory-identityprotection/1017.png "登入風險原則")

不過，基於安全性理由，這項設定只適用於已註冊的多重因素驗證的使用者。 如果還沒有註冊多重因素驗證的使用者滿足多重因素驗證條件，使用者會被封鎖。 

最佳作法，如果您想要具備多因素驗證風險性簽署增益集]，您應該︰

1. 啟用受影響使用者的[多重因素驗證註冊原則](#multi-factor-authentication-registration-policy)。
2. 若要執行 MFA 登錄非風險性工作階段要求受影響的使用者登入

完成下列步驟可確保該多重因素驗證時需要高風險的登入。 


### <a name="best-practices"></a>最佳作法

 
選擇 [**高**臨界值減少的次數原則觸發] 和 [最小化對使用者的影響。  
 
不過，其排除**低**和**媒體**簽署增益集標幟為的原則，可能不會封鎖利用識別攻擊的風險。 

設定原則時, 

- 排除使用者，請不要 / 不能有多重因素驗證

- 排除其中啟用原則不是實用的地區設定中的使用者 （例如沒有技術服務的存取）

- 排除可能會產生 false 測 （[安全性分析師中的 [開發人員） 很多使用者

- 使用**高**臨界值期間初始原則部署，或如果您必須最小化使用者所看到的挑戰。

- 如果您的組織要求較高的安全性，請使用**低**臨界值。 選取**[低**臨界值會介紹額外的使用者登入的挑戰，但可以增加安全性。

大部分的企業組織的建議使用預設值是設定合用性與安全性之間平衡**中型**臨界值的規則。

 
登入風險原則是︰

- 套用到所有的瀏覽器流量與使用的現代化驗證登增益集。
- 未套用至應用程式使用舊的安全性通訊協定停用在同盟 IDP，例如 ADFS Ws-trust 端點。

身分識別保護主控台中的 [**風險事件**] 頁面會列出所有事件︰

- 若要套用此原則
- 您可以檢閱活動，並決定是否動作是否適當 

如需相關的使用者體驗的概觀，請參閱︰

- [高風險的登入復原](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [高風險登入封鎖](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [多重因素驗證註冊期間高風險的登入](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**若要開啟 [相關的設定] 對話方塊**︰

1. 在**Azure AD 身分識別保護**刀中，在 [**設定**] 區段中，按一下 [**登入風險原則**]。

    ![使用者 ridk 原則](./media/active-directory-identityprotection/1014.png "使用者 ridk 原則")





## <a name="multi-factor-authentication-registration-policy"></a>多重因素驗證註冊原則

Azure 多重因素驗證是您的身分驗證的方法，需要使用的不只是使用者名稱和密碼。 它會提供第二個使用者登增益集及交易的安全性層級。  
我們建議您的使用者登增益集需要 Azure 多重因素驗證，因為它︰

- 提供強式驗證於某範圍內的簡單的驗證選項

- 播放準備保護與復原從帳戶折衷組織重要的角色

![使用者 ridk 原則](./media/active-directory-identityprotection/1019.png "使用者 ridk 原則")



如需詳細資訊，請參閱[什麼是 Azure 多重因素驗證？](../multi-factor-authentication/multi-factor-authentication.md)


Azure AD 身分識別保護可協助您管理多重因素驗證註冊相簿出設定原則，可讓您︰ 



- 設定使用者和群組原則適用於︰ 

    ![MFA 原則](./media/active-directory-identityprotection/1020.png "MFA 原則")



- 設定要觸發原則時，會強制執行的控制項::  

    ![MFA 原則](./media/active-directory-identityprotection/1021.png "MFA 原則")


- 切換原則的狀態︰

    ![MFA 原則](./media/active-directory-identityprotection/403.png "MFA 原則")

- 檢視目前的註冊狀態︰ 

    ![MFA 原則](./media/active-directory-identityprotection/1022.png "MFA 原則")


如需相關的使用者體驗的概觀，請參閱︰

- [多重因素驗證註冊流程](active-directory-identityprotection-flows.md#multi-factor-authentication-registration)。  

- [多重因素驗證註冊期間高風險的登入](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)。  





**若要開啟 [相關的設定] 對話方塊**︰

1. 在**Azure AD 身分識別保護**刀中，在 [**設定**] 區段中，按一下 [**多重因素驗證註冊**]。

    ![MFA 原則](./media/active-directory-identityprotection/1019.png "MFA 原則")





## <a name="next-steps"></a>後續步驟

 - [頻道 9: Azure AD 與身分識別放映︰ 身分識別保護預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [偵測到的 Azure Active Directory 身分識別保護風險事件的類型](active-directory-identityprotection-risk-events-types.md)
 - [偵測到的 Azure Active Directory 身分識別保護弱點](active-directory-identityprotection-vulnerabilities.md)
 - [Azure Active Directory 身分識別保護通知](active-directory-identityprotection-notifications.md)
 - [Azure Active Directory 身分識別保護 playbook](active-directory-identityprotection-playbook.md)
 - [Azure Active Directory 身分識別保護詞彙](active-directory-identityprotection-glossary.md)

 - [Azure AD 身分識別保護的登入體驗](active-directory-identityprotection-flows.md)

 - [啟用 Azure Active Directory 身分識別保護](active-directory-identityprotection-enable.md)
 - [Azure Active Directory 身分識別保護-如何解除封鎖使用者](active-directory-identityprotection-unblock-howto.md)

 - [開始使用 Azure Active Directory 身分識別保護與 Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


