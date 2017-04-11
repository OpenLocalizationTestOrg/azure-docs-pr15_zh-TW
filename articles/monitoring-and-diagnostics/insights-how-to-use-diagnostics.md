<properties
    pageTitle="啟用監控和診斷 Microsoft Azure 中的 |Microsoft Azure "
    description="瞭解如何設定診斷 Azure 中的資源。"
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="enable-monitoring-and-diagnostics"></a>啟用監控和診斷

在[Azure 入口網站](https://portal.azure.com)，您可以設定豐富的經常性，監控和診斷資料的相關資源。 您也可以使用[REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx)或[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)設定診斷以程式設計方式。

診斷、 監控及公制 Azure 中的資料會儲存到您所選擇的儲存空間。 這個選項可讓您可以使用任何您想要的工具讀取資料，儲存檔案總管] 中，從第三方修整 Power BI。

## <a name="when-you-create-a-resource"></a>當您建立資源

大部分的服務可讓您啟用診斷，當您第一次在[Azure 入口網站](https://portal.azure.com)建立。

1. 移至 [**新增]** ，然後選擇您感興趣的資源。

2. 選取 [**選擇性的設定**]。
    ![診斷刀](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. 選取 [**診斷程式**，然後按一下****。 您必須選擇您想要儲存到診斷的儲存空間帳戶。 您會被會儲存與交易的一般資料費率當您儲存帳戶傳送診斷。

4. 按一下**[確定]** ，並建立資源。

## <a name="change-settings-for-an-existing-resource"></a>變更現有的資源設定

如果您已經建立資源，而且您想要變更的診斷設定 （例如變更資料收集的層級），您可以執行 Azure 入口網站中的該權限。

1. 移至 [資源，然後按一下 [**設定**] 命令。

2. 選取 [**診斷**]。

3. **診斷**刀有的所有可能的診斷與該資源的監控集合資料。 您也可以選擇資料，以清理儲存帳戶的**保留**原則的資源。
    ![儲存診斷程式](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 一旦您已選擇您的設定，請按一下 [**儲存**] 命令。 需要稍微時的監視顯示如果您要啟用它第一次的資料。

### <a name="categories-of-data-collection-for-virtual-machines"></a>類別的虛擬機器中的資料收集
虛擬機器所有指標和記錄都會都記錄在一分鐘的時間間隔，因此您永遠可以瞭解您的電腦是最新的資訊。

- **基本的指標**︰ 您的虛擬機器處理器和記憶體等狀況指標
- **網路和 web 指標**︰ 關於您的網路連線和 web 服務的指標
- **.NET 指標**︰ 在您的虛擬機器上執行的.NET 和 ASP.NET 應用程式相關的指標
- **SQL 指標**︰ 如果您正在執行 Microsoft SQL 服務，其效能指標
- **Windows 事件記錄] 應用程式**︰ 傳送給該應用程式頻道的 Windows 事件
- **Windows 系統記錄**︰ 傳送給系統頻道的 Windows 事件。 這也包含來自[Microsoft 反惡意程式碼](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)的所有事件。
- **Windows 事件記錄] 安全性**︰ 傳送給安全通道的 Windows 事件
- **診斷基礎結構記錄**︰ 診斷集合基礎結構的相關記錄
- **IIS 記錄**︰ IIS 伺服器的相關記錄

請注意，這次 Linux 特定散發不受支援，而且虛擬機器上必須安裝來賓代理程式。

## <a name="next-steps"></a>後續步驟

* [接收提醒通知](insights-receive-alert-notifications.md)作業事件發生時或指標交互臨界值。
* [監視服務指標](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
* [自動調整執行個體計數](insights-how-to-scale.md)，以確定您的服務比例根據需求。
* [監視應用程式效能](../application-insights/app-insights-azure-web-apps.md)如果您想要了解完全如何程式碼執行雲端中。
* [檢視事件及稽核記錄檔](insights-debugging-with-events.md)若要瞭解所有項目發生在您的服務。
* 若要找出 Azure 遇到效能降低或服務中斷時[追蹤服務健康狀況](insights-service-health.md)。
