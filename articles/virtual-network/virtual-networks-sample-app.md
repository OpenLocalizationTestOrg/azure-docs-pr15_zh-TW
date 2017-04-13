<properties
   pageTitle="範例使用與安全性邊界環境的應用程式 |Microsoft Azure"
   description="部署測試流量流向的情況下建立 DMZ 之後這個簡單的 web 應用程式"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# <a name="sample-application-for-use-with-security-boundary-environments"></a>範例使用與安全性邊界環境的應用程式

[返回 [安全性邊界最佳作法頁面][HOME]

下列 PowerShell 指令碼可以 IIS01 及 AppVM01 伺服器上安裝和設定非常簡單的 web 應用程式顯示 html 頁面從前端 IIS01 伺服器的後端 AppVM01 伺服器內容與本機執行。

此將應用程式簡單的測試環境提供許多 DMZ 範例及如何變更端點、 NSGs、 UDR，以及防火牆規則可以效果流量。

## <a name="firewall-rule-to-allow-icmp"></a>若要允許 ICMP 防火牆規則
若要允許 ICMP (Ping) 的流量任何 Windows VM 您可以執行這個簡單的 PowerShell 陳述式。 這可讓您更容易測試和疑難排解藉由使用偵測 （ping） 通訊協定通過 windows 防火牆 （適用於大部分的 Linux distros ICMP 預設為開啟)。

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**附註︰**如果您是使用下方的指令碼，此防火牆規則是第一個陳述式。

## <a name="iis01---web-application-installation-script"></a>IIS01-Web 應用程式安裝指令碼
將這個指令碼︰

1.  開啟 IMCPv4 (Ping) 上的本機伺服器 windows 防火牆更容易測試
2.  安裝 IIS 和.Net Framework v4.5
3.  建立 ASP.NET 網頁和 Web.config 檔案
4.  變更預設應用程式集區輕鬆存取檔案
5.  將匿名使用者設定為您的管理員帳戶及密碼

RDP 已將 IIS01 時，應該本機執行這個 PowerShell 指令碼。

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01-檔案伺服器安裝指令碼
這個指令碼會設定後端這個簡單的應用程式。 將這個指令碼︰

1.  開啟 IMCPv4 (Ping) 上的防火牆，以更容易進行測試
2.  建立新的目錄
3.  建立要從遠端存取網頁上述文字檔案
4.  設定目錄和檔案，以匿名允許存取權限
5.  關閉 [允許此伺服器的更容易瀏覽 IE 增強安全性 

>[AZURE.IMPORTANT] **最佳作法**︰ 永遠不會關閉 IE 增強安全性生產伺服器上，加上最好通常不正確的實際執行伺服器瀏覽網頁。 此外，開啟匿名存取檔案共用上是不正確的概念，但完成以下的簡單。

RDP 已將 AppVM01 時，應該本機執行這個 PowerShell 指令碼。 PowerShell，才能執行以系統管理員身分，以確保順利執行。
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## <a name="dns01---dns-server-installation-script"></a>DNS01-DNS 伺服器安裝指令碼
有任何指令碼包含在此範例應用程式設定 DNS 伺服器。 如果測試的防火牆規則，NSG 或 UDR 必須包含 DNS 流量，以手動方式，設定需要 DNS01 伺服器。 這兩個範例的網路設定 xml 檔案包含 DNS01 為主要的 DNS 伺服器及公用的 DNS 伺服器裝載的層級 3 做為備份的 DNS 伺服器。 階層 3 DNS 伺服器想實際用於非本機流量的 DNS 伺服器而 DNS01 與設定，沒有本機 DNS，就會發生。

<!--Link References-->
[HOME]: ../best-practices-network-security.md
