
<properties
    pageTitle="使用 Azure AD 連線健康狀況與 AD DS |Microsoft Azure"
    description="這是將討論如何監控 AD DS 的 Azure AD 連線健康狀況] 頁面。"
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>使用 Azure AD 連線健康狀況與 AD DS
下列文件是特定監控 Azure AD 連線健康情況的 Active Directory 網域服務。 支援 AD DS 的版本︰ Windows Server 2008 R2、 Windows Server 2012 及 Windows Server 2012 R2。

如需有關如何監控 AD FS Azure AD 連線健康情況的詳細資訊，請參閱[使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)。 此外，如監控 Azure AD Connect （同步處理） Azure AD 連線健康情況的詳細資訊請參閱[使用 Azure AD 連線狀況的同步處理](active-directory-aadconnect-health-sync.md)。

![Azure AD Connect AD ds 的狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>通知 Azure AD 連線 AD ds 的狀況
Azure AD 連線狀況 AD ds 中的 [通知] 區段會提供作用中以及解決通知，您的網域控制站相關的清單。 選取 [作用中或解決提醒的其他資訊，以及解決步驟開啟新的刀及連結支援文件。 每一種通知類型可以有一或多個對應至每個網域控制站受到該特定的提醒的執行個體。 底部附近的通知刀，您可以按兩下以開啟該通知的執行個體的相關的更多詳細資料其他刀受影響的網域。

內此刀，您可以啟用通知的電子郵件通知，並在檢視中變更的時間範圍。 展開的時間範圍，可讓您看到先前解決的通知。

![Azure AD Connect 同步處理錯誤](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>網域控制站儀表板
此儀表板提供拓撲檢視您的環境，主要操作的度量，以及每個監視的網域控制站的健康狀態。 呈現的指標幫助您快速識別可能需要進一步調查任何網域控制站。 根據預設，會顯示資料行的子集。 不過，您也可以按兩下 [欄] 命令來尋找整組可用的資料行。 選取您最關注的資訊，開啟此儀表板並輕鬆地在單一位置檢視 AD DS 環境的狀況的資料行。

![網域控制站](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

網域控制站可以依其個別網域或網站，讓您瞭解環境拓撲還是有幫助。 最後，如果您按兩下刀標題時，儀表板最大化利用可用的螢幕大小。 顯示多個資料行時，此較大的檢視有幫助。

## <a name="replication-status-dashboard"></a>複寫狀態儀表板
此儀表板提供的檢視複製狀態和監視的網域控制站複寫拓撲。 列出的最新的複寫嘗試狀態，以及很有幫助發現任何錯誤的文件。 您可以按兩下網域控制站發生錯誤時，若要開啟新的刀資訊，例如︰ 錯誤，建議的解決步驟及連結至疑難排解文件的詳細資訊。

![複製狀態](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>監控
此功能會提供不同的效能計數器，持續收集的每一種監視的網域控制站的圖形化趨勢。 樹系中所有其他監視的網域控制站時，可以輕鬆地比較網域控制站的效能。 此外，您可以看到各種效能計數器並列，這就很有幫助您的環境中的問題的疑難排解。

![監控](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

根據預設，我們已預先選取四個效能計數器;不過，您可以藉由按一下 [篩選] 命令，選取或取消選取任何想要的效能計數器包含其他人。 此外，您可以按兩下以開啟新的刀，其中包含每個監視的網域控制站的資料點的效能計數器圖形。

## <a name="related-links"></a>相關的連結

* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況代理程式的安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect 狀況作業](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用同步處理 Azure AD 連線狀況](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect 狀況常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
