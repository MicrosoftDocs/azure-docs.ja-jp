---
title: "Azure Data Lake Tools: Azure Data Lake Tools for Visual Studio Code の使用 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。 "
Keywords: "VScode,Azure Data Lake Tools,ローカル実行,ローカル デバッグ,ストレージ ファイルのプレビュー,ストレージ パスにアップロード"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 4be6e2ef1cfba31dd3cf06f44e6a71ffd5900856
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

Azure Data Lake Tools for Visual Studio Code (VS Code) を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。 この情報は、次のビデオにも含まれています。

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>前提条件

Data Lake Tools は、VS Code でサポートされているプラットフォームにインストールできます。 Windows、Linux、MacOS などのプラットフォームでサポートされます。 プラットフォームごとの前提条件を次に示します。

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 java.exe のパスをシステム環境変数パスに追加します。 構成手順については、[Path システム変数を設定または変更する方法]( https://www.java.com/download/help/path.xml)に関するページを参照してください。 このパスは C:\Program Files\Java\jdk1.8.0_77\jre\bin のようになります。
    - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。
    
- Linux (Ubuntu 14.04 LTS を推奨)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 コードをインストールするには、次のコマンドを入力します。

              sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - deb パッケージ ソースを更新するには、次のコマンドを入力します。

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Mono をインストールするには、次のコマンドを入力します。

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 はサポートされていません。 4.2.x をインストールする前にバージョン 4.6 を完全にアンインストールしてください。  

        - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 インストール手順については、「[Linux 64-bit installation instructions for Java (Java の Linux 64 ビットのインストール手順)]( https://java.com/en/download/help/linux_x64_install.xml)」ページを参照してください。
        - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/)。 
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 インストール手順については、「[Linux 64-bit installation instructions for Java (Java の Linux 64 ビットのインストール手順)](https://java.com/en/download/help/mac_install.xml)」ページを参照してください。
    - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。

## <a name="install-data-lake-tools"></a>Data Lake Tools のインストール

前提条件の各項目のインストールが完了したら、Data Lake Tools for VS Code をインストールできます。

**Data Lake Tools をインストールするには**

1. Visual Studio Code を開きます。
2. Ctrl + P キーを押して、次のコマンドを入力します。
```
ext install usql-vscode-ext
```
Visual Studio Code 拡張機能の一覧が表示されます。 そのうちの 1 つが **Azure Data Lake Tools** です。

3. **[Azure Data Lake Tools]** の横にある **[インストール]** を選択します。 数秒後、**[インストール]** ボタンが **[再度読み込む]** に変わります。
4. **[再度読み込む]** を選択して拡張機能をアクティブにします。
5. **[OK]** を選択して確定します。 **[拡張機能]** ウィンドウに Azure Data Lake Tools が表示されます。
    ![Data Lake Tools for Visual Studio Code の [拡張機能] ウィンドウ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する
新しい .usql ファイルを作成するか、既存の .usql ファイルを開いて拡張機能をアクティブにしてください。 

## <a name="connect-to-azure"></a>Azure への接続

Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

**Azure に接続するには**

1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2.  「**ADL: Login**」と入力します。 ログイン情報が **[出力]** ウィンドウに表示されます。

    ![Data Lake Tools for Visual Studio Code のコマンド パレット](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake Tools for Visual Studio Code デバイス ログイン情報](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Ctrl キーを押しながらログイン URL「https://aka.ms/devicelogin」をクリックして、ログイン Web ページを開きます。 テキスト ボックスにコード「**G567LX42V**」を入力し、**[続行]** を選択します。

   ![Data Lake Tools for Visual Studio Code のログイン時に貼り付けるコード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  手順に従って Web ページからサインインします。 接続すると、**VS Code** ウィンドウの左下隅のステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE] 
    > アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証の使用をお勧めします。

サインアウトするには、**ADL: Logout** コマンドを入力します。

## <a name="list-your-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントの一覧表示

接続をテストするには、Data Lake Analytics アカウントの一覧を取得します。

**Azure サブスクリプション内の Data Lake Analytics アカウントを一覧表示するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL: List Accounts**」と入力します。 **[出力]** ウィンドウにアカウントが表示されます。

## <a name="open-the-sample-script"></a>サンプル スクリプトを開く
コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL: Open Sample Script**」と入力します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**U-SQL プロジェクトのフォルダーを開くには**

1. Visual Studio Code で **[ファイル]** メニューを選択し、**[フォルダーを開く]** を選択します。
2. フォルダーを指定し、**[フォルダーの選択]** を選択します。
3. **[ファイル]** メニューを選択し、**[New]\(新規作成\)** を選択します。 Untitled-1 ファイルがプロジェクトに追加されます。
4. 次のコードを Untitled-1 ファイルに入力します。

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルが /output フォルダーに作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。 adltools_settings.json 構成ファイルもプロジェクトに追加されます。
4. adltools_settings.json を開き、以下のプロパティで構成します。

    - アカウント: Azure サブスクリプション内の Data Lake Analytics アカウント。
    - データベース: 自分のアカウント内のデータベース。 既定値は **master** です。
    - スキーマ: データベース内のスキーマ。 既定値は **dbo** です。
    - オプション設定:
        - 優先順位: 優先順位の範囲は 1 ～ 1,000 で、1 が最も高い優先順位です。 既定値は **1,000** です。
        - 並列処理: 並列処理の範囲は 1 ～ 150 です。 既定値は、Azure Data Lake Analytics アカウントで許可される並列処理の最大数です。 
        
        > [!NOTE] 
        > 設定値が有効でない場合は、既定値が使用されます。

    ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    U-SQL ジョブのコンパイルと実行には、コンピューティング用の Data Lake Analytics アカウントが必要です。 U-SQL ジョブのコンパイルと実行を行う前に、このコンピューター アカウントを構成する必要があります。
    
構成の保存後、アカウント、データベース、スキーマ情報が、対応する .usql ファイルの左下隅にあるステータス バーに表示されます。 
 
 
ファイルを開いた場合と比べて、フォルダーを開いたときは、次のことができます。

- 分離コード ファイルを使用する。 単一ファイル モードでは、分離コードがサポートされていません。
- 構成ファイルを使用する。 フォルダーを開くと、作業フォルダ―内のスクリプトで 1 つの構成ファイルが共有されます。


U-SQL スクリプトのコンパイルは、Data Lake Analytics サービスによってリモートで行われます。 **compile** コマンドを発行すると、U-SQL スクリプトが Data Lake Analytics アカウントに送信されます。 Visual Studio Code は後でコンパイル結果を受信します。 このようにリモートでコンパイルを行うため、Visual Studio Code では、Data Lake Analytics アカウントに接続するための一連の情報が構成ファイル内に必要です。

**U-SQL スクリプトをコンパイルするには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Compile Script**」と入力します。 **[出力]** ウィンドウにコンパイル結果が表示されます。 スクリプト ファイルを右クリックし、**[ADL: Compile Script]** を選択して U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は **[出力]** ウィンドウに表示されます。
 

**U-SQL スクリプトを送信するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Submit Job**」と入力します。  スクリプト ファイルを右クリックし、**[ADL: Submit Job]** を選択することもできます。 

U-SQL ジョブの送信後、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。

ジョブの詳細の出力を有効にするには、**vs code for the u-sql_settings.json** ファイルで **jobInformationOutputPath** を設定します。
 
## <a name="use-a-code-behind-file"></a>分離コード ファイルの使用

分離コード ファイルは、1 つの U-SQL スクリプトに関連付けられている C# ファイルです。 分離コード ファイルでは、スクリプト専用の UDO、UDA、UDT、UDF を定義できます。 UDO、UDA、UDT、UDF は、アセンブリを先に登録しなくても、スクリプト内で直接使用できます。 分離コード ファイルは、対になる U-SQL スクリプト ファイルと同じフォルダーに置かれます。 スクリプトに xxx.usql という名前が付けられた場合、分離コードの名前は xxx.usql.cs となります。 分離コード ファイルを手動で削除すると、関連付けられている U-SQL スクリプトの分離コード機能が無効になります。 U-SQL スクリプト用のカスタム コードを記述する方法の詳細については、「[Writing and Using Custom Code in U-SQL – User-Defined Functions (U-SQL でのカスタム コードの記述と使用 - ユーザー定義関数)]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)」を参照してください。

分離コードをサポートするには、作業フォルダーを開く必要があります。 

**分離コード ファイルを生成するには**

1. ソース ファイルを開きます。 
2. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
3. 「**ADL: Generate Code Behind**」と入力します。 分離コード ファイルが同じフォルダーに作成されます。 

スクリプト ファイルを右クリックし、**[ADL: Generate Code Behind]** を選択することもできます。 

分離コード ファイルを含む U-SQL スクリプトのコンパイルと送信は、単独の U-SQL スクリプト ファイルの場合と同様です。

以下の 2 つのスクリーンショットは、分離コード ファイルとそれに関連付けられている U-SQL スクリプト ファイルを示したものです。
 
![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools for Visual Studio Code の分離コード スクリプト ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>アセンブリの使用

アセンブリの開発に関する情報については、「[Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

Data Lake Analytics カタログには、Data Lake Tools を使用してカスタム コード アセンブリを登録できます。

**アセンブリを登録するには**

**ADL: Register Assembly** コマンドまたは **ADL: Register Assembly through Configuration** コマンドを使用してアセンブリを登録することができます。

**ADL: Register Assembly コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  Data Lake Analytics アカウントを選択します。
5.  データベースを選択します。

結果: ブラウザーにポータルが表示され、アセンブリの登録プロセスが表示されます。  

**ADL: Register Assembly** コマンドをトリガーする他の便利な方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。 

**ADL: Register Assembly through Configuration コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly through Configuration**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  JSON ファイルが表示されます。 アセンブリの依存関係とリソース パラメーターを確認して、必要な場合は編集します。 手順が **[出力]** ウィンドウに表示されます。 アセンブリの登録に進むには、JSON ファイルを保存 (Ctrl + S) します。

![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- アセンブリの依存関係: Azure Data Lake Tools は、DLL が依存関係を備えているかどうかを自動的に検出します。 検出された依存関係は、その後 JSON ファイルに表示されます。 
>- リソース: DLL のリソース (.txt、.png、.csv など) をアセンブリの登録の一部としてアップロードできます。 

**ADL: Register Assembly through Configuration** コマンドは、エクスプローラーで .dll ファイルを右クリックすることによってトリガーすることもできます。 

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。

```
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


## <a name="access-the-data-lake-analytics-catalog"></a>Data Lake Analytics カタログへのアクセス

Azure に接続した後は、以下の手順に従って U-SQL カタログにアクセスできます。

**Azure Data Lake Analytics のメタデータにアクセスするには**

1.  Ctrl + Shift + P キーを押し、「**ADL: List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントを選択します。
3.  いずれかの Data Lake Analytics データベースを選択します。
4.  いずれかのスキーマを選択します。 テーブルの一覧が表示されます。

## <a name="view-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブの表示

**Data Lake Analytics ジョブを表示するには**
1.  コマンド パレットを開いて (Ctrl + Shift + P)、**[ADL: Show Job]** を選択します。 
2.  Data Lake Analytics アカウントまたはローカル アカウントを選択します。 
3.  アカウントのジョブの一覧が表示されるまで待機します。
4.  ジョブの一覧からジョブを選択すると、Data Lake Tools によって Azure Portal にジョブの詳細が表示され、VS Code に JobInfo ファイルが表示されます。

![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake Storage の統合

Azure Data Lake Storage 関連コマンドを使用して次のことを実行できます。
 - Azure Data Lake Storage のリソースを閲覧する。 
 - Azure Data Lake Storage ファイルをプレビューする。  
 - VS Code で Azure Data Lake Storage に直接ファイルをアップロードする。 

### <a name="list-the-storage-path"></a>ストレージ パスの一覧表示 
ストレージ パスは、コマンド パレットまたは右クリックを使用して一覧表示することができます。

**コマンド パレットを使用してストレージ パスを一覧表示するには**

1.  コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL: List Storage Path**」と入力します。

    ![Data Lake Tools for Visual Studio Code のストレージ パスの一覧表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  ストレージ パスを一覧表示する方法を選択します。 ここでは、例として **[Enter a path] \(パスを入力)** を使用しています。

    ![Data Lake Tools for Visual Studio Code でストレージ パスを一覧表示する方法の選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- 最後にアクセスしたパスは、VS Code によって各 Data Lake Analytics アカウントに記録されます  (/tt/ss など)。
    >- [Browser from root path]\(ルート パスから参照\): 選択した Data Lake Analytics アカウントまたはローカル パスを起点としてパスを一覧表示します。
    >- [Enter a path]\(パスを入力\): 選択した Data Lake Analytics アカウントまたはローカル パスから、指定したパスを一覧表示します。
    
3. アカウントをローカル パスまたは Data Lake Analytics アカウントから選択します。

    ![Data Lake Tools for Visual Studio Code で [その他] を選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4. **[more]\(その他\)** を選択して、他の Data Lake Analytics アカウントを一覧表示し、Data Lake Analytics アカウントを選択します。

    ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Azure ストレージ パスを入力します。 たとえば、「/output」と入力します。

    ![Data Lake Tools for Visual Studio Code でストレージ パスを入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  結果: コマンド パレットには、入力内容に基づくパス情報が一覧表示されます。

    ![Data Lake Tools for Visual Studio Code のストレージ パスの結果一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

相対パスの一覧を表示するさらに便利な方法は、右クリック コンテキスト メニューを使用することです。

**右クリックを使用してストレージ パスを一覧表示するには**

1.  パス文字列を右クリックし、**[List Storage Path]\(ストレージ パスのリスト\)** を選択します。

       ![Data Lake Tools for Visual Studio Code の右クリック コンテキスト メニュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

2. コマンド パレットに、選択した相対パスが表示されます。

   ![Data Lake Tools for Visual Studio Code の選択した相対パス](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  アカウントをローカル パスまたは Data Lake Analytics アカウントから選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  結果: コマンド パレットに、現在のパスのフォルダーおよびファイルが一覧表示されます。

       ![Data Lake Tools for Visual Studio Code の現在のパスを起点とする一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-the-storage-file"></a>ストレージ ファイルのプレビュー
ストレージ ファイルは、コマンド パレットまたは右クリックを使用してプレビューすることができます。

**コマンド パレットを使用してストレージ ファイルをプレビューするには**

1.  コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL: Preview Storage File**」と入力します。

       ![Data Lake Tools for Visual Studio Code のストレージ ファイルをプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  アカウントをローカル パスまたは Data Lake Analytics アカウントから選択します。

       ![Data Lake Tools for Visual Studio Code のアカウント一覧表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  **[more]\(その他\)** を選択して、他の Data Lake Analytics アカウントを一覧表示し、Data Lake Analytics アカウントを選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Azure ストレージ パスまたはファイルを入力します  (例: /output/SearchLog.txt)。

       ![Data Lake Tools for Visual Studio Code のストレージ パスとファイルを入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  結果: コマンド パレットには、入力内容に基づくパス情報が一覧表示されます。

       ![Data Lake Tools for Visual Studio Code のファイル結果をプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

**右クリックを使用してストレージ パスを一覧表示するには**

1.  ファイルをプレビューするには、ファイル パスを右クリックします。

   ![Data Lake Tools for Visual Studio Code の右クリック コンテキスト メニュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png) 

2.  アカウントをローカル パスまたは Data Lake Analytics アカウントから選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  結果: VS Code に、ファイルのプレビュー結果が表示されます。

       ![Data Lake Tools for Visual Studio Code のファイル結果をプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-a-file"></a>ファイルをアップロードする 

ファイルのアップロードは、**ADL: Upload File** コマンドまたは **ADL: Upload File through Configuration** コマンドを入力することによって実行できます。

**ADL: Upload File コマンドを使用してファイルをアップロードするには**
1. Ctrl + Shift + P キーを押してコマンド パレットを開くか、スクリプト エディターを右クリックして、「**Upload File**」と入力します。
2.  ファイルをアップロードするには、ローカル パスを入力します。

    ![Data Lake Tools for Visual Studio Code でのローカル パスの入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. ストレージ パスの一覧表示の方法を選択します。 ここでは、例として **[Enter a path] \(パスを入力)** を使用しています。

    ![Data Lake Tools for Visual Studio Code のストレージ パスの一覧表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- 最後にアクセスしたパスは、VS Code によって各 Data Lake Analytics アカウントに記録されます  (/tt/ss など)。
    >- [Browser from root path]\(ルート パスから参照\): 選択した Data Lake Analytics アカウントまたはローカル パスを起点としてパスを一覧表示します。
    >- [Enter a path]\(パスを入力\): 選択した Data Lake Analytics アカウントまたはローカル パスから、指定したパスを一覧表示します。

4. アカウントをローカル パスまたは Data Lake Analytics アカウントから選択します。

    ![Data Lake Tools for Visual Studio Code でのストレージの右クリック](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5. Azure ストレージ パスを入力します。 たとえば、「/output」と入力します。

       ![Data Lake Tools for Visual Studio Code enter storage path](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. 目的の Azure ストレージ パスを探します。 **[Choose current folder] \(現在のフォルダーを選択)** を選択します。

    ![Data Lake Tools for Visual Studio Code のフォルダーの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  結果: **[出力]** ウィンドウに、ファイル アップロードの状態が表示されます。

       ![Data Lake Tools for Visual Studio Code のアップロード状態](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration コマンドを使用してファイルをアップロードするには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開くか、スクリプト エディターを右クリックして、「**Upload File through Configuration**」と入力します。
2.  VS Code に、JSON ファイルが表示されます。 ファイルのパスを入力し、同時に複数のファイルをアップロードできます。 手順が **[出力]** ウィンドウに表示されます。 ファイルのアップロードを続行するには、Ctrl + S キーを押して JSON ファイルを保存します。

       ![Data Lake Tools for Visual Studio Code のファイル パス](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  結果: **[出力]** ウィンドウに、ファイル アップロードの状態が表示されます。

       ![Data Lake Tools for Visual Studio Code のアップロード状態](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

ファイルをストレージにアップロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。 ローカル ファイルのパスを入力し、アカウントを選択します。 **[出力]** ウィンドウに、アップロードの状態が表示されます。 

### <a name="open-azure-storage-explorer"></a>Azure ストレージ エクスプローラーを開く
**Azure ストレージ エクスプローラー**は、**ADL: Open Web Azure Storage Explorer** コマンドを入力するか、このコマンドを右クリックのコンテキスト メニューから選択することによって開くことができます。

**Azure ストレージ エクスプローラーを開くには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、**[Open Web Azure Storage Explorer]\(Web Azure Storage Explorer を開く\)** を選択します。
3. Data Lake Analytics アカウントを選択します。

Data Lake Tools によって、Azure Portal に Azure ストレージ パスが表示されます。 そのパスを見つけて Web からファイルをプレビューすることができます。

### <a name="local-run-and-local-debug-for-windows-users"></a>Windows ユーザーのローカル実行とローカル デバッグ
U-SQL ローカル実行は、Data Lake Analytics にコードを発行する前に、ローカル データをテストし、スクリプトをローカルで検証するものです。 作成したコードを Data Lake Analytics に送信する前に、ローカル デバッグ機能によって次のタスクを実行することができます。 
- C# 分離コードをデバッグする。 
- コードをステップ実行する。 
- スクリプトをローカルで検証する。

ローカル実行とローカル デバッグの手順については、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VS Code では、以下の各機能がサポートされています。

-   IntelliSense オートコンプリート: キーワードやメソッド、変数などの要素の付近にポップアップ ウィンドウで入力候補が表示されます。 以下に示すさまざまなオブジェクト型が、異なるアイコンで表されます。

    - Scala データ型
    - 複合データ型
    - 組み込みの UDT
    - .NET コレクションおよびクラス
    - C# 式
    - 組み込みの C# UDF、UDO、UDAAG 
    - U-SQL 関数
    - U-SQL ウィンドウ化関数
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics メタデータに基づく IntelliSense オートコンプリート: Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。 IntelliSense 機能により、Data Lake Analytics メタデータからデータベース、スキーマ、テーブル、ビュー、テーブル値関数、プロシージャ、C# アセンブリなどのオブジェクトが自動的に入力されます。
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense メタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense エラー マーカー: Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。 
-   構文の強調表示: Data Lake Tools では、変数、キーワード、データ型、関数などの各種の要素を区別するために異なる色が使用されます。 

    ![Data Lake Tools for Visual Studio Code の構文の強調表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>次のステップ

- Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグについては、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。
- Data Lake Analytics の概要については、[Azure Data Lake Analytics の使用開始に関するチュートリアル](data-lake-analytics-get-started-portal.md)のページを参照してください。
- Data Lake Tools for Visual Studio については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- アセンブリの開発に関する情報については、「[Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。



