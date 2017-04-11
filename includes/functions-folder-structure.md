
針對您所有的指定的函數應用程式中的函數的程式碼放在包含主機設定檔的根資料夾及一或多個子資料夾，每個包含不同的函數，如下列範例所示的程式碼

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

*Host.json*檔案包含一些執行階段特定設定，位於 [函數] 應用程式的根資料夾中。 設定所提供的資訊，請參閱[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) WebJobs.Script 存放庫 wiki 中。

每個函數已包含一或多個程式碼檔案、 function.json 設定以及其他相依性的資料夾。