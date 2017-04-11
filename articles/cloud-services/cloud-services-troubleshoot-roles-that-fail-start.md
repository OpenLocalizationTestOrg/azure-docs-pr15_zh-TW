<properties
   pageTitle="疑難排解無法啟動的角色 |Microsoft Azure"
   description="以下是一些常見的原因為何雲端服務角色可能無法啟動。 另外也提供這些問題的解決方案。"
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>疑難排解無法啟動的雲端服務角色

以下是一些常見的問題和解決方案相關 Azure 雲端服務無法啟動的角色。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>遺漏 Dll 或相依性

沒有回應的角色和循環**初始化**]、 [**忙碌**]，[**停止**狀態之間的角色，會造成遺失 Dll 或組件。

您可以執行徵狀缺少 Dll 或組件︰

- 您的角色執行個體輪流**初始化**]、 [**忙碌**]，[**停止**狀態。
- 您的角色執行個體已經**準備好**，但如果您瀏覽至您的 web 應用程式，不會出現頁面。

有數種建議調查這些問題的方法。

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>診斷遺失 DLL 問題，在網頁的角色

當您瀏覽至網站的部署網站中的角色，並在瀏覽器會顯示類似下列伺服器錯誤時，可能表示 DLL 會遺失。

![「 / 」 應用程式中的伺服器錯誤。](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>關閉 [自訂錯誤診斷問題

更完整的錯誤資訊，請設定 web.config web 角色設定自訂錯誤模式為 [關閉並重新部署服務可以檢視。

若要檢視更完整的錯誤，而不使用遠端桌面︰

1. Microsoft Visual Studio 中開啟的方案。

2. 在**方案總管]**中，找出 web.config 檔案並將其開啟。

3. 在 web.config 檔案中，找出 [system.web] 區段中，新增下列行︰

    ```xml
    <customErrors mode="Off" />
    ```

4. 儲存檔案。

5. 封裝並重新部署服務。

一旦服務已部署，您會看到錯誤訊息與遺失的組件或 DLL 的名稱。

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>診斷遠端檢視錯誤的問題

您可以使用遠端桌面存取角色及遠端檢視更完整的錯誤資訊。 您可以使用下列步驟，使用遠端桌面檢視錯誤︰

1. 確定已安裝 Azure SDK 1.3 或更新版本。

2. 使用 Visual Studio 方案的部署時, 選擇 「 設定遠端桌面連線...」。 如需有關如何設定 「 遠端桌面連線的詳細資訊，請參閱[使用遠端桌面 Azure 角色](../vs-azure-tools-remote-desktop-roles.md)。

3. 在 Microsoft Azure 傳統入口網站後執行個體顯示狀態為 [**準備好**，請按一下其中一個角色執行個體。

4. 按一下功能區的 [**遠端存取**] 區域中的 [**連線**] 圖示。

5. 使用遠端桌面設定時所指定的認證登入虛擬機器。

6. 開啟命令視窗。

7. 輸入`IPconfig`。

8. 請注意 [IPV4 位址] 值。

9. 開啟 Internet Explorer。

10. 輸入地址] 和 [web 應用程式的名稱。 例如， `http://<IPV4 Address>/default.aspx`。

瀏覽至網站現在會傳回更明確的錯誤訊息︰

* 「 / 」 應用程式中的伺服器錯誤。

* 描述︰ 目前的 web 要求執行期間發生處理的例外狀況。 請檢閱堆疊追蹤，如需有關錯誤，並產生程式碼中的位置。

* 例外狀況的詳細資料︰ System.IO.FIleNotFoundException︰ 無法載入檔案或組件 」 Microsoft.WindowsAzure.StorageClient、 版本 = 1.1.0.0，文化特性 = 中性，PublicKeyToken = 31bf856ad364e35' 或其中一個相依性。 系統找不到所指定的檔案。

例如︰

![「 / 」 應用程式中的明確伺服器錯誤](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>使用計算模擬器診斷問題

您可以使用 Microsoft Azure 計算模擬器診斷和疑難排解遺漏相依性與 web.config 錯誤的問題。

使用這個方法的診斷最佳結果，您應使用的電腦或具有全新的 Windows 安裝的虛擬機器。 若要最適合模擬 Azure 環境，請使用 Windows Server 2008 R2 x64。

1. 安裝[Azure SDK](https://azure.microsoft.com/downloads/)的獨立版本。

2. 開發在電腦上，建立雲端服務專案。

3. 在 Windows 檔案總管] 中瀏覽至雲端服務專案的 bin\debug 資料夾。

4. 將.csx 資料夾和.cscfg 檔案複製到 [偵錯問題，您所使用的電腦。

5. 清除在電腦上，開啟 [Azure SDK 命令提示字元視窗，輸入`csrun.exe /devstore:start`。

6. 在命令提示字元中，輸入`run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`。

7. 角色啟動時，您會看到詳細的錯誤資訊，在 Internet Explorer 中。 您也可以使用標準 Windows 疑難排解工具，以進一步診斷問題。

## <a name="diagnose-issues-by-using-intellitrace"></a>使用 IntelliTrace 診斷問題

工作和使用.NET Framework 4 的 web 角色，您可以使用[IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx)，已在[Microsoft Visual Studio 最終](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs)。

請遵循下列步驟，以啟用 IntelliTrace 部署服務︰

1. 確認已安裝 Azure SDK 1.3 或更新版本。

2. 使用 Visual Studio 中部署方案。 部署期間，核取 [**啟用 IntelliTrace.NET 4 角色的**核取方塊。

3. 後執行個體啟動了，請開啟**伺服器總管]**。

4. 展開**Azure\\雲端服務**節點並找出部署。

5. 直到您看到角色執行個體，請展開 [部署]。 以滑鼠右鍵按一下其中一個執行個體。

6. 選擇 [**檢視 IntelliTrace 記錄**]。 隨即會開啟**IntelliTrace 摘要**。

7. 找出摘要的 [例外狀況] 的區段。 如果有例外狀況，[] 區段會標示**例外狀況資料**。

8. 展開 [**例外狀況資料**，看起來類似下列**System.IO.FileNotFoundException**錯誤︰

![例外狀況資料、 遺失的檔案或組件](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>地址遺失 Dll 和組件

若要解決遺失 DLL 和組件錯誤，請遵循下列步驟︰

1. 在 Visual Studio 中開啟的方案。

2. 在**方案總管]**中，開啟 [**參考資料**] 資料夾。

3. 按一下 [組件中的錯誤]。

4. 在 [**屬性**] 窗格中，找出**複製到本機] 屬性**，設為**True**的值。

5. 重新部署雲端服務。

一旦您確認已修正的所有錯誤時，您可以部署服務，而不核取 [**啟用 IntelliTrace.NET 4 角色的**核取方塊。

## <a name="next-steps"></a>後續步驟

檢視更多[疑難排解文章](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)雲端服務。

若要瞭解如何使用 Azure PaaS 電腦診斷資料，即可疑難排解在雲端服務角色問題，請參閱[Kevin Williamson 部落格系列](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)。
