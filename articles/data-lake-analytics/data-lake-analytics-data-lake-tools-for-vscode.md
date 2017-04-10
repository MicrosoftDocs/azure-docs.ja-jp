---
title: "Azure Data Lake Tools for Visual Studio Code の使用 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
translationtype: Human Translation
ms.sourcegitcommit: e79513590bb37570764f398e716182a11c74612a
ms.openlocfilehash: 59cc35bc740625ed0582c1557fac9a04bf0cb8bc

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

Azure Data Lake Tools for Visual Studio Code (VSCode) を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。  この情報は、次のビデオにも含まれています。

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>前提条件

Data Lake Tools は、VSCode でサポートされている Windows、Linux、MacOS などのプラットフォームにインストールできます。 プラットフォームごとの前提条件については、以下を参照してください。

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 java.exe のパスをシステム環境変数パスに追加する必要があります。  手順については、「[how do I set or change the Path system variable? (Path システム変数を設定または変更するにはどうすればよいですか?)]( https://www.java.com/download/help/path.xml)」を参照してください。 このパスは、C:\Program Files\Java\jdk1.8.0_77\jre\bin のようなパスです。
    - [.NET Core SDK 1.0.1 Preview 2 または .NET Core 1.0.1 ランタイム]( https://www.microsoft.com/net/download)。
    
- Linux (Ubuntu 14.04 LTS を推奨)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 次のコマンドを使用してインストールします。

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - 次のコマンドを実行して deb パッケージ ソースを更新します。

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 次のコマンドを実行して Mono をインストールします。

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 はサポートされていません。  バージョン 4.6 を完全にアンインストールしてから 4.2.x をインストールする必要があります。  

        - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 手順は[こちら]( https://java.com/en/download/help/linux_x64_install.xml)に記載されています。
        - [.NET Core SDK 1.0.1 Preview 2 または .NET Core 1.0.1 ランタイム]( https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/)。 
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 手順は[こちら](https://java.com/en/download/help/mac_install.xml)に記載されています。
    - [.NET Core SDK 1.0.1 Preview 2 または .NET Core 1.0.1 ランタイム]( https://www.microsoft.com/net/download)。

## <a name="install-the-data-lake-tools"></a>Data Lake Tools のインストール

前提条件の各項目のインストールが完了したら、Data Lake Tools for VSCode をインストールできます。

**Data Lake Tools をインストールするには**

1. **Visual Studio Code** を開きます。
2. **Ctrl + P** キーを押し、次のコマンドを入力します。

        ext install usql-vscode-ext
    Visual Studio Code 拡張機能の一覧が表示されます。 そのうちの 1 つが **Azure Data Lake Tool (Preview)** です。
3. **[Azure Data Lake Tool (Preview)]** の横にある **[インストール]** をクリックします。 数秒後、[インストール] ボタンが [再度読み込む] に変わります。
4. **[再度読み込む]** をクリックして拡張機能をアクティブにします。
5. **[OK]** をクリックして確定します。 [拡張機能] ウィンドウに Azure Data Lake Tools が表示されます。

    ![Data Lake Tools for Visual Studio Code のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Connect to Azure

U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

**Azure に接続するには**

1.  **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2.  「**ADL: Login**」と入力します。

    ![Data Lake Tools for Visual Studio Code のコマンド パレット](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  手順に従って Web ページからサインインします。 接続すると、ウィンドウの下部にあるステータス バーにアカウント名が表示されます。

> [!NOTE] 
> アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証を使用することをお勧めします。

サインオフするには、**ADL: Logout** コマンドを使用します。

## <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントを一覧表示します。

接続をテストするには、Data Lake Analytics アカウントを一覧表示します。

**Azure サブスクリプション内の Data Lake Analytics アカウントを一覧表示するには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2. 「**ADL: List Accounts**」と入力します。  **[出力]** ウィンドウにアカウントが表示されます。

## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**U-SQL プロジェクトのフォルダーを開くには**

1. Visual Studio Code で **[ファイル]** メニューをクリックし、**[フォルダーを開く]** をクリックします。
2. フォルダーを指定し、**[フォルダーの選択]** をクリックします。
3. **[ファイル]** メニューをクリックし、**[新規ファイル]** をクリックします。 **Untilted-1** ファイルがプロジェクトに追加されます。
4. 次のコードをコピーして、Untitled-1 ファイルに貼り付けます。

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
            TO “/Output/departments.csv”

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルを /output フォルダー内に作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。 **adltools_settings.json** 構成ファイルもプロジェクトに追加される点に注意してください。
4. **adltools_settings.json** を開き、以下のプロパティで構成します。

    - アカウント: Azure サブスクリプション内の Data Lake Analytics アカウント。
    - オプション設定:

        - 優先順位: 優先順位の範囲は 1 ～ 1,000 で、1 が最も高い優先順位です。 既定値は 1,000 です。
        - 並列処理: 並列処理の範囲は 1 ～ 150 です。 既定値は 150 です。 

        > [!NOTE] 
        > 設定値が有効でない場合は、既定値が使用されます。

     ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    U-SQL ジョブのコンパイルと実行には、コンピューティング用の Data Lake Analytics アカウントが必要です。  U-SQL ジョブのコンパイルと実行を行う前に、このコンピューター アカウントを構成する必要があります。

ファイルを開く方法とは異なり、フォルダーを開く方法では以下の操作が可能です。

- 分離コード ファイルを使用する。  単一ファイル モードでは、分離コードがサポートされていません。
- 構成ファイルを使用する。 フォルダーを開くと、作業フォルダ―内のスクリプトで 1 つの構成ファイルが共有されます。


U-SQL スクリプトのコンパイルは、Data Lake Analytics サービスによってリモートで行われます。  コンパイル コマンドを発行すると、U-SQL スクリプトが Data Lake Analytics アカウントに送信されます。 コンパイル結果は後で Visual Studio Code が受信します。 このようにリモートでコンパイルを行うため、Visual Studio Code では、Data Lake Analytics アカウントに接続するための情報が構成ファイル内に必要です。

**U-SQL スクリプトをコンパイルするには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2. 「**ADL: Compile Script**」と入力します。 コンパイル結果が出力ウィンドウに表示されます。 スクリプト ファイルを右クリックし、**[ADL: Compile Script]** をクリックして U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は出力ウィンドウに表示されます。
 

**U-SQL スクリプトを送信するには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2. 「**ADL: Submit Job**」と入力します。  スクリプト ファイルを右クリックし、**[ADL: Submit Job]** をクリックして U-SQL ジョブを送信することもできます。 

U-SQL ジョブを送信すると、送信ログが VSCode の出力ウィンドウに表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。

ジョブの詳細の出力を有効にするには、**vscode for u-sql_settings.json** ファイルで "jobInformationOutputPath" を設定します。
 
## <a name="use-code-behind-file"></a>分離コード ファイルの使用

分離コード ファイルは、1 つの U-SQL スクリプトに関連付けられている CSharp ファイルです。 分離コード ファイルでは、スクリプト専用の UDO/UDA/UDT/UDF を定義できます。 UDO/UDA/UDT/UDF は、アセンブリを先に登録しなくても、スクリプト内で直接使用できます。 分離コード ファイルは、対になる U-SQL スクリプト ファイルと同じフォルダーに置かれます。 スクリプトに xxx.usql という名前が付けられた場合、分離コードの名前は xxx.usql.cs となります。 分離コード ファイルを手動で削除すると、関連付けられている U-SQL スクリプトの分離コード機能が無効になります。 U-SQL スクリプト用のカスタム コードの記述方法の詳細については、「[Writing and Using Custom Code in U-SQL – User-Defined Functions (U-SQL でのカスタム コードの記述と使用 - ユーザー定義関数)]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)」を参照してください。

分離コードをサポートするには、作業フォルダーを開く必要があります。 

**分離コード ファイルを生成するには**

1. ソース ファイルを開きます。 
2. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
3. 「**ADL: Generate Code Behind**」と入力します。  分離コード ファイルが同じフォルダーに作成されます。 

スクリプト ファイルを右クリックし、**[ADL: Generate Code Behind]** をクリックして、分離コード ファイルを生成することもできます。 

分離コードを含む U-SQL スクリプトのコンパイルと送信は、単独の U-SQL スクリプトの場合と同様です。

以下の 2 つのスクリーンショットは、分離コード ファイルとそれに関連付けられている U-SQL スクリプト ファイルを示したものです。
 
![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>アセンブリの使用

アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

Data Lake Tools を使用すると、カスタム コード アセンブリを Data Lake Analytics カタログに登録できます。

**アセンブリを登録するには**

1.  **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly**」と入力します。
3.  Data Lake Analytics アカウントを選択します。
4.  データベースを選択します。
5.  ローカル アセンブリのパスを指定します。

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。

    REFERENCE ASSEMBLY [test];
    @a=EXTRACT Iid int,Starts DateTime,Region string,Query string,DwellTime int,Results string,ClickedUrls string 
    FROM @"ruoxin/SearchLog.txt" USING Extractors.Tsv();
    
    @d=SELECT DISTINCT Region FROM @a;
    
    @d1=PROCESS @d
        PRODUCE Region string,
                Mkt string
                USING new USQLApplication_codebehind.MyProcessor();
    
    OUTPUT @d1 TO @"ruoxin/SearchLogtest.txt" USING Outputters.Tsv();



## <a name="access-data-lake-analytics-catalog"></a>Data Lake Analytics カタログへのアクセス

Azure に接続した後は、以下の手順に従って U-SQL カタログにアクセスできます。

**U-SQL メタデータにアクセスするには**

1.  **Ctrl + Shift + P** キーを押し、「**ADL: List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントをクリックします。
3.  いずれかの Data Lake Analytics データベースをクリックします。
4.  いずれかのスキーマをクリックします。 テーブルが表示されます。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VSCode では、以下の各機能がサポートされています。

-   IntelliSense オートコンプリート。 キーワード、メソッド、変数などの候補がポップアップ表示されます。以下に示すさまざまなオブジェクト型が、異なるアイコンで表されます。

    - Scala データ型
    - 複合データ型
    - 組み込みの UDT
    - .NET コレクションおよびクラス
    - C# 式
    - 組み込みの C# UDF、UDO、UDAAG 
    - U-SQL 関数
    - U-SQL ウィンドウ化関数
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics メタデータでの IntelliSense オートコンプリート。 Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。  IntelliSense 機能により、Data Lake Analytics メタデータからデータベース、スキーマ、テーブル、ビュー、TVF、プロシージャ、C# アセンブリなどのオブジェクトが自動的に入力されます。
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense メタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense エラー マーカー。 Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。 
-   構文の強調表示。 Data Lake Tools では、変数、キーワード、データ型、関数などを区別するために異なる色が使用されます。 

    ![Data Lake Tools for Visual Studio Code の構文の強調表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

##<a name="next-steps"></a>次のステップ:

- Data Lake Analytics の概要については、[Azure Data Lake Analytics の使用開始に関するチュートリアル](data-lake-analytics-get-started-portal.md)のページを参照してください。
- Data Lake Tools for Visual Studio の使用方法については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。






<!--HONumber=Dec16_HO1-->


