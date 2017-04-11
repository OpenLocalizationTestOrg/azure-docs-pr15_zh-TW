<properties
   pageTitle="了解服務布料的轉印圖樣應用程式和服務的安全性原則 |Microsoft Azure"
   description="概略瞭解如何在 [系統及安全性本機帳戶，包括應用程式需要執行一些權限的動作，則在開始之前的 SetupEntry 點底下執行服務布料的轉印圖樣的應用程式"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>設定您的應用程式的安全性原則
Azure 服務架構提供安全叢集不同的使用者帳戶執行的應用程式的能力。 服務布料的轉印圖樣也會保護時所使用的應用程式的部署使用者帳戶，例如檔案、 目錄及憑證的資源。 如此可讓執行應用程式，即使是在共用主控環境中，從另一個安全。 

根據預設，服務布料的轉印圖樣應用程式執行 Fabric.exe 程序執行的帳號] 之下。 服務布料的轉印圖樣也會提供這項功能可以執行本機的使用者帳戶或指定應用程式的資訊清單內的本機系統帳戶的應用程式。 支援的本機系統帳戶類型是**LocalUser**、**網路服務**、 **LocalService**及**LocalSystem**。

 當您使用安裝程式的資料中心裡的 Windows Server 執行服務布料的轉印圖樣，您可以使用 Active Directory (AD) 網域的帳戶。

使用者群組可以定義，並建立一或多位使用者可以新增至每個群組在一起管理。 當有不同的服務項目點的多個使用者，而且他們必須擁有特定常見的權限，可在群組層級，這是很有用。

## <a name="configure-the-policy-for-service-setupentrypoint"></a>設定服務 SetupEntryPoint 原則

[應用程式模型](service-fabric-application-model.md)中所述**SetupEntryPoint**是執行服務布料的轉印圖樣 （通常是在*網路服務*的帳戶） 作為任何其他項目點之前相同的認證與權限的進入點。 指定**進入點**可執行檔是通常長服務主機有個別的設定進入點可避免使用高的權限執行服務主機可執行檔延伸一段時間。 **SetupEntryPoint**結束成功後，會執行可執行檔指定**進入點**。 監視並重新啟動，產生的程序**SetupEntryPoint**，以再次開頭，如果時結束，或當機。

以下是簡易服務的資訊清單範例，顯示 SetupEntryPoint 和服務的主要進入點。

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>設定原則使用本機帳戶

設定服務已安裝程式進入點之後，您可以變更下執行應用程式資訊清單中的安全性權限。 Followin 範例會示範如何設定服務使用者系統管理員帳戶權限底下執行。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

首先，建立**原則**] 區段的使用者名稱，例如 SetupAdminUser。 這表示使用者是系統管理員群組的成員。

接下來， **ServiceManifestImport** ] 區段下，設定原則，將這個主體套用至**SetupEntryPoint**。 這就是告訴服務布料的轉印圖樣執行**MySetup.bat**檔案時，它應該是 RunAs 具有管理員權限。 假設您有*不*會套用原則的主要進入點到、 系統**網路服務**帳戶] 下，執行**MyServiceHost.exe**中的程式碼。 這是預設帳戶當作來執行所有服務進入點。

現在就讓我們現在加入檔案 MySetup.bat Visual Studio 專案，若要測試系統管理員權限。 在 Visual Studio 中，以滑鼠右鍵按一下該服務的專案，並新增稱為 MySetup.bat 檔案。

接下來，請確定 MySetup.bat 檔案會包含在服務套件。 根據預設，即表示無法。 選取檔案，以取得快顯功能表中，以滑鼠右鍵按一下，選擇 [**內容]**。 在 [屬性] 對話方塊中，確定，**複製到輸出目錄**設定為 [**更新**。 請參閱以下的螢幕擷取畫面。

![Visual Studio CopyToOutput SetupEntryPoint 批次檔案][image1]

現在開啟 MySetup.bat 檔案，並新增下列命令︰

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

接下來，建置，及部署至本機開發叢集的解決方案。  一旦啟動服務，如下圖所示，在服務布料的轉印圖樣總管] 中，您可以看到 MySetup.bat 成功的兩種方式。 開啟的 PowerShell 命令提示字元與類型︰

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

然後，記下的節點服務的部署及啟動服務布料的轉印圖樣總管] 中，例如節點 2 的位置的名稱。 接下來，請瀏覽至應用程式的執行個體的工作資料夾，找出 out.txt 檔案顯示**TestVariable**的值。 範例，如果這項服務已部署至節點 2，然後您可以前往**MyApplicationType**此路徑︰

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>設定原則使用本機系統帳戶
通常最好執行啟動指令碼使用本機系統帳戶，而不是系統管理員帳戶，如下圖所示的前面。 執行 RunAs 原則系統管理員的身分通常是無法運作以及由於電腦有使用者存取控制預設為啟用。 在這種情況下，**建議您以 LocalSystem 執行 SetupEntryPoint，而不是本機的使用者新增至管理員群組**。 下列範例顯示設定來執行以 LocalSystem SetupEntryPoint。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>啟動從 SetupEntryPoint 的 PowerShell 命令
若要執行 PowerShell **SetupEntryPoint**點，您可以執行**PowerShell.exe**批次檔案 PowerShell 檔案中。 首先，將 PowerShell 檔案新增至服務專案，例如**MySetup.ps1**。 設定 [*更新*] 屬性，讓檔案也會包含在服務套件，請記得。 下列範例顯示的範例批次檔案，以啟動稱為 MySetup.ps1，設定系統環境變數，稱為**TestVariable**PowerShell 檔案。


若要啟動 PowerShell 檔案 MySetup.bat。

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

在 PowerShell 檔案中，新增下列設定系統環境變數︰

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**附註︰**依預設批次檔案執行時它查看應用程式稱為**工作**檔案的資料夾。 在此情況下，MySetup.bat 執行時，我們希望此相同的資料夾，這是應用程式的**程式碼套件**資料夾中尋找 MySetup.ps1。 若要變更這個資料夾中，設定工作資料夾，如下所示。

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>使用主控台重新導向本機偵錯
有時很有用，查看執行指令碼偵錯主控台輸出。 才能執行這項操作，您可以設定主控台重新導向原則，它將輸出檔案。 輸出檔案會寫入稱為**記錄**節點部署及執行 （請參閱尋找先前範例中的位置） 應用程式的位置上的 [應用程式] 資料夾。

**附註︰ 永不**生產環境中部署，因為這會影響應用程式容錯移轉應用程式中使用主控台重新導向原則。 **僅**使用此本機開發和偵錯。  

下列範例顯示設定 FileRetentionCount 值主控台重新導向。

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

如果您現在會變更 MySetup.ps1 檔案撰寫**回應**] 命令，則會將寫入偵錯的輸出檔案。

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**一旦您已經將您的指令碼偵錯，立即移除此主控台重新導向原則**

## <a name="configure-policy-for-service-code-packages"></a>設定原則服務代碼套件 
在上述步驟中，您會看到如何套用 SetupEntryPoint RunAs 原則。 讓我們來看一點探討如何建立不同的原則，可套用服務原則。

### <a name="create-local-user-groups"></a>建立本機使用者群組
可定義並建立的使用者群組允許都會新增至群組的一或多位使用者。 如果有不同的服務項目點的多個使用者，而且他們必須擁有特定常見的權限，可在群組層級，這是特別有用。 下列範例會顯示稱為**LocalAdminGroup**具有管理員權限本機群組。 兩個使用者，Customer1 和 Customer2，進行此本機群組的成員。

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>建立本機的使用者
您可以建立可用於安全服務應用程式中的本機使用者。 指定**LocalUser**帳戶類型時的應用程式顯示的 [原則] 區段中，服務布料的轉印圖樣會建立本機的使用者帳戶電腦應用程式部署的位置上。 根據預設，這些帳戶沒有與應用程式顯示 （例如，在下面的範例中的 Customer3） 中所指定的相同名稱。 不過，它們動態產生，並已隨機的密碼。

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>服務代碼套件指派原則
**ServiceManifestImport** **RunAsPolicy**區段指定原則的節，一應該用來執行程式碼套件的帳戶。 它也將服務資訊清單中的程式碼套件與 [原則] 區段中的使用者帳戶關聯。 您可以指定此設定或主要進入點，或者您可以指定`All`套用到兩者。 下列範例顯示套用不同的原則︰

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

如果未指定**EntryPointType** ，預設會設定為 EntryPointType = 「 主要 」。 指定**SetupEntryPoint**時，特別是您想要執行系統帳戶特定高權安裝作業。 之下較低權限的帳戶，可以執行的實際服務代碼。

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>將預設的原則套用至所有服務的程式碼套件
**DefaultRunAsPolicy**區段用來指定預設的使用者帳戶沒有已定義之特定**RunAsPolicy**的所有程式碼套件。 大部分的應用程式使用的服務資訊清單中指定的程式碼套件需要執行相同的使用者，如果應用程式只可以定義預設 RunAs 原則的使用者帳戶。 下列範例會指定是否程式碼套件沒有指定**RunAsPolicy** ，程式碼套件應該執行下**MyDefaultAccount**指定原則] 區段中。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>使用 Active Directory 網域群組或使用者
服務布料的轉印圖樣與安裝程式安裝在 Windows Server 上的可以執行 [認證] 底下的 Active Directory (AD) 使用者或群組帳戶的服務。 注意︰ 這是 Active Directory 內部部署在您的網域，不是與 Azure Active Directory (AAD)。 藉由使用網域的使用者或群組，您可以存取其他網域 （例如，檔案共用） 中的資源授與權限。

下列範例會顯示使用網域密碼加密使用憑證，稱為*MyCert*呼叫*TestUser* AD 使用者。 您可以使用`Invoke-ServiceFabricEncryptText`Powershell 命令建立私人的密碼文字。 請參閱本文[管理服務布料的轉印圖樣應用程式中的機密資料](service-fabric-application-secret-management.md)的詳細資料。 私密金鑰的憑證解密密碼，必須先部署到-頻方法 （在這是資源管理員透過 Azure) 中的本機電腦。 時服務布料的轉印圖樣有服務套件部署到電腦，, 則無法解密密碼及使用者名稱，以及驗證方法 AD 執行 [那些認證] 底下。

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>指派 SecurityAccessPolicy HTTP 和 HTTPS 的端點
如果您將 RunAs 原則套用至服務，服務資訊清單宣告 HTTP 的通訊協定的端點資源，您必須指定**SecurityAccessPolicy** ，以確保配置給這些端點的連接埠都正確存取控制 RunAs 使用者帳戶執行服務的列。 否則， **http.sys**沒有存取服務，並從用戶端取得通話失敗。 Followin 範例適用於 Customer3 帳戶端點稱為**ServiceEndpointName**，讓它的完整存取權限。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

HTTPS 端點，您也可以表示若要返回 [用戶端憑證的名稱。 您可以使用**EndpointBindingPolicy**，在應用程式資訊清單中的 [憑證] 區段中定義的憑證。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>完整的應用程式的資訊清單範例
下列應用程式資訊清單顯示許多不同的設定︰

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

* [瞭解此應用程式模型](service-fabric-application-model.md)
* [指定服務資訊清單中的資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
