有些套件可能無法安裝使用 pip 在 Azure 上執行時。  只要可能套件無法使用 Python 套件索引。  可能是編譯器，不需要 （編譯器不 Azure 應用程式服務中執行的 web 應用程式的電腦上）。

在此區段中，我們會查看處理此問題的方法。

### <a name="request-wheels"></a>要求滾輪

如果套件安裝需要編譯器，您應該嘗試連絡要求滾輪進行套件可用套件的擁有者。

使用[Microsoft Visual c + + 編譯器的 Python 2.7][]最近可用性，現在建立的 Python 2.7 有原生程式碼的封裝變得更容易。

### <a name="build-wheels-requires-windows"></a>建立滾輪 （需要 Windows）

附註︰ 使用此選項時，請務必編譯使用 Python 環境符合平台/架構/版本用於 Azure 應用程式服務中的 web 應用程式套件 （Windows/32-bit/2.7 或 3.4）。

如果沒有安裝套件，因為其需要編譯器，您可以在您的本機電腦上安裝編譯器，並建立封裝，然後將存放庫中要包含的滾輪。

Mac/Linux 使用者︰ 如果您沒有安裝 Windows 電腦存取，請參閱[建立虛擬機器在執行 Windows][] Azure 上建立 VM。  您可以使用其建立滾輪，將其新增至存放庫，如果您想放棄 VM。 

您可以安裝[Microsoft Visual c + + 編譯器的 Python 2.7][]Python 2.7。

Python 3.4，您可以安裝[Microsoft Visual c + + 2010 Express][]。

若要建立滾輪，您必須使用滾輪套件︰

    env\scripts\pip install wheel

您會使用`pip wheel`編譯的相依性︰

    env\scripts\pip wheel azure==0.8.4

這會建立 \wheelhouse 資料夾.whl 檔案。  新增 \wheelhouse 資料夾和滾輪檔案到您儲存機制。

編輯您要新增的 requirements.txt`--find-links`頂端的選項。 這就是告訴 pip 之前前往 python 套件索引查詢完全符合本機資料夾中。

    --find-links wheelhouse
    azure==0.8.4

如果您想要 \wheelhouse 資料夾中包含您所有的相依性，並不會在使用 python 套件索引，您可以強制藉由新增忽略套件索引 pip`--no-index`您 requirements.txt 的頂端。

    --no-index

### <a name="customize-installation"></a>自訂安裝

您可以自訂安裝套件使用替代的安裝程式，例如輕鬆虛擬環境中部署指令碼\_安裝。  請參閱 deploy.cmd 加上註解的範例。  請確定的這類封包未列在 requirements.txt，若要防止 pip 進行安裝。

部署指令碼加入這項︰

    env\scripts\easy_install somepackage

您也可以輕鬆使用\_安裝來安裝執行檔安裝程式 (有某些 zip 相容，很容易\_安裝支援這些)。  將安裝程式新增至您的存放庫，並輕鬆叫用\_安裝可執行檔傳遞路徑。

新增此部署指令碼︰

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>在儲存機制 （需要 Windows） 中包含的虛擬環境

附註︰ 使用此選項時，請務必使用符合平台/架構/版本上 Azure 應用程式服務中的 web 應用程式使用在虛擬環境 （Windows/32-bit/2.7 或 3.4）。

如果您將存放庫內包含虛擬環境，您可以防止的部署指令碼執行 Azure 虛擬環境管理來建立空白的檔案︰

    .skipPythonDeployment

我們建議您刪除現有的虛擬環境上，請在應用程式，以防止剩餘的虛擬環境時自動管理的檔案。


[建立執行 Windows 的虛擬機器]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual c + + 編譯器 python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual c + + 2010 Express]: http://go.microsoft.com/?linkid=9709949
