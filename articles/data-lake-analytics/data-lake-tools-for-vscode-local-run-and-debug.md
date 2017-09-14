
---
title: "Azure Data Lake Tools: Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使用してローカル実行とローカル デバッグを行う方法について説明します。"
Keywords: "VScode,Azure Data Lake Tools,ローカル実行,ローカル デバッグ,ストレージ ファイルのプレビュー,ストレージ パスにアップロード"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 367e4ba792f83d6ee246208306e4c09b69cb49ef
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ

## <a name="prerequisites"></a>前提条件
この作業を始める前に、次の前提条件を満たしていることを確認してください。
- Azure Data Lake Tool for Visual Studio Code。 詳しくは、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」を参照してください。
- Visual Studio Code 用の C# (U-SQL ローカル デバッグを実行する場合)。

   ![Data Lake Tools for Visual Studio Code に C# をインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > 現在、U-SQL のローカル実行およびデバッグ機能は Windows ユーザーのみをサポートします。 


## <a name="set-up-the-u-sql-local-run-environment"></a>U-SQL ローカル実行環境の設定

1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL: Download LocalRun Dependency**」と入力してパッケージをダウンロードします。  

   ![ADL LocalRun Dependency パッケージのダウンロード](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. **[出力]** ウィンドウに表示されるパスから依存関係パッケージを探し、BuildTools をインストールして Win10SDK 10240 をインストールします。 パスの例を次に示します。  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![依存関係パッケージを探す](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   a. ウィザード画面の指示に従って BuildTools をインストールします。   

  ![BuildTools のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   b. ウィザード画面の指示に従って Win10SDK 10240 をインストールします。  

  ![Win10SDK 10240 のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. 環境変数を設定します。 **SCOPE_CPP_SDK** 環境変数を次のように設定します。  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. 環境変数の設定を有効にするために、OS を再起動します。  

   ![SCOPE_CPP_SDK 環境変数がインストールされていることを確認](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>ローカル実行サービスを開始してローカル アカウントに U-SQL ジョブを送信する 
初回使用時で、ADL: Download LocalRun Dependency パッケージがまだインストールされていない場合は、パッケージをダウンロードするように求められます。
1. Ctrl + Shift + P キーを押してコマンド パレットを選択し、「**ADL: Start Local Run Service**」と入力します。
2. 初回使用時は、**[同意する]** を選択し、マイクロソフト ソフトウェア ライセンス条項に同意します。 

   ![マイクロソフト ソフトウェア ライセンス条項に同意](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. cmd コンソールが表示されます。 初回使用時は「**3**」と入力し、データの入力と出力に使用するローカル フォルダーのパスを決める必要があります。 その他のオプションについては、既定値を使用できます。 

   ![Data Lake Tools for Visual Studio Code のローカル実行 cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL: Submit Job**」と入力して **[Local]** を選択し、ローカル アカウントにジョブを送信します。

   ![Data Lake Tools for Visual Studio Code のローカルの選択](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. ジョブを送信すると、その詳細を確認できます。 送信の詳細を確認するには、**[出力]** ウィンドウで **[jobUrl]** を選択します。 cmd コンソールからジョブの送信ステータスを確認することもできます。 さらに詳しいジョブの情報が必要な場合は、cmd コンソールで「**7**」と入力します。

   ![Data Lake Tools for Visual Studio Code ローカル実行の出力](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code ローカル実行の cmd ステータス](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>U-SQL ジョブのローカル デバッグを開始する  
初回使用時で、ADL: Download LocalRun Dependency パッケージがまだインストールされていない場合は、パッケージをダウンロードするように求められます。
  
1. Ctrl + Shift + P キーを押してコマンド パレットを選択し、「**ADL: Start Local Run Service**」と入力します。 cmd コンソールが表示されます。 **DataRoot** が設定されていることを確認します。
3. C# コードビハインドにブレークポイントを設定します。
4. スクリプト エディターに戻り、Ctrl + Shift + P キーを押してコマンド コンソールを開き、「**Local Debug**」と入力してローカル デバッグ サービスを開始します。

![Data Lake Tools for Visual Studio Code のローカル実行結果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>次のステップ
- Data Lake Tools for Visual Studio Code の使用については、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」を参照してください。
- Data Lake Analytics の概要については、[Azure Data Lake Analytics の使用開始に関するチュートリアル](data-lake-analytics-get-started-portal.md)のページを参照してください。
- Data Lake Tools for Visual Studio については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

