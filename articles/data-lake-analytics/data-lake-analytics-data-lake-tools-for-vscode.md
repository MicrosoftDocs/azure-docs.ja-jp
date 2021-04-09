---
title: Azure Data Lake Tools for Visual Studio Code の使用
description: Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751361"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

このアーティクルでは、U-SQL スクリプトを作成、テスト、実行するために Azure Data Lake Tools for Visual Studio Code (VS Code) を使用する 方法について説明します。 この情報は、次のビデオにも含まれています。

[![ビデオ プレーヤー:Azure Data Lake tools for VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>前提条件

Azure Data Lake Tools for VS Code では、Windows、Linux、および macOS がサポートされます。  U-SQL のローカル実行とローカル デバッグは Windows でのみ動作します。

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS および Linux の場合 :

- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake Tools をインストールする

前提条件の各項目のインストールが完了したら、Azure Data Lake Tools for VS Code をインストールできます。

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake Tools をインストールするには

1. Visual Studio Code を開きます。
2. 左側のウィンドウで、 **[拡張機能]** を選択します。 検索ボックスに「**Azure Data Lake**」と入力します。
3. **[Azure Data Lake Tools]** の横にある **[インストール]** を選択します。

   ![Data Lake Tools をインストールするための選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   数秒後、 **[インストール]** ボタンが **[再度読み込む]** に変わります。
4. **[再度読み込む]** を選択して、**Azure Data Lake Tools** 拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 **[拡張機能]** ウインドウに **Azure Data Lake Tools** が表示されます。

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する

新しい .usql ファイルを作成するか、既存の .usql ファイルを開いて拡張機能をアクティブにしてください。

## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

### <a name="to-open-the-sample-script"></a>サンプル スクリプトを開くには

コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL:Open Sample Script**」と入力します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

### <a name="to-open-a-folder-for-your-u-sql-project"></a>U-SQL プロジェクトのフォルダーを開くには

1. Visual Studio Code で **[ファイル]** メニューを選択し、 **[フォルダーを開く]** を選択します。
2. フォルダーを指定し、 **[フォルダーの選択]** を選択します。
3. **[ファイル]** メニューを選択し、 **[New]\(新規作成\)** を選択します。 Untitled-1 ファイルがプロジェクトに追加されます。
4. 次のコードを 無題-1 ファイルに入力します：

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   OUTPUT @departments     TO "/Output/departments.csv" USING Outputters.Csv();

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルが /output フォルダーに作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。

### <a name="to-compile-a-u-sql-script"></a>U-SQL スクリプトをコンパイルするには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Compile Script**」と入力します。 **[出力]** ウィンドウにコンパイル結果が表示されます。 スクリプト ファイルを右クリックし、 **[ADL:Compile Script]** を選択して U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は **[出力]** ウィンドウに表示されます。

### <a name="to-submit-a-u-sql-script"></a>U-SQL スクリプトを送信するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Submit Job** を選択して、ジョブを送信します。 スクリプト ファイルを右クリックし、 **[ADL:Submit Job]** を選択して、ジョブを送信します。

U-SQL ジョブの送信後、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 右側のウィンドウで、ジョブ ビューが表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。

ジョブ ビューの **[概要]** タブでは、ジョブの詳細を確認できます。 主な機能には、スクリプトの再送信、スクリプトの複製、ポータルで開くなどがあります。 ジョブ ビューの **[データ]** タブでは、入力ファイル、出力ファイル、リソースファイルを参照できます。 ファイルはローカル コンピューターにダウンロードできます。

![ジョブ ビューの概要タブ](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![ジョブ ビューのデータタブ](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>既定のコンテキストを設定するには

ファイルのパラメーターを個々に設定していない場合、この設定をすべてのスクリプトファイルに適用する既定のコンテキストを設定することができます。

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Set Default Context** を選択します。 または、スクリプト エディターを右クリックし、 **[ADL:Set Default Context]** を選択します。
3. 使用するアカウント、データベース、およびスキーマを選択します。 この設定は xxx_settings.json 構成ファイルに保存されます。

   ![既定のコンテキストとして設定されたアカウント、データベース、およびスキーマ](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>スクリプト パラメーターを設定するには

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

### <a name="to-set-git-ignore"></a>Git Ignore を設定するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Set Git Ignore**」と入力します。

   - VS Code 作業フォルダーに **.gitIgnore** ファイルが存在しない場合、このフォルダーに **.gitIgnore** という名前のファイルが作成されます。 4 つの項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、 **.cache**、**obj**) が既定でファイルに追加されます。 必要な場合は、さらに更新することができます。
   - VS Code 作業フォルダーに **.gitIgnore** ファイルが既に存在する場合、ツールによって4項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、 **.cache**、**obj**) が **.gitIgnore** ファイルに追加されます(ファイルにこの 4 項目が含まれていない場合)。

   ![.gitIgnore ファイル内の項目](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>分離コード ファイル(C Sharp、Python、R) を操作する

Azure Data Lake Tools は、複数のカスタム コードをサポートしています。 手順については、[VS Code で Azure Data Lake Analytics の Python、R、および C Sharp を使用した U-SQL を開発する](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)を参照してください。

## <a name="work-with-assemblies"></a>アセンブリの操作

アセンブリの開発に関する情報については、「[Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発]()」を参照してください。

Data Lake Analytics カタログには、Data Lake Tools を使用してカスタム コード アセンブリを登録できます。

### <a name="to-register-an-assembly"></a>アセンブリを登録するには

**ADL:Register Assembly** または **ADL:Register Assembly (Advanced)** コマンドを使用して、アセンブリを登録できます。

### <a name="to-register-through-the-adl-register-assembly-command"></a>ADL:Register Assembly コマンドを使用して登録するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Register Assembly**」と入力します。
3. ローカル アセンブリのパスを指定します。
4. Data Lake Analytics アカウントを選択します。
5. データベースを選択します。

ポータルがブラウザーで開かれ、アセンブリの登録プロセスが表示されます。  

**ADL:Register Assembly** コマンドをトリガーする、より便利な方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>ADL:Register Assembly (Advanced) コマンドを使用して登録するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:Register Assembly (Advanced)** 」と入力します。
3. ローカル アセンブリのパスを指定します。
4. JSON ファイルが表示されます。 アセンブリの依存関係とリソース パラメーターを確認して、必要な場合は編集します。 手順が **[出力]** ウィンドウに表示されます。 アセンブリの登録に進むには、JSON ファイルを保存 (Ctrl + S) します。

   ![アセンブリの依存関係とリソース パラメーターを使用した JSON file](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Tools は、DLL がアセンブリの依存関係をもつかどうかを自動的に検出します。 依存関係は、検出された後 JSON ファイルに表示されます。
>- DLL リソース (.txt、.png、.csv など) をアセンブリの登録の一部としてアップロードできます。

**ADL:Register Assembly (Advanced)** コマンドをトリガーする別の方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。

```usql
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
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>U-SQL のローカル実行とローカル デバッグをWindows ユーザーで使用する

U-SQL ローカル実行は、Data Lake Analytics にコードを発行する前に、ローカル データをテストし、スクリプトをローカルで検証するものです。 作成したコードを Data Lake Analytics に送信する前に、ローカル デバッグ機能を使用して次のタスクを完了することができます：

- C# 分離コードをデバッグする。
- コードをステップ実行する。
- スクリプトをローカルで検証する。

ローカル実行およびローカル デバッグ機能は Windows 環境でのみ動作し、macOS および Linux ベースのオペレーティング システムではサポートされていません。

ローカル実行とローカル デバッグの手順については、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。

## <a name="connect-to-azure"></a>Azure に接続する

Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>コマンドを使用して Azure に接続するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。

2. 「**ADL:Login** を選択します。 右下にログイン情報が表示されます。

   ![ログイン コマンドを入力しています](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![サインインと認証についての通知](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. **[Copy & Open]** を選択し、[ログイン Web ページ](https://aka.ms/devicelogin)を開きます。 ボックスにコードを貼り付け、 **[続行]** を選択します。

    ![ログイン Web ページ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. 手順に従って Web ページからサインインします。 接続すると、VS Code ウィンドウの左下隅のステータス バーに Azure アカウント名が表示されます。

> [!NOTE]
>
> - Data Lake Tools は、次回サインアウトしない場合、自動的にサインアウトします。
> - アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証の使用をお勧めします。

サインアウトするには、**ADL:Logout** コマンドを入力します。

### <a name="to-connect-to-azure-from-the-explorer"></a>エクスプローラーから Azure に接続するには

**AZURE DATALAKE** を展開し、 **Azure へのサインイン** を選択して次の [コマンドを使用して Azure に接続する](#sign-in-by-command)の手順 3 と手順 4に従ってください。

![エクスプ ローラーで「Azure にサインインする」の選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

エクスプ ローラーからサインアウトすることはできません。 サインアウトするには、[コマンドを使用して Azure に接続する](#sign-in-by-command)を参照してください。

## <a name="create-an-extraction-script"></a>抽出スクリプトの作成

.csv、.tsv、および .txt ファイルの抽出スクリプトは、**ADL:Create EXTRACT Script** コマンドを使用するか、Azure Data Lake エクスプローラーから作成できます。

### <a name="to-create-an-extraction-script-by-using-a-command"></a>コマンドを使用して抽出スクリプトを作成するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開き、「**ADL:Create EXTRACT Script**」と入力します。
2. Azure Storage ファイルの完全なパスを指定し、Enter キーを押します。
3. 1 つのアカウントを選択します。
4. .txt ファイルの場合は、区切り記号を選択してファイルを抽出します。

![抽出スクリプト作成のプロセス](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

入力に基づいて抽出スクリプトが生成されます。 列を検出できないスクリプトの場合は、次の 2 つのオプションのいずれかを選択します。 それ以外の場合は、1 つのスクリプトのみが生成されます。

![抽出スクリプト作成の結果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>エクスプローラーから抽出スクリプトを作成するには

抽出スクリプトを作成する別の方法は、Azure Data Lake Store または Azure Blob Storage 内の .csv ファイル、.tsv ファイルの右クリック (ショートカット) メニューを使用することです。

![ショートカット メニューの「抽出スクリプトを作成する」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>次のステップ

- [VS Code で Azure Data Lake Analytics の Python、R、および C　Sharp を使用した U-SQL の開発](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
- [チュートリアル:Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [チュートリアル:Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)