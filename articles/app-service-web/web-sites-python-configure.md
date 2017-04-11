<properties 
    pageTitle="設定 Python Azure 應用程式服務 Web 應用程式" 
    description="本教學課程說明撰寫和 Azure 應用程式服務 Web 應用程式上設定的基本的網頁伺服器閘道器介面 (WSGI) 相容 Python 應用程式的選項。" 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>設定 Python Azure 應用程式服務 Web 應用程式

本教學課程說明撰寫和[Azure 應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)上設定的基本網頁伺服器閘道器介面 (WSGI) 遵守 Python 應用程式的選項。

它描述給部署，例如虛擬環境和使用 requirements.txt 套件安裝的其他功能。


## <a name="bottle-django-or-flask"></a>多、 Django 或 Flask 嗎？

Azure Marketplace 包含多 Django 與 Flask 架構的範本。 如果您開發 Azure 應用程式服務中，第一個 web 應用程式，或您不熟悉給，我們建議您遵循這些教學課程，其中包含建立可用的應用程式從圖庫給部署 Windows 或 Mac 的逐步指示其中一項︰

- [建立多 web 應用程式](web-sites-python-create-deploy-bottle-app.md)
- [使用 Django 建立 web 應用程式](web-sites-python-create-deploy-django-app.md)
- [使用 Flask 建立 web 應用程式](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Azure 入口網站上建立 web 應用程式

本教學課程假設現有 Azure 訂閱與 Azure 入口網站的存取權。

如果您沒有在現有的 web 應用程式，您可以建立一個從[Azure 入口網站](https://portal.azure.com)。  按一下左上角，[新增] 按鈕，然後按一下 [ **Web + 行動** > **Web 應用程式**。

## <a name="git-publishing"></a>給發佈

設定給發佈新建立的 web 應用程式在[本機給部署至 Azure 應用程式服務](app-service-deploy-local-git.md)的指示執行。 本教學課程中使用給建立、 管理及我們 Python web 應用程式發佈到 Azure 應用程式服務。

給發佈設定時，會建立並與您的 web 應用程式相關聯給存放庫。 存放庫的 URL 會顯示，並是日後可將資料從本機的開發環境發送至雲端。 若要發佈給透過應用程式，請確定給用戶端也會安裝並使用所提供給您的 web 應用程式內容推 Azure 應用程式服務的相關指示。


## <a name="application-overview"></a>應用程式概觀

在下一個區段中，會建立下列檔案。 他們應該置於給存放庫的根目錄。

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>WSGI 處理常式

WSGI 是 Python 標準所定義的網頁伺服器與 Python 之間介面[PEP 3333](http://www.python.org/dev/peps/pep-3333/)描述。 它提供標準的介面撰寫各種 web 應用程式使用 Python 架構。 熱門 Python web 架構今天使用 WSGI。 Azure 應用程式服務 Web 應用程式可讓您支援這類架構。此外，進階的使用者可以甚至撰寫自己，只要自訂處理常式遵循 WSGI 規格指導方針。

以下是範例`app.py`定義自訂的處理常式︰

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

您可以執行這個應用程式與本機`python app.py`，然後瀏覽至`http://localhost:5555`在網頁瀏覽器中。


## <a name="virtual-environment"></a>虛擬環境

雖然上述範例應用程式不需要任何外部套件，則可能是應用程式所需的部分。

若要協助管理外部套件的相依性，Azure 給部署支援建立的虛擬環境。

Azure 會偵測 requirements.txt 存放庫的根目錄，它會自動建立名為在虛擬環境`env`。 這只是在第一個部署中，或選取 Python 之後任何部署期間執行階段已變更。

您可能會想要建立本機的開發、 在虛擬環境，但不包括您就可以給存放庫內。


## <a name="package-management"></a>封裝管理

將使用 pip 虛擬環境中自動安裝套件 requirements.txt 中所列。 發生在每個部署中，這種情況，但 pip 會略過安裝，如果已安裝套件。

範例`requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Python 版本

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

範例`runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config

您需要建立 web.config 檔案，以指定 [伺服器應該如何處理要求。

請注意，如果您存放位置 x.y 符合所選的 Python 執行階段，然後 Azure 會自動將複製為 web.config 適當的檔案中有 web.x.y.config 檔案。

下列 web.config 範例依賴虛擬環境 proxy 指令碼下, 一節所述。  使用範例中的 WSGI 處理常式`app.py`上方。

範例`web.config`python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


範例`web.config`python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


靜態檔案處理網頁伺服器直接，不透過 Python 程式碼，以改善效能。

在上述範例中，在磁碟上的靜態檔案的位置應符合 URL 中的位置。 這表示要求`http://pythonapp.azurewebsites.net/static/site.css`做在磁碟上的檔案`\static\site.css`。

`WSGI_ALT_VIRTUALENV_HANDLER`是您指定 WSGI 處理常式的位置。 在上述範例中，有`app.wsgi_app`因為處理常式是一個名為 「 函數`wsgi_app`中`app.py`的根資料夾中。

`PYTHONPATH`您可以自訂，但如果您在虛擬環境中安裝您所有的相依性在 requirements.txt 時，您應該不需要加以變更。


## <a name="virtual-environment-proxy"></a>虛擬環境 Proxy

下列指令碼用來擷取 WSGI 處理常式、 啟動虛擬環境和記錄錯誤。 它被專為一般，而不需修改使用。

內容`ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>自訂給部署

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>疑難排解-套件的安裝

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>疑難排解-虛擬環境

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Python 開發人員中心](/develop/python/)。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)





 
