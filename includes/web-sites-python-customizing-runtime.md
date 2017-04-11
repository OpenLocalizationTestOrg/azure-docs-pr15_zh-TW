Azure 會決定使用虛擬環境中使用下列優先順序 Python 的版本︰

1. 指定 runtime.txt 的根資料夾中的版本
1. Python 設定 web 應用程式設定中所指定的版本 (**設定** > web 應用程式在 Azure 入口網站**應用程式設定**刀)
1. 如果未指定任何以上，python 2.7 是預設值

有效的值的內容 

    \runtime.txt

為︰

- python 2.7
- python 3.4

如果微版本指定 （第三個位數），則會忽略。
