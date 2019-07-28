---
title: Azure Data Lake Tools for Visual Studio Code の使用
description: Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509099"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

このアーティクルでは、U-SQL スクリプトを作成、テスト、実行するために Azure Data Lake Tools for Visual Studio Code (VS Code) を使用する 方法について説明します。 この情報は、次のビデオにも含まれています。

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>前提条件

Azure Data Lake Tools for VS Code では、Windows、Linux、および macOS がサポートされます。 U-SQL のローカル実行とローカル デバッグは Windows でのみ動作します。

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS および Linux の場合 :
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake Tools をインストールする

前提条件の各項目のインストールが完了したら、Azure Data Lake Tools for VS Code をインストールできます。

**Azure Data Lake Tools をインストールするには**

1. Visual Studio Code を開きます。
2. 左側のウィンドウで、 **[拡張機能]** を選択します。 検索ボックスに「**Azure Data Lake**」と入力します。
3. **[Azure Data Lake Tools]** の横にある **[インストール]** を選択します。 

   ![Data Lake Tools をインストールするための選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   数秒後、 **[インストール]** ボタンが **[再度読み込む]** に変わります。
4. **[再度読み込む]** を選択して、**Azure Data Lake Tools** 拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 **[拡張機能]** ウインドウに**Azure Data Lake Tools** が表示されます。

 
## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する
新しい .usql ファイルを作成するか、既存の .usql ファイルを開いて拡張機能をアクティブにしてください。 


## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**サンプル スクリプトを開くには**

コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL:Open Sample Script**」と入力します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

**U-SQL プロジェクトのフォルダーを開くには**

1. Visual Studio Code で **[ファイル]** メニューを選択し、 **[フォルダーを開く]** を選択します。
2. フォルダーを指定し、 **[フォルダーの選択]** を選択します。
3. **[ファイル]** メニューを選択し、 **[New]\(新規作成\)** を選択します。 Untitled-1 ファイルがプロジェクトに追加されます。
4. 次のコードを 無題-1 ファイルに入力します：

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルが /output フォルダーに作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。

**U-SQL スクリプトをコンパイルするには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL:Compile Script**」と入力します。 **[出力]** ウィンドウにコンパイル結果が表示されます。 スクリプト ファイルを右クリックし、 **[ADL:Compile Script]** を選択して U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は **[出力]** ウィンドウに表示されます。
 
**U-SQL スクリプトを送信するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL:Submit Job**」と入力します。 スクリプト ファイルを右クリックし、 **[ADL:Submit Job]** を選択することもできます。 

U-SQL ジョブの送信後、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 右側のウィンドウで、ジョブ ビューが表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。 

ジョブ ビューの **[概要]** タブでは、ジョブの詳細を確認できます。 主な機能には、スクリプトの再送信、スクリプトの複製、ポータルで開くなどがあります。 ジョブ ビューの **[データ]** タブでは、入力ファイル、出力ファイル、リソースファイルを参照できます。 ファイルはローカル コンピューターにダウンロードできます。

![ジョブ ビューの概要タブ](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![ジョブ ビューのデータタブ](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**既定のコンテキストの設定**

ファイルのパラメーターを個々に設定していない場合、この設定をすべてのスクリプトファイルに適用する既定のコンテキストを設定することができます。

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL:Set Default Context**」と入力します。 または、スクリプト エディターを右クリックし、 **[ADL:Set Default Context]** を選択します。
3. 使用するアカウント、データベース、およびスキーマを選択します。 この設定は xxx_settings.json 構成ファイルに保存されます。

   ![既定のコンテキストとして設定されたアカウント、データベース、およびスキーマ](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**スクリプト パラメーターの設定**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL:Set Script Parameters**」と入力します。
3. xxx_settings.json ファイルが以下のプロパティでが開かれています：

   - **account**:U-SQL ジョブをコンパイルし、実行するために必要な Azure サブスクリプションの Azure Data Lake Analytics アカウント。 U-SQL ジョブのコンパイルと実行を行う前に、このコンピューター アカウントを構成する必要があります。
   - **database**:自分のアカウントのデータベース。 既定値は **master** です。
   - **schema**:自分のデータベースのスキーマ。 既定値は **dbo** です。
   - **optionalSettings**:
        - **priority**:優先順位の範囲は 1 から 1,000 で、1 が最も高い優先順位です。 既定値は **1,000** です。
        - **degreeOfParallelism**:並列処理の範囲は 1 から 150 です。 既定値は、Azure Data Lake Analytics アカウントで許可される並列処理の最大数です。 

   ![JSON ファイルのコンテンツ](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> 構成の保存後、アカウント、データベース、スキーマ情報が、対応する .usql ファイルの左下隅にあるステータス バーに表示されます (既定のコンテキストを設定していない場合)。

**Git Ignore の設定**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL:Set Git Ignore**」と入力します。

   - VS Code 作業フォルダーに **.gitIgnore** ファイルが存在しない場合、このフォルダーに **.gitIgnore** という名前のファイルが作成されます。 4 つの項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、 **.cache**、**obj**) が既定でファイルに追加されます。 必要な場合は、さらに更新することができます。
   - VS Code 作業フォルダーに **.gitIgnore** ファイルが既に存在する場合、ツールによって4項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、 **.cache**、**obj**) が **.gitIgnore** ファイルに追加されます(ファイルにこの 4 項目が含まれていない場合)。

   ![.gitIgnore ファイル内の項目](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>分離コード ファイル(C Sharp、Python、R) を操作する

Azure Data Lake Tools は、複数のカスタム コードをサポートしています。 手順については、[VS Code で Azure Data Lake Analytics の Python、R、および C Sharp を使用した U-SQL を開発する](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)を参照してください。

## <a name="work-with-assemblies"></a>アセンブリの操作

アセンブリの開発に関する情報については、「[Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

Data Lake Analytics カタログには、Data Lake Tools を使用してカスタム コード アセンブリを登録できます。

**アセンブリを登録するには**

**ADL:Register Assembly** または **ADL:Register Assembly (Advanced)** コマンドを使用して、アセンブリを登録できます。

**ADL:Register Assembly コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL:Register Assembly**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  Data Lake Analytics アカウントを選択します。
5.  データベースを選択します。

ポータルがブラウザーで開かれ、アセンブリの登録プロセスが表示されます。  

**ADL:Register Assembly** コマンドをトリガーする、より便利な方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。 

**ADL:Register Assembly (Advanced) コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL:Register Assembly (Advanced)** 」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  JSON ファイルが表示されます。 アセンブリの依存関係とリソース パラメーターを確認して、必要な場合は編集します。 手順が **[出力]** ウィンドウに表示されます。 アセンブリの登録に進むには、JSON ファイルを保存 (Ctrl + S) します。

    ![アセンブリの依存関係とリソース パラメーターを使用した JSON file](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools は、DLL がアセンブリの依存関係をもつかどうかを自動的に検出します。 依存関係は、検出された後 JSON ファイルに表示されます。 
>- DLL リソース (.txt、.png、.csv など) をアセンブリの登録の一部としてアップロードできます。 

**ADL:Register Assembly (Advanced)** コマンドをトリガーする別の方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。 

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>U-SQL のローカル実行とローカル デバッグをWindows ユーザーで使用する
U-SQL ローカル実行は、Data Lake Analytics にコードを発行する前に、ローカル データをテストし、スクリプトをローカルで検証するものです。 作成したコードを Data Lake Analytics に送信する前に、ローカル デバッグ機能を使用して次のタスクを完了することができます： 
- C# 分離コードをデバッグする。 
- コードをステップ実行する。 
- スクリプトをローカルで検証する。

ローカル実行およびローカル デバッグ機能は Windows 環境でのみ動作し、macOS および Linux ベースのオペレーティング システムではサポートされていません。

ローカル実行とローカル デバッグの手順については、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。


## <a name="connect-to-azure"></a>Azure への接続

Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

<b id="sign-in-by-command"> コマンドを使用して Azure に接続する</b>

1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2.  「**ADL:Login** を選択します。 右下にログイン情報が表示されます。

    ![ログイン コマンドを入力しています](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![サインインと認証についての通知](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  **[Copy & Open]** を選択し、[ログイン Web ページ](https://aka.ms/devicelogin)を開きます。 ボックスにコードを貼り付け、 **[続行]** を選択します。

    ![ログイン Web ページ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  手順に従って Web ページからサインインします。 接続すると、VS Code ウィンドウの左下隅のステータス バーに Azure アカウント名が表示されます。 

> [!NOTE] 
>- Data Lake Tools は、次回サインアウトしない場合、自動的にサインアウトします。
>- アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証の使用をお勧めします。


サインアウトするには、**ADL:Logout** コマンドを入力します。

**エクスプローラーから Azure に接続する**

**AZURE DATALAKE**を展開し、 **Azure へのサインイン**を選択して次の[コマンドを使用して Azure に接続する](#sign-in-by-command)の手順 3 と手順 4に従ってください。

![エクスプ ローラーで「Azure にサインインする」の選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

エクスプ ローラーからサインアウトすることはできません。 サインアウトするには、[コマンドを使用して Azure に接続する](#sign-in-by-command)を参照してください。


## <a name="create-an-extraction-script"></a>抽出スクリプトの作成 
.csv、.tsv、および .txt ファイルの抽出スクリプトは、**ADL:Create EXTRACT Script** コマンドを使用するか、Azure Data Lake エクスプローラーから作成できます。

**コマンドを使用して抽出スクリプトを作成する**

1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Create EXTRACT Script**」と入力します。
2. Azure Storage ファイルの完全なパスを指定し、Enter キーを押します。
3. 1 つのアカウントを選択します。
4. .txt ファイルの場合は、区切り記号を選択してファイルを抽出します。 

![抽出スクリプト作成のプロセス](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

入力に基づいて抽出スクリプトが生成されます。 列を検出できないスクリプトの場合は、次の 2 つのオプションのいずれかを選択します。 それ以外の場合は、1 つのスクリプトのみが生成されます。

![抽出スクリプト作成の結果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**エクスプローラーから抽出スクリプトを作成する**

抽出スクリプトを作成する別の方法は、Azure Data Lake Store または Azure Blob Storage 内の .csv ファイル、.tsv ファイルの右クリック (ショートカット) メニューを使用することです。

![ショートカット メニューの「抽出スクリプトを作成する」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>コマンドを使用して Azure Data Lake Analytics と統合する

Azure Data Lake Analytics リソースにアクセスして、アカウントの一覧表示、メタデータへのアクセス、分析ジョブの表示を実行できます。 

**Azure サブスクリプション内の Azure Data Lake Analytics アカウントを一覧表示するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:List Accounts**」と入力します。 **[出力]** ウィンドウにアカウントが表示されます。

**Azure Data Lake Analytics メタデータにアクセスするには**

1.  Ctrl + Shift + P キーを押し、「**ADL:List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントを選択します。
3.  いずれかの Data Lake Analytics データベースを選択します。
4.  いずれかのスキーマを選択します。 テーブルの一覧が表示されます。

**Azure Data Lake Analytics ジョブを表示するには**
1.  コマンド パレットを開いて (Ctrl + Shift + P)、 **[ADL:Show Job]** を選択します。 
2.  Data Lake Analytics アカウントまたはローカル アカウントを選択します。 
3.  アカウントのジョブ一覧が表示されるまで待ちます。
4.  ジョブ一覧からジョブを選択します。 Data Lake Tools により右側のウィンドウにジョブ ビューが開かれ、VS Code 出力情報を表示します。

    ![Job 一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>コマンドを使用して Azure Data Lake Store と統合する

Azure Data Lake Store 関連コマンドを使用して次のことを実行できます：
 - [Azure Data Lake Store のリソースを閲覧する](#list-the-storage-path) 
 - [Azure Data Lake Store ファイルをプレビューする](#preview-the-storage-file) 
 - VS Code で Azure Data Lake Store に直接ファイルをアップロードする
 - VS Code で Azure Data Lake Store から直接ファイルをダウンロードする

### <a name="list-the-storage-path"></a>ストレージ パスの一覧表示 

**コマンド パレットを使用してストレージ パスを一覧表示するには**

1. スクリプト エディターを右クリックし、 **[ADL:List Path]** を選択します。
2. 一覧でフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。) 
3. Data Lake Analytics アカウントを選択します。
4. ストレージ フォルダーのパスを参照もしくは入力します (/output/ など)。  

コマンド パレットに入力に基づくパス情報の一覧が表示されます。

![Storage パスの結果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

相対パスの一覧を表示するさらに便利な方法は、ショートカット メニューを使用することです。

**ショートカット メニューを使用してストレージ パスを一覧表示する**

パス文字列を右クリックし、 **[List Path]** を選択します。

![ショートカット メニューの「パスの一覧を表示する」](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>ストレージ ファイルのプレビュー

1. スクリプト エディターを右クリックし、 **[ADL:Preview File]** を選択します。
2. Data Lake Analytics アカウントを選択します。 
3. Azure Storage ファイルのパス (/output/SearchLog.txt など) を入力します。 

このファイルは、VS Code で開きます。

![ストレージファイルのプレビュー手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

ファイルをプレビューする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。 

### <a name="upload-a-file-or-folder"></a>ファイルまたはフォルダーをアップロード

1. スクリプト エディターを右クリックし、 **[ファイルのアップロード]** または **[フォルダーをアップロード]** を選択します。
2. 1 つ以上のファイル ( **[ファイルのアップロード]** を選択した場合) またはフォルダー全体 ( **[フォルダーをアップロード]** を選択した場合) を選択します。 **[アップロード]** を選択します。 
3. 一覧でストレージフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。) 
4. Data Lake Analytics アカウントを選択します。 
5. ストレージ フォルダーのパスを参照もしくは入力します (/output/ など)。 
6. **[Choose Current Folder]** をクリックして、アップロード先を指定します。

![ファイルまたはフォルダーをアップロードする手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

ファイルをストレージにアップロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスのショートカット メニューを使用する方法があります。

[アップロードの状態の監視](#check-storage-tasks-status)ができます。


### <a name="download-a-file"></a>ファイルをダウンロードする 
ファイルは、コマンド **ADL:Download File** または **ADL:Download File (Advanced)** を使用してダウンロードできます。

**ADL:Download File (Advanced) コマンドを使用してファイルをダウンロードするには**
1. スクリプト エディターを右クリックし、 **[Download File (Advanced)]\(ファイルのダウンロード (詳細)\)** を選択します。
2. VS Code に、JSON ファイルが表示されます。 ファイルのパスを入力して、複数のファイルを同時にダウンロードできます。 手順が **[出力]** ウィンドウに表示されます。 ファイルのダウンロードを続行するには、Ctrl + S キーを押して JSON ファイルを保存します。

    ![ファイルのダウンロード用のパスつき JSON ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**[出力]** ウィンドウに、ファイル ダウンロードの状態が表示されます。

![ダウンロードの状態が表示された出力ウィンドウ](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

[ダウンロードの状態の監視](#check-storage-tasks-status)ができます。

**ADL:Download File コマンドを使用してファイルをダウンロードするには**

1. スクリプト エディターを右クリックし、 **[ファイルのダウンロード]** を選択してから、 **[フォルダーの選択]** ダイアログ ボックスでダウンロード先フォルダーを選択します。
2. 一覧でフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。) 
3. Data Lake Analytics アカウントを選択します。 
4. ストレージ フォルダーのパス (/output/ など) を参照または入力し、 ダウンロードするファイルを選択します。

![ファイルをダウンロードする手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

ストレージファイルをダウンロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスのショートカット メニューを使用する方法があります。

[ダウンロードの状態の監視](#check-storage-tasks-status)ができます。

### <a name="check-storage-tasks-status"></a>ストレージ タスクの状態の確認
アップロードとダウンロードの状態は、ステータス バーに表示されます。 ステータス バーを選択すると、**出力**タブに状態が表示されます。

![ステータス バーと出力の詳細](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>エクスプローラーから Azure Data Lake Analytics と統合する

ログインすると、 **[AZURE DATALAKE]** の下の左側のウインドウに、現在の Azure アカウントに含まれるサブスクリプションがすべて一覧表示されます。 

![Data Lake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics メタデータへのナビゲーション

Azure サブスクリプションの展開。 **U-SQL データベース**ノードの下で、U SQL データベースを参照することができ、**スキーマ**、**資格情報**、**アセンブリ**、**テーブル**、および**インデックス**などのフォルダーが表示されます。

### <a name="data-lake-analytics-metadata-entity-management"></a>Azure Data Lake Analytics メタデータのエンティティ

**U-SQL データベース**を展開します。 データベース、スキーマ、テーブル、テーブル型、インデックス、または統計を作成するには、対応するノードを右クリックして、ショートカット メニューの**作成するスクリプト**を選択します。 開いているスクリプトページで、必要に応じてスクリプトを編集します。 その後、これを右クリックし、 **[ADL:Submit Job]** を選択して、ジョブを送信します。 

アイテムの作成が完了したら、ノードを右クリックし、**最新の情報に更新**を選択してアイテムを表示します。 右クリックして **[削除]** を選択して、アイテムを削除することもできます。

![Data Lake エクスプローラーのショートカットメニューの「作成するスクリプト」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![新しい項目のスクリプトページ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics アセンブリの登録

**[アセンブリ]** ノードを右クリックして、 **[アセンブリを登録]** をクリックしてアセンブリを関連するデータベースに登録できます。

![アセンブリノードのショートカット メニューの「アセンブリの登録」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>エクスプローラーから Azure Data Lake Store と統合する

**Data Lake Store**を参照する：

- フォルダー ノードを右クリックし、ショートカット メニューの **[更新]** 、 **[削除]** 、 **[アップロード]** 、 **[フォルダーをアップロード]** 、 **[相対パスのコピー]** および **[完全パスのコピー]** コマンドを実行できます。

   ![Data Lake エクスプ ローラーのフォルダー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- ファイル ノードを右クリックし、ショートカット メニューのコマンド **[プレビュー]** 、 **[ダウンロード]** 、 **[削除]** 、 **[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、 **[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

   ![Data Lake エクスプ ローラーのファイル ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>エクスプローラーから Azure BLOB ストレージ と統合する

BLOB ストレージの参照:

- BLOB コンテナー ノードを右クリックし、ショートカット メニューのコマンド **[最新の情報に更新]** 、 **[BLOB コンテナーの削除]** 、および **[BLOB のアップロード]** を実行できます。

   ![Blob ストレージの blob コンテナー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- フォルダー ノードを右クリックし、ショートカット メニューのコマンド **[最新の情報に更新]** および **[BLOB のアップロード]** を実行できます。

   ![BLOB ストレージの フォルダー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- ファイル ノードを右クリックし、ショートカット メニューのコマンド **[プレビュー/編集]** 、 **[ダウンロード]** 、 **[削除]** 、 **[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、 **[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

    ![BLOB ストレージの ファイル ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>ポータルで、Data Lake エクスプ ローラーを開く
1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、 **[Open Web Azure Storage Explorer]** を選択します。
3. Data Lake Analytics アカウントを選択します。

Data Lake Tools によって、Azure Portal に Azure Storage パスが表示されます。 そのパスを見つけて Web からファイルをプレビューすることができます。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VS Code では、以下の各機能がサポートされています。

-   **IntelliSense オートコンプリート**:キーワード、メソッド、変数などの項目の付近にポップアップ ウィンドウで修正候補が表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます：

    - Scala データ型
    - 複合データ型
    - 組み込みの UDT
    - .NET コレクションおよびクラス
    - C# 式
    - 組み込みの C# UDF、UDO、UDAAG 
    - U-SQL 関数
    - U-SQL ウィンドウ化関数
 
    ![IntelliSense オブジェクトの種類](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Data Lake Analytics メタデータでの IntelliSense オートコンプリート**:Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。 IntelliSense 機能は、Data Lake Analytics メタデータからオブジェクトを自動的に作成します。 これらのオブジェクトには、データベース、スキーマ、テーブル、ビュー、テーブル値関数、プロシージャ、および C# アセンブリが含まれます。
 
    ![IntelliSense のメタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense エラー マーカー**:Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。 
-   **構文の強調表示**:Data Lake Tools では色を使用して、変数、キーワード、データ型、関数などの項目を区別します。 

    ![さまざまな色で表示された構文](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azure Data Lake Tools for Visual Studio バージョン 2.3.3000.4 以降にアップグレードすることをお勧めしています。 以前のバージョンはダウンロードできなくなり、非推奨になっています。  
   
## <a name="next-steps"></a>次の手順
- [VS Code で Azure Data Lake Analytics の Python、R、および C　Sharp を使用した U-SQL の開発](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
- [チュートリアル:Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [チュートリアル:Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
