---
title: U-SQL ジョブのデバッグ - Azure Data Lake Tools for Visual Studio Code
description: Azure Data Lake Tools for Visual Studio Code を使用して U-SQL　ジョブをローカルで実行およびデバッグを行う方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 07/14/2017
ms.openlocfilehash: 194fe3494915be9f6784e1596e647885634817ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97969012"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>U-SQL を実行し、Visual Studio Code のローカルでデバッグする
このアーティクルは、ローカルの開発コンピューター上で U-SQL ジョブを実行して、初期のコーディング フェーズを迅速化したり、Visual Studio Code でローカルにコードをデバッグしたりする方法について説明します。 Azure Data Lake Tools for Visual Studio Code に関する手順については、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」をご覧ください。

U-SQL ジョブをローカルで実行したり U-SQL をローカルでデバッグするアクションは、Azure Data Lake Tools for Visual Studio の Windows インストールでのみサポートされています。 この機能は、macOS および Linux ベースのオペレーティング システム上のインストールではサポートされていません。

## <a name="set-up-the-u-sql-local-run-environment"></a>U-SQL ローカル実行環境の設定

1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Download Local Run Package**」と入力してパッケージをダウンロードします。  

   ![ADL LocalRun Dependency パッケージのダウンロード](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. **[出力]** ウィンドウに表示されるパスから依存関係パッケージを探し、BuildTools をインストールして Win10SDK 10240 をインストールします。 パスの例を次に示します。  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![依存関係パッケージを探す](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 **BuildTools** をインストールするには、LocalRunDependency フォルダーにある visualcppbuildtools_full.exe をクリックして、ウィザードの指示に従います。   

    ![BuildTools のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 **Win10SDK 10240** をインストールするには、LocalRunDependency/Win10SDK_10.0.10240_2 フォルダーにある sdksetup.exe をクリックして、ウィザードの指示に従います。  

    ![Win10SDK 10240 のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. 環境変数を設定します。 **SCOPE_CPP_SDK** 環境変数を次のように設定します。  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>ローカル実行サービスを開始してローカル アカウントに U-SQL ジョブを送信する 
初めてのユーザーで、[U-SQL ローカル実行環境を設定](#set-up-the-u-sql-local-run-environment)していない場合は、**ADL: Download Local Run Package** を使用して、ローカルの実行パッケージをダウンロードします。

1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Start Local Run Service**」と入力します。   
2. 初回使用時は、 **[同意する]** を選択し、マイクロソフト ソフトウェア ライセンス条項に同意します。 

   ![マイクロソフト ソフトウェア ライセンス条項に同意](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. cmd コンソールが表示されます。 初回使用時は「**3**」と入力し、データの入力と出力に使用するローカル フォルダーのパスを決める必要があります。 バックスラッシュでパスを定義できなかった場合は、スラッシュを使用してください。 その他のオプションについては、既定値を使用できます。

   ![Data Lake Tools for Visual Studio Code のローカル実行 cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Submit Job**」と入力し、 **[Local]\(ローカル\)** を選択してローカル アカウントにジョブを送信します。

   ![Data Lake Tools for Visual Studio Code のローカルの選択](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. ジョブを送信すると、その詳細を確認できます。 送信の詳細を確認するには、 **[出力]** ウィンドウで **[jobUrl]** を選択します。 cmd コンソールからジョブの送信ステータスを確認することもできます。 さらに詳しいジョブの情報が必要な場合は、cmd コンソールで「**7**」と入力します。

   ![Data Lake Tools for Visual Studio Code ローカル実行の出力](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code ローカル実行の cmd ステータス](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>U-SQL ジョブのローカル デバッグを開始する  
初めてのユーザーは、次の手順を実行します。

1. [U-SQL ローカル実行環境を設定](#set-up-the-u-sql-local-run-environment)していない場合は、**ADL: Download Local Run Package** を使用して、ローカルの実行パッケージをダウンロードします。
2. .NET Core SDK 2.0 をまだインストールしていない場合は、メッセージ ボックスの提示に従ってインストールします。
 
  ![リマインダーが Dotnet をインストールする](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Visual Studio Code 向けの C# をまだインストールしていない場合は、メッセージ ボックスの提示に従ってインストールします。   **[インストール]** をクリックして続行し、VSCode を再起動します。

![リマインダーによる C# のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

ローカル デバッグを実行するには、次の手順に従います。
  
1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Start Local Run Service**」と入力します。 cmd コンソールが表示されます。 **DataRoot** が設定されていることを確認します。
2. C# コードビハインドにブレークポイントを設定します。
3. スクリプト エディターに戻り、右クリックして **[ADL:Local Debug]** を選択します。
    
   ![Data Lake Tools for Visual Studio Code のローカル実行結果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)
* [VSCode で Azure Data Lake Analytics の Python、R、および CSharp を使用して U-SQL を開発する](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) カタログの使用](./data-lake-analytics-u-sql-get-started.md)
