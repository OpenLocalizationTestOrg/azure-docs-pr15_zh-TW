<properties 
    pageTitle="Azure Active Directory 與連線的 Visual Studio 服務 （MVC 專案） 的 [快速入門 |Microsoft Azure" 
    description="如何開始使用 MVC 專案中的 Azure Active Directory 連線到或建立使用 Visual Studio Azure AD 連線服務之後" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Azure Active Directory 與 Visual Studio 快速入門連線服務 （MVC 專案）

> [AZURE.SELECTOR]
> - [快速入門](vs-active-directory-dotnet-getting-started.md)
> - [發生什麼事](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>要求存取控制站的驗證 

在專案中的所有控制站已裝飾有**授權**的屬性。 這個屬性會要求使用者，以存取這些控制站之前進行驗證。 若要允許控制器匿名存取，請從控制器移除這個屬性。 如果您想要更細緻的層級設定權限，請至每一種方法，需要授權，而不是將其套用至控制器類別套用的屬性。
 
##<a name="adding-signin--signout-controls"></a>新增登入 / 登出控制項 

若要新增至您的檢視登入/登出控制項，您可以將功能新增至您檢視的其中一個使用**_LoginPartial.cshtml**部分檢視。 以下是新增至標準**_Layout.cshtml**檢視的功能範例。 （請注意中使用類別瀏覽列摺疊 div 的最後一個項目）︰

<pre>
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset="utf-8" /&gt; 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
            &lt;div class="container"&gt; 
                &lt;div class="navbar-header"&gt; 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
                &lt;div class="navbar-collapse collapse"&gt; 
                    &lt;ul class="nav navbar-nav"&gt; 
                        &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
                    &lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class="container body-content"&gt; 
            @RenderBody() &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;&amp;copy;@DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[深入瞭解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
