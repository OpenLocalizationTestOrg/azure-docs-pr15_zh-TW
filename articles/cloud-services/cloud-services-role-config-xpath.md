<properties 
pageTitle="雲端服務角色 config XPath 速查表 |Microsoft Azure" 
description="各種 XPath 設定您可以使用雲端服務角色組態中與環境變數公開設定。" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>以與 XPath 環境變數的方式公開角色設定的設定

在雲端服務工作者或 web 角色服務定義檔案中，您可以為環境變數公開執行階段設定值。 支援下列 XPath 值 （由對應至 API 值）。

這些 XPath 值也可透過[Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx)文件庫。 

## <a name="app-running-in-emulator"></a>在模擬器中執行的應用程式

指出應用程式會在模擬器中執行。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| 程式碼  | var x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>部署識別碼

擷取執行個體的部署識別碼。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| 程式碼  | var deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>角色識別碼 

擷取執行個體目前的角色識別碼。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| 程式碼  | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>更新的網域

擷取更新網域的執行個體。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| 程式碼  | var 「 ud 」 = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>故障網域

擷取故障網域的執行個體。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| 程式碼  | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>角色名稱

擷取角色名稱的執行個體。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| 程式碼  | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>組態設定

擷取指定的組態設定的值。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| 程式碼  | var 設定 = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>本機存放區路徑

擷取執行個體的本機存放區路徑。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| 程式碼  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1")。RootPath; |


## <a name="local-storage-size"></a>本機的儲存空間大小

擷取執行個體本機的儲存空間的大小。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| 程式碼  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1")。MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>結束點通訊協定 

擷取執行個體的端點通訊協定。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| 程式碼  | var 連接埠正常 = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 」]。通訊協定。 |

## <a name="endpoint-ip"></a>結束點 IP

取得指定的端點的 IP 位址。

| 類型 | 範例 |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| 程式碼  | var 地址 = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 」]。IPEndpoint.Address |

## <a name="endpoint-port"></a>端點連接埠 

擷取執行個體的端點連接埠。

| 類型  | 範例 |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| 程式碼  | var 連接埠 = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 」]。IPEndpoint.Port; |





## <a name="example"></a>範例

以下是建立名為環境變數啟動工作工作者角色的範例`TestIsEmulated`設定為[@emulatedxpath 值](#app-running-in-emulator)。 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>後續步驟

進一步瞭解[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg)檔案。

建立[ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg)套件。

啟用[遠端桌面](cloud-services-role-enable-remote-desktop.md)的角色。
