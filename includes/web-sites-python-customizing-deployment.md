Azure 會決定應用程式使用 Python**這些情況皆成立時**︰

- requirements.txt 的根資料夾中的檔案
- 在根資料夾中的任何.py 檔案或指定 python runtime.txt

當的大小寫，它會使用 Python 特定部署指令碼，執行標準的同步處理的檔案，以及其他 Python 作業，例如︰

- 自動管理虛擬環境
- 使用 pip requirements.txt 中所列的套件的安裝
- 根據所選的 Python 版本適當 web.config 建立。
- 收集 Django 應用程式的靜態檔案

您可以控制預設部署步驟的某部分，而不需要自訂指令碼。

如果您想要略過所有 Python 特定的部署步驟，您可以建立此空白檔案︰

    \.skipPythonDeployment

更進一步控制部署的詳細資訊，您都可以建立下列檔案來覆寫預設部署指令碼︰

    \.deployment
    \deploy.cmd

若要建立的檔案，您可以使用[Azure 命令列介面][]。  從 [專案] 資料夾中使用此命令︰

    azure site deploymentscript --python

當這些檔案不存在時，Azure 建立暫時部署指令碼，然後執行。  這是您使用上述命令來建立相同。

[Azure 命令列介面]: http://azure.microsoft.com/downloads/
