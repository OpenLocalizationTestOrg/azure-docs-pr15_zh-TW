由於進行中的開發、 Android SDK 版本安裝在 Android Studio 中可能不符合程式碼中的版本。 在本教學課程中參考 Android SDK 是撰寫的版本 23 最新版時。 顯示 SDK 的新版本，以及我們建議您使用最新的版本，可能會增加，版本號碼。

版本不相符的兩個徵狀︰

1. 當您建立或重建專案時，您可能會收到 Gradle 錯誤訊息，例如 「**找不到目標 Google Inc.:Google APIs:n**」。

2. 標準的 Android 物件應該解決的程式碼根據`import`陳述式可能會產生錯誤訊息。

如果任一種出現時，版本的 Android SDK 安裝在 Android Studio 中可能不符合 SDK 的目標下載的專案。  若要檢查的版本，請進行下列變更︰


1. 在 Android Studio 中，按一下 [**工具** => **Android** => **SDK 管理員**。 如果您尚未安裝最新版的 SDK 平台，請按一下 [安裝。 記下，版本號碼。

2. 在 [專案總管] 索引標籤的 [ **Gradle 指令碼**，開啟的檔案**build.gradle (modeule︰ 應用程式)**。 確定**compileSdkVersion**和**buildToolsVersion**已設定為安裝最新 SDK 版本。 標記可能看起來像這樣︰
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. Android Studio 專案總管] 中以滑鼠右鍵按一下專案節點，選擇 [**內容**]，然後在左欄中選擇 [ **Android**。 確定**專案建立目標**設定為**targetSdkVersion**相同 SDK 的版本。

4. 在 Android Studio 中，資訊清單的檔案不會再用於指定目標 SDK 和最小的 SDK 版本，跟蝕使用大小寫。
