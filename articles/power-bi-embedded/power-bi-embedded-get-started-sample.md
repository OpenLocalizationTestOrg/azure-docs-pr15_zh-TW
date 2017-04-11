<properties
   pageTitle="開始使用範例"
   description="Power BI 內嵌，請使用 SDK 將您的商務智慧應用程式的互動式 Power BI 報表"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-power-bi-embedded-sample"></a>開始使用 Power BI 內嵌的範例

使用**Microsoft Power BI 內嵌**，您可以整合 Power BI 報表右將您的網頁或行動應用程式。 本文中，我們會為您介紹**Power BI 內嵌**的快速入門範例。

進一步之前，您可能需要儲存下列資源。 太整合到範例應用程式，然後您自己的應用程式的 Power BI 報表時，他們會協助您。

 -  [範例儀表板 web 應用程式](http://go.microsoft.com/fwlink/?LinkId=761493)
 -  [Power BI 內嵌 API 參照](https://msdn.microsoft.com/library/mt711493.aspx)
 -  [Power BI 內嵌.NET SDK](http://go.microsoft.com/fwlink/?LinkId=746472)（可透過 NuGet）



> [AZURE.NOTE] 您可以設定並執行 Power BI 內嵌的快速入門的範例之前，必須先建立至少有一個**工作區集合**Azure 訂閱中。 若要瞭解如何建立**工作區集合**Azure 入口網站中請參閱[開始使用 Power BI 內嵌](power-bi-embedded-get-started.md)。

## <a name="configure-the-sample-app"></a>設定範例應用程式

現在就讓我們逐步執行設定 Visual Studio 開發環境存取執行範例應用程式所需的元件。

1. 下載並解壓縮 GitHub 上[Power BI 內嵌-整合到 web app 中的報表](http://go.microsoft.com/fwlink/?LinkId=761493)範例。

2. 開啟 Visual Studio**中 embedded.sln** 。 您可能需要在 NuGET 封裝管理員主控台執行**更新套件**的命令，以更新此解決方案中使用的套件。

3. 建置解決方案。

4. 執行**ProvisionSample**主控台應用程式。 在範例主控台應用程式中，您佈建工作區，並匯入 PBIX 檔案。

5. 佈建新的**工作區**，請選擇 5、**佈建現有的工作區集合中的新工作區**。

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. 輸入您的**工作區集合**名稱和**便捷鍵**。 您可以取得這些**Azure 入口網站**中。 若要進一步瞭解如何取得**便捷鍵**，請參閱[檢視 Power BI API 便捷鍵](power-bi-embedded-get-started-sample.md#view-access-keys)中開始使用 Microsoft Power BI 內嵌。

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. 複製並儲存新建立的**工作區識別碼**本文稍後的使用。 建立**工作區 ID**之後，您可以找到它**Azure 入口網站**。

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. 若要 PBIX 檔案匯入您的**工作區**中，選取選項**6。匯入 PBIX 桌面檔案到現有的工作區**。 如果您沒有安裝方便 PBIX 檔案，您可以下載 [零售分析範例 PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547)。

9. 如果出現提示，請輸入您的**資料集**易記的名稱。

您應該會看到的回應，例如︰

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] If your PBIX file contains any direct query connections, run option 7 to update the connection strings.

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app

The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [AZURE.NOTE] This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

|Part|Description
|---|---
|Title| Name of the Report.
|QueryString| A link to the Report ID.

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app

Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## Filter reports embedded in your application

You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  


## See also

- [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
- [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)
