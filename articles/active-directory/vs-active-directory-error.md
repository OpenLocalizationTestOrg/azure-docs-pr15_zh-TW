<properties 
    pageTitle="驗證偵測時發生錯誤" 
    description="Active directory 連線精靈偵測不相容的驗證類型" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>驗證偵測時發生錯誤

偵測先前的驗證碼，精靈會偵測不相容的驗證類型。   

##<a name="what-is-being-checked"></a>什麼是存回？

**附註︰**若要正確偵測專案中的上一個驗證碼，必須建立專案。  如果發生此錯誤，而且您沒有在專案中的上一個的驗證碼，重建並再試一次。

###<a name="project-types"></a>專案類型

精靈會檢查您開發讓它可以在右驗證邏輯插入專案的專案類型。  如果有任何控制器衍生自`ApiController`在專案中，project 會被視為 WebAPI 專案。  如果有，衍生自控制站`MVC.Controller`在專案中，project 會被視為 MVC 專案。  精靈不支援其他項目。  目前不支援 WebForms 專案。

###<a name="compatible-authentication-code"></a>相容的驗證碼

精靈也會檢查先前設定精靈，或使用精靈相容的驗證設定。  如果所有的設定，就會被視為重疊的大小寫，精靈會開啟並顯示設定。  如果只有部分設定簡報，請將其視為錯誤的大小寫。

在 MVC 專案，精靈會檢查的下列設定值，從先前使用精靈的結果︰

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

此外，精靈會檢查的下列設定值的 Web API 專案中，從先前使用 「 精靈 」 所導致︰

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>不相容的驗證碼

最後，精靈會嘗試偵測已使用舊版的 Visual Studio 的版本的驗證碼。 如果您收到此錯誤，即表示您的專案包含不相容的驗證類型。 精靈會偵測下列類型的先前版本的 Visual Studio 驗證︰

* Windows 驗證 
* 個別使用者帳戶 
* 組織帳戶 
 

若要偵測 MVC 專案中的 Windows 驗證，精靈會檢查`authentication` **web.config**檔案中的項目。

<pre>
    &lt;設定&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;驗證模式 = 「 Windows 」 /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

若要偵測網路 API 專案中的 Windows 驗證，精靈會尋找`IISExpressWindowsAuthentication`專案的**.csproj**檔案中的項目︰

<pre>
    &lt;專案&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;啟用&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/專案        &gt;
</pre>

若要偵測個別的使用者帳戶驗證，精靈會檢查套件項目的從**Packages.config**檔案。

<pre>
    &lt;封裝&gt;
        <span style="background-color: yellow">&lt;封裝 id="Microsoft.AspNet.Identity.EntityFramework 」 版本 = 「 2.1.0 」 targetFramework = 「 net45 」 /&gt;</span>
    &lt;/套件&gt;
</pre>

若要偵測原來的形式的組織帳戶驗證，精靈會檢查下列項目從**web.config**:

<pre>
    &lt;設定&gt;
        &lt;和 appSettings&gt;
            <span style="background-color: yellow">&lt;新增鍵 = 」 ida︰ 領域 」 值 = 」 * * * 「 /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

若要變更的驗證類型，移除不相容的驗證類型，然後再次執行精靈。

如需詳細資訊，請參閱[Azure ad 驗證案例](active-directory-authentication-scenarios.md)。