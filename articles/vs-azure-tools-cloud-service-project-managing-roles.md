<properties
   pageTitle="管理角色 Azure 雲端服務以 Visual Studio 專案 |Microsoft Azure"
   description="瞭解如何新增新的角色至 Azure 雲端服務專案或移除現有的角色，使用 Visual Studio。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>使用 Visual Studio Azure 雲端服務專案中的管理角色

建立 Azure 雲端服務專案後，您可以新增新的角色，或移除現有的角色。 您也可以匯入的現有專案，並將它轉換成角色。 例如，您可以匯入 ASP.NET web 應用程式，並將其指定 web 角色。

## <a name="adding-or-removing-roles"></a>新增或移除角色

**若要新增的角色**

在**方案總管**中開啟雲端服務專案中的**角色**] 節點的快顯功能表，然後選擇 [**新增]**。 您可以從目前的方案中選取現有的網頁角色或工作者角色，或建立新 web 或工作者角色專案。 或者，您可以選取適當的專案，例如 ASP.NET web 應用程式專案，並將它與角色專案關聯。

**若要移除的角色關聯**

在 [**角色**] 節點的雲端服務專案總管] 中，開啟移除，然後選擇 [**移除**角色的捷徑功能表。

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>移除並加入您的雲端服務中的角色

如果您移除雲端服務專案中的角色，但稍後決定要將該角色新增回專案，就會新增僅角色宣告及基本的屬性，例如結束點和診斷資訊。 沒有其他資源或參照會新增至 ServiceDefinition.csdef 檔案或 ServiceConfiguration.cscfg 的檔案。 如果您想要新增這項資訊，您必須手動將其新增至這些檔案。

例如，您可能會移除 web 服務角色，而您稍後決定要將此角色加回您的方案。 如果您執行這個動作時，會發生錯誤。 若要避免此錯誤，您必須新增`<LocalResources>`成 ServiceDefinition.csdef 檔顯示下列 XML 中的項目。 使用您新增到專案的名稱屬性的組件為 web 服務角色名稱**<LocalStorage>**項目。 在此範例中的 web 服務角色名稱是**WCFServiceWebRole1**。

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>後續步驟

進一步瞭解如何設定 Visual Studio 中的角色，請閱讀[設定 Visual Studio Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。
