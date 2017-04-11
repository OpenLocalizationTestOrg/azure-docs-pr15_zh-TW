<properties
    pageTitle="疑難排解︰ Azure AD 密碼管理 |Microsoft Azure"
    description="Azure AD 密碼管理，包括重設、 變更、 回寫註冊，一般疑難排解步驟，協助尋找要包含的時機的資訊。"
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>如何疑難排解密碼管理

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

如果您有的密碼管理問題，我們可以協助。 幾個簡單的疑難排解步驟的您可以閱讀下方以疑難排解您的部署，就可以解決大部分您可能會遇到的問題︰

* [**您需要協助時要包含的資訊**](#information-to-include-when-you-need-help)
* [**Azure 管理入口網站中的密碼管理設定問題**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Azure 管理入口網站中的密碼安全性群組報告問題**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**問題密碼重設註冊入口網站**](#troubleshoot-the-password-reset-registration-portal)
* [**問題密碼重設入口網站**](#troubleshoot-the-password-reset-portal)
* [**密碼回寫的問題**](#troubleshoot-password-writeback)
  - [密碼回寫的事件記錄檔錯誤碼](#password-writeback-event-log-error-codes)
  - [密碼回寫連線問題](#troubleshoot-password-writeback-connectivity)

如果您已嘗試下列疑難排解步驟，仍遇到問題，您可以將問題張貼在[Azure AD 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)，或者連絡支援人員，我們會看看您的問題，我們可以如下。

## <a name="information-to-include-when-you-need-help"></a>您需要協助時要包含的資訊

如果您無法解決您的問題與下提供的指導方針，您也可以連絡我們支援工程師。 當您與其連絡時，建議包含下列資訊︰

 - **錯誤的一般描述**– 哪些正確的錯誤訊息並未使用者，請參閱嗎？  如果有任何錯誤訊息，說明未預期的行為，您會在詳細資料。
 - **頁面**-哪些頁面您是否曾經上時所看到的錯誤 （包含 URL）？
 - **日期 / 時間 / 時區**– 是精確的日期和時間，您所看到的錯誤 （包含時區）？
 - **支援的程式碼**– 所產生的使用者所看到的錯誤 （以找到這重現錯誤，然後在畫面底部的 [支援的程式碼] 連結，然後按一下傳送支援工程師結果 GUID） 時支援程式碼。
   - 如果您是不支援代碼底部的頁面上，按下 F12 和搜尋 SID 和德，然後傳送支援工程師兩個結果。

    ![][001]

 - **使用者識別碼**– 為何 （例如看到錯誤的使用者識別碼user@contoso.com)?
 - **使用者的相關資訊**– 是同盟的使用者、 密碼雜湊同步處理，只存在於雲端嗎？  使用者沒有指派 AAD 進階版或 AAD 基本授權嗎？
 - **應用程式的事件記錄檔**– 如果您使用密碼回寫和錯誤已在內部部署基礎結構，請設定您的應用程式事件記錄檔，從 Azure AD Connect 伺服器複本 zip 並傳送以及您的要求。

包括這項資訊可協助我們儘快解決的問題。


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>疑難排解 Azure 管理入口網站中的密碼重設設定
如果設定密碼重設，您就會發生錯誤，您可能可以解決問題，下列疑難排解步驟︰

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤的大小寫</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒有看到 [<strong>使用者密碼重設原則</strong>] 區段底下 Azure 管理入口網站中的 [<strong>設定</strong>] 索引標籤</p>
            </td>
            <td>
              <p>Azure 管理入口網站中的 [<strong>設定</strong>] 索引標籤上看不到 [<strong>使用者密碼重設原則</strong>] 區段。</p>
            </td>
            <td>
              <p>如果您沒有 AAD 進階版或 AAD 基本的授權指派給管理員執行這項作業，會發生這項目。 </p>
              <p>若要修正此問題，AAD 進階版或 AAD 基本的授權指派給系統帳戶問題，瀏覽至 [<strong>授權</strong>] 索引標籤，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>看不到任何的設定<strong>使用者密碼重設原則</strong>] 區段下的選項說明的文件。</p>
            </td>
            <td>
              <p>[<strong>使用者密碼重設原則</strong>] 區段會出現，但其下方出現的唯一旗標<strong>啟用密碼重設使用者</strong>的標幟。</p>
            </td>
            <td>
              <p>UI 部分會顯示當您切換 [<strong>啟用密碼重設使用者</strong>的標幟為<strong>[是]。</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>看不到特定的設定選項。</p>
            </td>
            <td>
              <p>例如，我時不會看到<strong>的天數，使用者必須確認其連絡人資料</strong>選項捲動<strong>使用者密碼重設原則</strong>] 區段 （或其他範例相同的問題）。</p>
            </td>
            <td>
              <p>UI 的許多項目會隱藏起來，直到所需。 嘗試啟用在頁面上的所有選項，如果您想要查看。</p>
              <p>所有的控制項，可在您的詳細資訊，請參閱<a href="active-directory-passwords-customize.md#password-management-behavior">密碼管理行為</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒有看到 [<strong>撰寫回密碼內部部署</strong>的設定選項</p>
            </td>
            <td>
              <p><strong>撰寫回密碼內部部署</strong>選項並不會看到 Azure 管理入口網站中的 [<strong>設定</strong>] 索引標籤</p>
            </td>
            <td>
              <p>如果您已下載 Azure AD Connect 並設定密碼回寫，才看得到這個選項。 當您這麼做時，該選項出現，並可讓您以啟用或停用回寫雲端。</p>
              <p>如需如何進行此動作的詳細資訊，請參閱<a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Azure AD Connect 中啟用密碼回寫</a>。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>疑難排解 Azure 管理入口網站中的密碼管理報告
如果使用密碼管理報表時，您就會發生錯誤，您可能可以解決問題，下列疑難排解步驟︰

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤的大小寫</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我看不到任何密碼管理報告</p>
            </td>
            <td>
              <p><strong>密碼重設活動</strong>和<strong>密碼重設登錄活動</strong>報表不會顯示在 [<strong>活動記錄</strong>中的報表 [<strong>報表</strong>] 索引標籤底下的。</p>
            </td>
            <td>
              <p>如果您沒有 AAD 進階版或 AAD 基本的授權指派給管理員執行這項作業，會發生這項目。 </p>
              <p>若要修正此問題，AAD 進階版或 AAD 基本的授權指派給系統帳戶問題，瀏覽至 [<strong>授權</strong>] 索引標籤，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者登錄顯示多個時間</p>
            </td>
            <td>
              <p>當使用者在進行註冊備用電子郵件、 行動電話及安全性問題會每個顯示為個別的行，而不是一行。</p>
            </td>
            <td>
              <p>目前，當使用者註冊，我們無法假設會註冊出現在 [註冊] 頁面上的所有項目。 如此一來，我們目前記錄的每個個別的一段已註冊為個別事件的資料。</p>
              <p>如果您想要彙總此資料，您可以下載報表，並開啟 excel 有更多的彈性的樞紐分析表中的資料。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>疑難排解密碼重設註冊入口網站
如果註冊的密碼重設使用者時，您就會發生錯誤，您可能可以解決問題，下列疑難排解步驟︰

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤的大小寫</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設密碼並未啟用目錄</p>
            </td>
            <td>
              <p>您的系統管理員具有未啟用您要使用此功能。</p>
            </td>
            <td>
              <p>切換<strong>啟用密碼重設使用者</strong>的標幟為 [<strong>是]</strong> ，然後按下 [Azure 管理入口網站目錄設定] 索引標籤中的 [<strong>儲存</strong>。 您必須有 Azure AD 進階版或基本的授權指派給管理員執行此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>沒有 AAD 進階版或 AAD 基本授權指派給使用者。</p>
            </td>
            <td>
              <p>您的系統管理員具有未啟用您要使用此功能。</p>
            </td>
            <td>
              <p>Azure AD 進階版或 Azure AD 基本將授權指派給使用者在 Azure 管理入口網站中的 [<strong>授權</strong>] 索引標籤。 您必須有 Azure AD 進階版或基本的授權指派給管理員執行此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>處理要求時發生錯誤</p>
            </td>
            <td>
              <p>使用者會看到的錯誤︰</p>
              <p>

              </p>
              <p>處理要求時發生錯誤 </p>
              <p>嘗試重設密碼。</p>
            </td>
            <td>
              <p>這可能會因許多問題，但此錯誤通常因任一的服務中斷或組態問題無法解決。 </p>
              <p>如果您看到此錯誤，它會影響您的企業，請連絡支援人員，我們會協助您 ASAP。 請參閱若要查看您應提供支援工程師，協助您快速解決方案以<a href="#information-to-include-when-you-need-help">包含當您需要協助的資訊</a>。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>疑難排解密碼重設入口網站
如果發生錯誤時重設使用者的密碼，您可能可以解決問題，下列疑難排解步驟︰

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤的大小寫</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設密碼並未啟用目錄</p>
            </td>
            <td>
              <p>重設密碼並未啟用您的帳戶</p>
              <p>很抱歉，但您的系統管理員尚未設定此服務搭配您的帳戶。 </p>
              <p>

              </p>
              <p>如果您想要我們可以連絡貴組織的系統管理員為您重設您的密碼。</p>
            </td>
            <td>
              <p>切換<strong>啟用密碼重設使用者</strong>的標幟為 [<strong>是]</strong> ，然後按下 [Azure 管理入口網站目錄設定] 索引標籤中的 [<strong>儲存</strong>。 您必須有 Azure AD 進階版或基本的授權指派給管理員執行此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>沒有 AAD 進階版或 AAD 基本授權指派給使用者。</p>
            </td>
            <td>
              <p>雖然我們無法自動重設非系統管理員帳戶的密碼，我們也可以連絡貴組織的系統管理員為您。</p>
            </td>
            <td>
              <p>Azure AD 進階版或 Azure AD 基本將授權指派給使用者在 Azure 管理入口網站中的 [<strong>授權</strong>] 索引標籤。 您必須有 Azure AD 進階版或基本的授權指派給管理員執行此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設密碼，啟用目錄，但使用者遺失或格式不正確的驗證資訊</p>
            </td>
            <td>
              <p>重設密碼並未啟用您的帳戶</p>
              <p>很抱歉，但您的系統管理員尚未設定此服務搭配您的帳戶。 </p>
              <p>

              </p>
              <p>如果您想要我們可以連絡貴組織的系統管理員為您重設您的密碼。</p>
            </td>
            <td>
              <p>請確定該使用者有正確連絡人資料之前目錄中的檔案。 如需如何設定目錄中的驗證資訊，讓使用者不會看到此錯誤的資訊，請參閱<a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">什麼資料由重設密碼</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>已啟用目錄重設密碼，但是使用者只有一段連絡人資料檔案原則設定為需要兩個步驟時</p>
            </td>
            <td>
              <p>重設密碼並未啟用您的帳戶</p>
              <p>很抱歉，但您的系統管理員尚未設定此服務搭配您的帳戶。 </p>
              <p>

              </p>
              <p>如果您想要我們可以連絡貴組織的系統管理員為您重設您的密碼。</p>
            </td>
            <td>
              <p>請確定該使用者有至少兩個經妥善設定的連絡方法 （例如行動電話和辦公室電話），再繼續執行。 如需如何設定目錄中的驗證資訊，讓使用者不會看到此錯誤的資訊，請參閱<a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">什麼資料由重設密碼</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設密碼，啟用目錄及使用者已正確設定，但使用者將無法連絡 </p>
            </td>
            <td>
              <p>糟糕 ！  我們可以時發生意外的錯誤與您連絡。</p>
            </td>
            <td>
              <p>這可能是暫時服務錯誤的結果或設定錯誤我們偵測不到正確的連絡人資料。 如果使用者等待 10 秒內，再試一次，而且會出現 「 連絡您的系統管理員 」 連結。 再按一次嘗試重新會分派通話，而按一下 「 連絡您的系統管理員 」 會表單電子郵件傳送給使用者、 密碼或全域管理員 （的優先順序） 要求密碼重設為該使用者帳戶上執行時。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者沒有收到密碼重設 SMS 或電話</p>
            </td>
            <td>
              <p>使用者按一下 」 文字我 」 或 「 撥號給我]，然後永不接收任何項目。</p>
            </td>
            <td>
              <p>這可能是在目錄中的格式不正確的電話號碼的結果。 請確定的電話號碼格式 [+ ccc xxxyyyzzzzXeeee]。 若要深入瞭解設定格式化的電話用於密碼重設數字會看到<a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">什麼資料由重設密碼</a>。</p>
              <p>如果您需要副檔名路由傳送至有問題的使用者，請注意該密碼重設不支援延伸，即使您指定一個 （他們移除之前會分派呼叫） 的目錄。 請嘗試使用沒有副檔名，數字或副檔名為整合在您的 PBX 電話號碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者沒有收到電子郵件-重設密碼</p>
            </td>
            <td>
              <p>使用者按一下 「 以電子郵件傳送我 」，然後永遠不會收到任何項目。</p>
            </td>
            <td>
              <p>此問題的常見原因是垃圾郵件篩選器的拒絕訊息。 請檢查您的垃圾郵件、 電子郵件的 [垃圾郵件] 或 [已刪除的郵件] 資料夾。</p>
              <p>確保您要檢查的正確的電子郵件訊息...許多人非常類似的電子郵件地址，且會檢查錯誤訊息的收件匣。 如果這些選項都沒有作用，也可能是在目錄中的電子郵件地址不正確，請確定電子郵件地址正確無誤，然後再試一次存回。 若要深入瞭解設定格式化的電子郵件地址重設密碼搭配使用，請參閱<a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">什麼資料由密碼重設</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>有設定密碼重設原則，但未套用時的管理員帳戶使用重設密碼，該原則</p>
            </td>
            <td>
              <p>重設密碼的系統管理員帳戶，請參閱重設密碼、 電子郵件及行動電話，無論哪些原則設定<strong>使用者密碼重設原則</strong>] 區段的 [<strong>設定</strong>] 索引標籤下功能相同的選項。</p>
            </td>
            <td>
              <p>設定<strong>使用者密碼重設原則</strong>] 區段的 [<strong>設定</strong>] 索引標籤下的選項只適用於您的組織中的使用者。</p>
              <p>Microsoft 管理和控制項管理員重設密碼原則以確保最高層級的安全性</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>防止嘗試太多次在日期中重設密碼的使用者</p>
            </td>
            <td>
              <p>使用者會看到錯誤說明︰</p>
              <p>

              </p>
              <p>請使用另一個選項。</p>
              <p>您已嘗試太多次中最後一個 1 小時驗證您的帳戶。 基於安全性理由，您必須等候 24 小時前再試一次。 </p>
              <p>如果您想要我們可以連絡貴組織的系統管理員為您重設您的密碼。</p>
            </td>
            <td>
              <p>我們嘗試重設密碼太多次在一段時間實作封鎖使用者的自動節流機制。 發生這種情況時︰</p>
              <ol class="ordered">
                <li>
使用者想要驗證的電話號碼 5 一小時的時間。<br\><br\></li>
                <li>
使用者嘗試使用安全性問題門 5 乘以一小時內。<br\><br\></li>
                <li>
使用者想要重設 5 乘以一小時內的相同的使用者帳戶的密碼。<br\><br\></li>
              </ol>
              <p>若要修正此問題，指示使用者等候 24 小時一次嘗試之後，使用者就無法重設密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>驗證他或她的電話號碼時，使用者會看到錯誤</p>
            </td>
            <td>
              <p>嘗試驗證使用的驗證方法電話，使用者會看到錯誤說明︰</p>
              <p>

              </p>
              <p>指定正確的電話號碼。</p>
            </td>
            <td>
              <p>輸入電話號碼不符合檔案的電話號碼時，會發生此錯誤。</p>
              <p>請確定使用者輸入完整的電話號碼，當您嘗試使用密碼重設電話型方法時，包括區域與國家/地區碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>處理要求時發生錯誤</p>
            </td>
            <td>
              <p>使用者會看到的錯誤︰</p>
              <p>

              </p>
              <p>處理要求時發生錯誤 </p>
              <p>嘗試重設密碼。</p>
            </td>
            <td>
              <p>這可能會因許多問題，但此錯誤通常因任一的服務中斷或組態問題無法解決。 </p>
              <p>如果您看到此錯誤，它會影響您的企業，請連絡支援人員，我們會協助您 ASAP。 請參閱若要查看您應提供支援工程師，協助您快速解決方案以<a href="#information-to-include-when-you-need-help">包含當您需要協助的資訊</a>。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>疑難排解密碼回寫
如果發生錯誤啟用、 停用，或使用密碼回寫時，您可能可以解決問題，下列疑難排解步驟︰

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤的大小寫</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>一般登入和啟動失敗</p>
            </td>
            <td>
              <p>內部部署 Azure AD Connect 電腦的應用程式的事件記錄檔中的 6800 發生錯誤無法啟動密碼重設服務。</p>
              <p>

              </p>
              <p>登入，同盟或密碼雜湊之後無法同步處理的使用者重設密碼。</p>
            </td>
            <td>
              <p>啟用密碼回寫時，同步處理引擎會向雲端登入服務呼叫回寫文件庫執行設定 （登入）。 在登入，或啟動 WCF 端點密碼回寫時所遇到的任何錯誤會導致 Azure AD Connect 電腦的事件記錄檔中的事件記錄中的錯誤。</p>
              <p>期間重新啟動 ADSync 服務，如果已設定為回寫，WCF 端點會啟動。 不過，如果端點的啟動會失敗，我們會直接 6800 事件記錄，讓啟動同步處理服務。 目前狀態的此事件表示端點尚未啟動設定密碼回寫。 PasswordResetService 元件會指出為什麼端點無法啟動產生的事件記錄項目以及此事件 (6800) 的事件記錄檔詳細資料。 檢閱這些事件記錄檔錯誤，並嘗試重新啟動 Azure AD Connect，如果密碼回寫仍然無法運作。 如果問題持續發生，請嘗試停用或重新啟用密碼回寫。</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>當使用者嘗試重設密碼或解除鎖定帳戶啟用的密碼回寫時，操作會失敗。 此外，請參閱 Azure AD Connect 包含的事件記錄檔中的事件: 「 同步處理引擎會傳回錯誤 hr = 800700CE，郵件 = 檔名或副檔名太長 」 發生解除鎖定作業之後。
                            </p>
            </td>
            <td>
              <p>如果您已從舊版 Azure AD Connect 或 DirSync 升級，會發生這項目。 升級至舊版 Azure AD Connect 設定 254 字元帳戶的密碼 Azure AD 管理代理程式 （較新版本就會設定 127 字元長度密碼）。 適用於 AD 連接器匯入及匯出作業的這類長的密碼，但不是受解除鎖定作業。
                            </p>
            </td>
            <td>
              <p>[尋找 Active Directory 帳戶](active-directory-aadconnect-accounts-permissions.md#active-directory-account)Azure AD Connect 和重設密碼，以包含不超過 127 個字元。 從 [開始] 功能表，然後開啟**同步處理服務**。 瀏覽至**連接器**並尋找**Active Directory 連接器**。 選取它，然後按一下 [**屬性**]。 瀏覽至 [**認證**] 頁面，然後輸入新密碼。 選取**[確定**] 關閉頁面]。
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Azure AD Connect 安裝期間設定回寫的錯誤。</p>
            </td>
            <td>
              <p>Azure AD Connect 安裝程序的最後一個步驟，在您會看到錯誤指出無法設定密碼回寫。</p>
              <p>

              </p>
              <p>Azure AD 連線應用程式的事件記錄檔中包含文字的錯誤 32009 」 錯誤快速驗證 token 」。</p>
            </td>
            <td>
              <p>在下列兩種情況下，會發生此錯誤︰</p>
              <ul>
                <li class="unordered">
您已經指定指定 Azure AD Connect 安裝程序的開頭的全域管理員帳戶的密碼不正確。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
您嘗試要用於指定 Azure AD Connect 安裝程序的開頭的全域管理員帳戶的同盟的使用者。<br\><br\></li>
              </ul>
              <p>若要修正此錯誤，請確定您不使用同盟的帳戶全域管理員的指定 Azure AD Connect 安裝程序的開頭，而且指定的密碼不正確。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Azure AD Connect 安裝期間設定回寫的錯誤。</p>
            </td>
            <td>
              <p>Azure AD Connect 電腦的事件記錄檔含有錯誤 32002 PasswordResetService 擲回。</p>
              <p>

              </p>
              <p>讀取錯誤: 「 錯誤連線至 ServiceBus，權杖提供者無法提供安全性權杖...」</p>
              <p>

              </p>
            </td>
            <td>
              <p>這項錯誤的根本原因是，在您的內部部署環境中執行的密碼重設服務不能連線至雲端服務匯流排端點。 此錯誤通常以正常方式因封鎖輸出連線至特定的連接埠或 web 位址的防火牆規則。</p>
              <p>

              </p>
              <p>請確定您的防火牆允許傳出連線下列項目︰</p>
              <ul>
                <li class="unordered">
所有流量透過 TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
輸出連線 <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>當您更新這些規則時，請重新啟動 Azure AD Connect 電腦，並密碼回寫應該再次開始工作。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>密碼回寫端點內部部署無法連線到</p>
            </td>
            <td>
              <p>之後的部分時間、 同盟或密碼雜湊工作同步處理的使用者無法重設密碼。</p>
            </td>
            <td>
              <p>某些少見的情況下，可能會失敗重新啟動 Azure AD Connect 重新啟動時的密碼回寫服務。 在下列情況下，首先，檢查是否密碼回寫出現要啟用的 prem.上 這可以使用 [Azure AD Connect 精靈] 或 [powershell （請參閱 HowTos 上面）。如果功能已啟用，請嘗試啟用或停用的功能一次透過 UI 或 PowerShell。 請參閱 「 步驟 2︰ 啟用目錄同步處理電腦上的密碼回寫&amp;設定防火牆規則 」 中<a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">啟用或停用密碼回寫的方式</a>，如需有關如何進行此動作。</p>
              <p>

              </p>
              <p>如果無法解決問題，請嘗試完全解除安裝並重新安裝 Azure AD Connect。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>權限錯誤</p>
            </td>
            <td>
              <p>同盟或密碼雜湊同步處理鉈使用者嘗試重設自己的密碼，請參閱錯誤之後送出指出服務問題的密碼。</p>
              <p>

              </p>
              <p>此外，在密碼重設作業時，您可能會看到一個關於管理代理程式的錯誤拒絕存取您的部署事件記錄檔中。</p>
            </td>
            <td>
              <p>如果您的事件記錄檔] 中看到這些錯誤，請確認 [AD 台中帳戶 （中所指定的精靈設定的時間） 具有密碼回寫的必要權限]。</p>
              <p>

              </p>
              <p>請注意之後提供此權限, 可能需要向下 trickle 透過 sdprop 背景工作的 DC 上的權限最 1 小時。 </p>
              <p>重設為搭配使用的密碼，必須戳記，在其密碼重設使用者物件的安全性描述元權限。 此權限會顯示使用者物件中，直到會繼續失敗拒絕存取重設密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>從 Azure AD Connect 設定精靈設定密碼回寫時的錯誤 </p>
            </td>
            <td>
              <p>在精靈中啟用/停用密碼回寫的 「 無法尋找台中 」 錯誤</p>
            </td>
            <td>
              <p>Azure AD Connect 其在下列情況資訊清單的發行版本有已知的錯誤︰</p>
              <ol class="ordered">
                <li>
您設定 Azure AD Connect 的租用戶 abc.com （已驗證網域） 使用認證。 這會導致 AAD 連接器的 「 名稱 abc.com 的 – AAD 」 所建立。<br\><br\></li>
                <li>
您然後再變更連接器 （使用舊的同步處理 UI） AAD 認證 （請注意，它是相同的租用戶但不同的網域名稱）。 <br\><br\></li>
                <li>
現在您嘗試啟用或停用密碼回寫。 精靈會建構使用認證，為 「 abc.onmicrosoft.com – AAD 」 的連接器名稱，並將傳遞至密碼回寫指令程式。 這會失敗，因為沒有建立具有此名稱的連接器。<br\><br\></li>
              </ol>
              <p>此已經修正我們最新版本中。 如果您有較舊的建立，一個因應措施是使用 powershell cmdlet 來啟用或停用此功能。 請參閱 「 步驟 2︰ 啟用目錄同步處理電腦上的密碼回寫&amp;設定防火牆規則 」 中<a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">啟用或停用密碼回寫的方式</a>，如需有關如何進行此動作。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>若要重設密碼等 Domain Admins 特殊的群組中的使用者無法 / 企業系統管理員等等。</p>
            </td>
            <td>
              <p>同盟或密碼雜湊同步處理鉈屬於受保護的群組，並嘗試重設自己的密碼，請參閱錯誤，提交指出服務問題的密碼之後的使用者。</p>
            </td>
            <td>
              <p>權限的使用者在 Active Directory 中使用 AdminSDHolder 受保護狀態。 請參閱<a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a>如需詳細資訊。 </p>
              <p>

              </p>
              <p>這表示，這些物件的安全性說明會定期檢查以符合一個指定的 AdminSDHolder 和如果不同重設。 所需的密碼回寫的其他權限因此不 trickle 這類使用者。 這可能會導致無法運作的這類使用者的密碼回寫。如此一來，我們不支援管理密碼這些群組內的使用者因為它會中斷 AD 安全性模型。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>找不到重設作業失敗，與物件</p>
            </td>
            <td>
              <p>同盟或密碼雜湊同步處理鉈使用者嘗試重設自己的密碼，請參閱錯誤之後送出指出服務問題的密碼。</p>
              <p>

              </p>
              <p>此外，在密碼重設作業時，您可能會看到錯誤指出 「 物件找不到 」 錯誤 Azure AD Connect 服務事件記錄檔中。</p>
            </td>
            <td>
              <p>此錯誤通常表示同步處理引擎是找不到的使用者物件中 AAD 連接器空間或連結的 MV 」 或 「 AD 連接器空間物件。 </p>
              <p>

              </p>
              <p>若要疑難排解這個問題，請確定您的使用者確實，同步處理從內部部署 AAD 透過 Azure AD Connect 目前執行個體，並查看物件的連接器空格及 MV 的狀態。 確認 AD CS 物件是透過 「 Microsoft.InfromADUserAccountEnabled.xxx 」 規則 MV 物件的連接器。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設使用多個相符的項目找到 eror 作業失敗</p>
            </td>
            <td>
              <p>同盟或密碼雜湊同步處理鉈使用者嘗試重設自己的密碼，請參閱錯誤之後送出指出服務問題的密碼。</p>
              <p>

              </p>
              <p>此外，在密碼重設作業時，您可能會看到錯誤指出 「 找到多個 maches 」 錯誤 Azure AD Connect 服務事件記錄檔中。</p>
            </td>
            <td>
              <p>這表示同步處理引擎偵測到 MV 物件是透過 「 Microsoft.InfromADUserAccountEnabled.xxx 」 的連線至多個 AD CS 物件。 這表示使用者會有一個以上的樹系中啟用的帳戶。 </p>
              <p>

              </p>
              <p>目前的密碼回寫不支援這種情況。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>密碼作業失敗，並設定錯誤。</p>
            </td>
            <td>
              <p>密碼作業失敗，並設定錯誤。 應用程式的事件記錄檔中包含文字的 Azure AD Connect 錯誤 6329: 0x8023061f （作業失敗，因為密碼同步處理不會啟用這個管理代理程式。）</p>
            </td>
            <td>
              <p>這樣就會發生如果 Azure AD Connect 設定變更為新增&nbsp;新 AD 樹系 （或若要移除再重新加入 [現有的樹系）<strong>之後</strong>已啟用密碼回寫功能。 在這種新增樹系使用者的密碼操作會失敗。 若要修正此問題，請停用，並已完成樹系變更設定之後，重新啟用密碼回寫功能。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者變更回寫的密碼已重設使用者的運作正常，但是回寫的密碼或重設系統管理員的使用者會失敗。</p>
            </td>
            <td>
              <p>嘗試從 Azure 管理入口網站使用者代表重設密碼，您就會看到訊息，說明: 「 您的內部部署環境中執行的密碼重設服務不支援管理員重設使用者密碼。 請升級至最新版 Azure AD Connect，若要解決這個問題。 」</p>
            </td>
            <td>
              <p>這會發生的同步處理引擎版本不支援使用特定密碼回寫作業。 Azure AD Connect 1.0.0419.0911 支援所有密碼管理作業稍後版本，包括密碼重設回寫、 密碼變更回寫和從 Azure 管理入口網站的管理員啟動密碼重設回寫。&nbsp; DirSync 版本稍後比 1.0.6862 支援密碼重設回寫。 若要解決此問題，我們強烈建議您安裝最新版 Azure AD Connect 或 Azure [Active Directory 連線 （如需詳細資訊，請參閱<a href="active-directory-aadconnect">目錄整合工具</a>） 若要解決這個問題並取得充分利用您組織中的密碼回寫。</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>密碼回寫的事件記錄檔錯誤碼
疑難排解密碼回寫的問題時的最佳作法是檢查 Azure AD Connect 電腦上的應用程式事件記錄檔。 此事件記錄檔將密碼回寫的包含兩個來源感興趣的事件。 PasswordResetService 來源會說明作業及密碼回寫的作業相關的問題。 ADSync 來源會說明作業與 AD 環境中設定密碼的相關問題。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>程式碼</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>命名 / 訊息</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>來源</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>描述</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>釋出︰ MMS(4924) 0x80230619 – 」 限制防止密碼變更為指定的目前這一。 」</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>此事件發生時密碼回寫服務嘗試在您的本機目錄的不符合密碼年齡、 歷程記錄、 複雜性或篩選需求的網域設定密碼。</p>
              <ul>
                <li class="unordered">
如果您有最小的密碼期限，並且最近變更的時間的視窗中的密碼，您無法變更的密碼，直到達到您網域中指定的年齡。 供測試之用，最小的年齡應該設為 0。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼歷程記錄的需求，則您必須先選取的最後一個 N 時間，在未使用的密碼其中 N 是密碼的 [歷程記錄] 設定。 如果您選取的最後一個 N 時間使用的密碼，然後您會看到無法在此情況下。 供測試之用，則應該設為 0 的歷程記錄。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼的複雜性需求，所有這些強制當使用者嘗試變更或重設密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼篩選啟用，且使用者選了密碼不符合篩選的準則，然後重設或變更作業會失敗。<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>人力資源 8023042</p>
            </td>
            <td>
              <p>同步處理引擎會傳回錯誤 hr = 80230402，郵件 = 嘗試將物件失敗，因為具有相同的錨點的重複項目</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>此事件發生時相同的使用者識別碼會啟用多個網域。  例如，如果您正在同步處理的帳戶/資源樹系，並有相同的使用者識別碼展示且在每一個已啟用，可能會發生這個錯誤。  </p>
              <p>如果您使用的非唯一的錨點屬性 （例如別名或 UPN），兩個使用者共用該相同的錨點屬性，也可能會發生這個錯誤。</p>
              <p>若要解決此問題，請確定，您不需要任何重複的使用者在您的網域與您使用每個使用者是唯一的錨點的屬性。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示內部部署服務偵測到密碼重設要求同盟或密碼雜湊同步處理鉈來自雲端的使用者。 第一個事件在每個密碼重設回寫作業時，則此事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件指示使用者密碼重設作業期間選取新的密碼，我們判定這個密碼符合公司的密碼需求，該密碼已成功寫入回本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>使用者選取了密碼，然後密碼成功抵達內部部署環境，，但我們嘗試在本機 AD 環境中設定的密碼時，發生失敗，則會指出此事件。 這可能會發生原因包括︰</p>
              <ul>
                <li class="unordered">
使用者的密碼不符合年齡歷程記錄、 複雜度，或篩選的網域需求。 請嘗試解決這個問題的全新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
台中服務帳戶沒有適當的權限設定的問題的使用者帳戶的新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
使用者的帳戶是在受保護的群組中，例如網域或企業版不允許密碼設定作業的系統管理員。<br\><br\></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>若要瞭解更多關於其他哪些 situtions 可能會導致這個錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件如果您啟用與 Azure AD Connect 密碼回寫就會發生，表示我們啟動登入貴組織的密碼回寫 web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>登入程序順利完成及密碼回寫功能已可供使用，則會指出此事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示內部部署服務偵測到密碼變更要求的同盟或密碼雜湊同步處理鉈來自雲端的使用者。 此事件會在每個密碼變更回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件指示使用者密碼變更作業期間選取新的密碼，我們判定這個密碼符合公司的密碼需求，該密碼已成功寫入回本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>使用者選取了密碼，然後密碼成功抵達內部部署環境，，但我們嘗試在本機 AD 環境中設定的密碼時，發生失敗，則會指出此事件。 這可能會發生原因包括︰</p>
              <ul>
                <li class="unordered">
使用者的密碼不符合年齡歷程記錄、 複雜度，或篩選的網域需求。 請嘗試解決這個問題的全新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
台中服務帳戶沒有適當的權限設定的問題的使用者帳戶的新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
使用者的帳戶是在受保護的群組中，例如網域或企業版不允許密碼設定作業的系統管理員。<br\><br\></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>若要瞭解更多關於何種情況下可能會導致這個錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>密碼重設要求同盟或密碼雜湊同步處理鉈代表使用者針對系統管理員的使用者，偵測到的內部部署的服務。 此事件會在每個管理員啟動密碼重設回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>管理員選取新密碼管理員啟動密碼重設作業期間，我們判定這個密碼符合公司的密碼需求，該密碼已成功寫入回本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>管理員選取代表使用者的密碼和密碼成功抵達內部部署環境中，但我們嘗試在本機 AD 環境中設定的密碼時，發生失敗。 這可能會發生原因包括︰</p>
              <ul>
                <li class="unordered">
使用者的密碼不符合年齡歷程記錄、 複雜度，或篩選的網域需求。 請嘗試解決這個問題的全新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
台中服務帳戶沒有適當的權限設定的問題的使用者帳戶的新密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
使用者的帳戶是在受保護的群組中，例如網域或企業版不允許密碼設定作業的系統管理員。<br\><br\></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>若要瞭解更多關於哪些其他 situtions 可能會導致這個錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>如果您停用與 Azure AD Connect 密碼回寫就會發生此事件，然後表示我們啟動 offboarding 貴組織的密碼回寫 web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件會指出 offboarding 程序順利完成，而該密碼回寫功能已經順利停用。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示 offboarding 程序不成功。 在設定過程中指定之雲端或內部部署系統管理員帳戶權限錯誤可能是因為您想要停用密碼回寫時使用同盟的雲端全域管理員或。 若要修正此問題，核取您系統管理員權限，您不使用任何同盟帳戶，同時設定密碼回寫功能。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示密碼回寫服務已順利啟動，並已準備好接受雲端管理要求密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已停止密碼回寫服務，而且無法成功雲端任何密碼管理要求，則會指出此事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示我們成功擷取授權權杖，才能開始 offboarding 或登入程序 Azure AD Connect 安裝期間所指定的全域管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示我們已順利建立將用來傳送到您的內部部署環境雲端密碼加密的密碼加密金鑰。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件密碼管理作業期間，表示無法辨識的錯誤。 查看更多詳細資料的事件中的例外文字。 如果您有問題，請嘗試停用，並重新啟用密碼回寫。 如果這不協助，包括您的事件記錄檔，以及指定的追蹤識別碼複本至您的支援工程師內部。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件指出時發生錯誤連線至雲端密碼重設服務，通常發生內部部署服務無法連線至密碼重設 web 服務。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示連線到您的租用戶服務匯流排執行個體發生錯誤。 因為您會在您的內部部署環境中封鎖輸出連線，則會發生此問題。 檢查您的防火牆，以確保您允許連線，透過 TCP 443 和<a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>，並再試一次。 如果您仍有問題，請嘗試停用，並重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示傳遞給我們的 web 服務 API 輸入不正確。 請再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示抵達雲端密碼解密時發生錯誤。 這可能是因為解密鍵不相符的雲端服務與內部部署環境之間。 若要解決這個問題，請停用，並重新啟用您的內部部署環境中的 [密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>期間登入，我們會在您的內部部署環境中的設定檔中儲存租用戶專屬的資訊。 儲存此檔案時發生錯誤，當那里啟動服務已錯誤讀取或檔案，則會指出此事件。 若要修正此問題，請嘗試強制重新撰寫此設定檔停用並重新啟動密碼回寫。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已遭取代 – 此事件不存在於中 Azure AD Connect，很早期只 DirSync 支援回寫的建立。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>登入，在我們傳送雲端的資料的內部部署密碼重設服務。 記憶體內檔案然後會寫入的資料之前磁碟上的安全地儲存此資訊傳送到同步處理服務。 此事件表示撰寫或更新的記憶體中的資料有問題。 若要修正此問題，請嘗試停用並重新啟動密碼回寫強制重新寫入這項設定。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示我們密碼重設 web 服務收到不正確的回應。 若要修正此問題，請嘗試停用並重新啟動密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示，我們無法取得授權權杖 Azure AD Connect 安裝期間所指定的全域管理員帳戶。 此錯誤可能造成錯誤的使用者名稱，或指定的全域管理員帳戶的密碼，或因為指定的全域管理員帳戶同盟。 若要修正此問題，請重新執行使用正確的使用者名稱和密碼的設定，並確保系統管理員已受管理 (僅限雲端或密碼同步處理會) 帳戶。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示時，發生錯誤時的密碼加密金鑰或解密送達來自雲端服務的密碼。 此錯誤可能會指出您的環境發生問題。 查看您的事件記錄檔，若要深入瞭解並解決這個問題的詳細資料。 您也可以嘗試停用和重新啟用密碼回寫服務，若要解決這個問題。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示的內部部署服務無法正確地通訊與密碼重設 web 服務進行登入程序。 這可能是由於防火牆規則或取得您的租用戶的 「 驗證 」 token 的問題。 若要修正此問題，確定您不會在 TCP 443 並 TCP 9350 9354 或<a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>，封鎖輸出連線並不同盟 AAD 管理員帳戶，您會使用內建。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已遭取代 – 此事件不存在於中 Azure AD Connect，很早期只 DirSync 支援回寫的建立。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示的內部部署服務無法正確地通訊與密碼重設 web 服務進行 offboarding 程序。 這可能是由於防火牆規則或取得您的租用戶中的授權權杖的問題。 若要修正此問題，請確定您不會封鎖輸出連線，透過 443 或<a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>，並不同盟 AAD 管理員帳戶您使用 offboard。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示我們要再試一次連線到您的租用戶服務匯流排執行個體。 在標準的情況下這不應該是考量，但如果您看到此事件的次數，請考慮檢查您的網路連線到服務匯流排，尤其是在高延遲或低頻寬連線。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>監視您的密碼回寫服務健康狀況，我們傳送活動訊號資料，我們的密碼重設 web 服務每 5 分鐘。 此事件表示時發生錯誤時傳送此狀況資訊給雲端 web 服務。 此狀況資訊不包含 OII 或 PII 的資料，以及是完全活動訊號基本服務統計資料，讓我們可以提供在雲端服務的狀態資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示有 AD 所傳回的未知的錯誤，請檢查 Azure AD Connect 伺服器事件記錄檔，如需此錯誤 ADSync 來源的事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示，嘗試重設或變更密碼的使用者找不到內部部署目錄中。 這可能會發生使用者已刪除的內部部署時，而不是在雲端，或如果有同步處理問題。 核取您同步處理記錄，以及最後幾個同步處理執行詳細資料，如需詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>密碼重設或變更要求來自雲端，我們會使用指定在安裝過程中的 Azure AD Connect 雲端錨點，決定如何連結該要求回內部部署環境中的使用者。 此事件表示我們相同的雲端錨點屬性與內部部署目錄中找到兩個使用者。 核取您同步處理記錄，以及最後幾個同步處理執行詳細資料，如需詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示的管理代理程式服務帳戶沒有適當的權限設定的新密碼問題的帳戶。 確定使用者的樹系台中帳戶樹系中的所有物件上已重設] 和 [變更密碼權限。  如需有關如何執行此，請參閱<a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">步驟 4︰ 設定適當的 Active Directory 權限</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示我們嘗試重設或變更的已停用內部部署帳戶的密碼。 啟用帳戶，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>事件表示我們嘗試重設或變更的內部部署鎖定帳戶的密碼。 當使用者嘗試變更或重設密碼作業太多次在短時間時，可能會發生鎖定。 解除鎖定帳戶，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件會指出使用者在執行密碼變更作業時，會指定目前的密碼不正確。 指定正確目前的密碼，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件發生時密碼回寫服務嘗試在您的本機目錄的不符合密碼年齡、 歷程記錄、 複雜性或篩選需求的網域設定密碼。</p>
              <ul>
                <li class="unordered">
如果您有最小的密碼期限，並且最近變更的時間的視窗中的密碼，您無法變更的密碼，直到達到您網域中指定的年齡。 供測試之用，最小的年齡應該設為 0。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼歷程記錄的需求，則您必須先選取的最後一個 N 時間，在未使用的密碼其中 N 是密碼的 [歷程記錄] 設定。 如果您選取的最後一個 N 時間使用的密碼，然後您會看到無法在此情況下。 供測試之用，則應該設為 0 的歷程記錄。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼的複雜性需求，所有這些強制當使用者嘗試變更或重設密碼。<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
如果您有密碼篩選啟用，且使用者選了密碼不符合篩選的準則，然後重設或變更作業會失敗。<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示有密碼返回您的內部部署目錄因為組態問題與 Active Directory 問題撰寫。 檢查 Azure AD Connect 電腦從 ADSync 服務的詳細資訊發生何種錯誤的郵件應用程式事件記錄檔。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已遭取代 – 此事件不存在於中 Azure AD Connect，很早期只 DirSync 支援回寫的建立。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已遭取代 – 此事件不存在於中 Azure AD Connect，很早期只 DirSync 支援回寫的建立。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>已遭取代 – 此事件不存在於中 Azure AD Connect，很早期只 DirSync 支援回寫的建立。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>密碼回寫連線疑難排解

如果您遇到的 Azure AD Connect 的服務中斷，將密碼回寫的元件，以下是一些您可以採取以解決這個問題的快速步驟︰

 - [重新啟動 Azure AD 連線同步處理服務](#restart-the-azure-AD-Connect-sync-service)
 - [停用或重新啟用密碼回寫功能](#disable-and-re-enable-the-password-writeback-feature)
 - [安裝最新版 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [疑難排解密碼回寫](#troubleshoot-password-writeback)

一般而言，我們建議您以最快速的方式來復原您的服務才能上述的順序執行這些步驟。

### <a name="restart-the-azure-ad-connect-sync-service"></a>重新啟動 Azure AD 連線同步處理服務
重新啟動 Azure AD 連線同步處理服務可以協助您解決連線問題或其他服務的暫時性問題。

 1. 身為管理員，按一下 [執行**Azure AD Connect**伺服器上的 [**開始**]。
 2. 在 [搜尋] 方塊中輸入**「 services.msc 」** ，然後按**Enter**。
 3. 尋找**Microsoft Azure AD Connect**項目。
 4. 服務項目上按一下滑鼠右鍵，按一下 [**重新啟動**，並等待作業完成。

    ![][002]

這些步驟會重新建立您的雲端服務的連線，並解決您可能會發生任何中斷情況。  如果重新啟動同步處理服務無法解決您的問題，我們建議您嘗試要停用，並重新啟用密碼回寫功能，在下一個步驟。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>停用或重新啟用密碼回寫功能
停用和重新啟用密碼回寫功能可協助解決連線問題。

 1. 身為管理員，開啟**Azure AD Connect 設定精靈**。
 2. 在 [**連線至 Azure AD** ] 對話方塊中，輸入**Azure AD 全域管理員認證**
 3. 在 [**連線至 AD DS** ] 對話方塊中，輸入您的**AD 網域服務管理員認證**。
 4. 在 [**可唯一識別您的使用者**] 對話方塊中，按一下 [**下一步**] 按鈕。
 5. 在 [**選擇性功能**] 對話方塊中，取消核取**密碼回寫功能**] 核取方塊。

    ![][003]

 6. 按**下一步**] 對話方塊中其他頁面而不變更任何項目，直到移至**準備設定**] 頁面。
 7. 確定 [設定] 頁面顯示為 [**停用為密碼回寫選項**，然後按一下 [認可變更的綠色的 [**設定**] 按鈕。
 8. 在 [**完成**] 對話方塊中，取消選取 [**立即同步處理**] 選項，然後再按一下 [**完成**] 以關閉精靈。
 9. 重新開啟**Azure AD Connect 設定精靈**。
 10.    **重複步驟 2-8**，但 [**選用的功能**中的 [**檢查密碼回寫] 選項**，確保您螢幕畫面重新啟用服務。

    ![][004]

這些步驟會重新建立您的連線，我們雲端服務，並解決您可能會發生任何中斷情況。

如果停用和重新啟用密碼回寫功能無法解決您的問題，我們建議您嘗試重新安裝 Azure AD Connect 下一個步驟。

### <a name="install-the-latest-azure-ad-connect-release"></a>安裝最新版 Azure AD Connect
重新安裝 Azure AD Connect 套件會解決設定問題可能會影響能力先將其連線至我們雲端服務，或管理您的本機 AD 環境中的密碼。
我們建議您，只有在嘗試上述的前兩個步驟之後，才能執行此步驟。

 1. 下載最新版 Azure AD Connect[以下](active-directory-aadconnect.md#install-azure-ad-connect)。
 2. 因為您已安裝 Azure AD Connect，您會只需要執行就地升級到更新為最新版本的 Azure AD Connect 安裝。
 3. 執行下載的套件，並遵循螢幕上更新您的 Azure AD Connect 電腦的相關指示。  沒有其他手動步驟所需的除非您已自訂的同步處理規則] 方塊中，**這些之前，先備份繼續進行升級，並手動重新部署完成之後，您**應該在此情況下。

這些步驟會重新建立您的連線，我們雲端服務，並解決您可能會發生任何中斷情況。

如果安裝最新版 Azure AD Connect 伺服器無法解決您的問題，我們建議最後一個步驟中嘗試停用並重新啟動密碼回寫，安裝最新的同步處理 QFE 後。

如果的無法解決您的問題，我們建議您，請查看[疑難排解密碼回寫](#troubleshoot-password-writeback)和[Azure AD 密碼管理常見問題集](active-directory-passwords-faq.md)，以查看 [如果您的問題可能會那里討論。


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>連結至密碼重設文件
以下是所有 Azure AD 密碼重設文件頁面的連結︰

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
* [**運作方式**](active-directory-passwords-how-it-works.md)-瞭解六個不同的元件服務，以及每個會
* [**快速入門**](active-directory-passwords-getting-started.md)-瞭解如何允許您使用者重設並變更其雲端或內部部署的密碼
* [**自訂**](active-directory-passwords-customize.md)-瞭解如何自訂外觀與風格與您組織的需求服務的行為
* [**最佳作法**](active-directory-passwords-best-practices.md)-瞭解如何快速部署及有效地管理您的組織中的密碼
* [**取得深入見解**](active-directory-passwords-get-insights.md)-瞭解我們整合式的報告權限
* [**常見問題集**](active-directory-passwords-faq.md)-取得常見問題的解答
* [**進一步瞭解**](active-directory-passwords-learn-more.md)移深的 service 的運作方式技術詳細資料



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
