---
title: Azure Virtual Machines で Micro Focus Enterprise Developer 4.0 用の Micro Focus CICS BankDemo を設定する
description: Micro Focus Enterprise Server と Enterprise Developer の使用方法を学習するために、Azure Virtual Machines (VM) で Micro Focus BankDemo アプリケーションを実行します。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411066"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Azure で Micro Focus Enterprise Developer 4.0 用の Micro Focus CICS BankDemo を設定する

Micro Focus Enterprise Server 4.0 と Enterprise Developer 4.0 を Azure 上で設定する際には、IBM z/OS ワークロードのデプロイをテストできます。 この記事では、CICS BankDemo (Enterprise Developer に付属しているサンプル アプリケーション) の設定方法について説明します。

CICS とは、Customer Information Control System (顧客情報管理システム) の略です。これは、多くのオンライン メインフレーム アプリケーションで使用されているトランザクション プラットフォームです。 BankDemo アプリケーションは、Enterprise Server と Enterprise Developer の動作を学習したり、グリーンスクリーンターミナルを備えた実際のアプリケーションの管理方法やデプロイ方法を学習するのに最適です。

> [!NOTE]
> 近日対応予定:Azure VM での [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) のセットアップの手順。

## <a name="prerequisites"></a>前提条件

- [Enterprise Developer](set-up-micro-focus-azure.md) がインストールされたVM。 Enterprise Developer には、開発やテスト目的に使用できる、Enterprise Server の完全なインスタンスが含まれています。 このインスタンスは、デモで使用される Enterprise Server のインスタンスです。

- [SQL Server 2017 Express エディション](https://www.microsoft.com/sql-server/sql-server-editions-express)。 ダウンロードして、Enterprise Developer VM にインストールします。 Enterprise Server では、CICS リージョンの管理用データベースが必要になります。また BankDemo アプリケーションでも、BANKDEMO という SQL Server データベースが使用されます。 このデモでは、両方のデータベースに SQL Server Express を使用していることを前提とします。 インストールの際には、基本インストールを選択してください。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS)。 SSMS は、データベースの管理と T-SQL スクリプトの実行に使用されます。 ダウンロードして、Enterprise Developer VM にインストールします。

- 最新のサービス パックが適用された [Visual Studio 2019](https://azure.microsoft.com/downloads/) か、[Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) (無料でダウンロードできます)。

- Rumba Desktop または別の 3270 のエミュレーター。

## <a name="configure-the-windows-environment"></a>Windows 環境を構成する

Enterprise Developer 4.0 を VM にインストールしたら、それに付属する Enterprise Server のインスタンスを構成する必要があります。 これを行うには、次の手順で、いくつかの追加の Windows 機能をインストールする必要があります。

1. RDP を使用して、作成した Enterprise Server 4.0 の VM にログオンします。

2. **[スタート]** ボタンの横にある **[検索]** アイコンをクリックし、「**Windows 機能**」と入力します。 サーバー マネージャーの役割と機能の追加ウィザードが開きます。

3. **[Web サーバー (IIS) の役割]** を選択し、次のオプションをオンにします。

    - Web 管理ツール
    - IIS 6 管理互換 (使用可能な機能をすべて選択します)
    - IIS 管理コンソール
    - IIS 管理スクリプトおよびツール
    - IIS 管理サービス

4. **[World Wide Web サービス]** を選択し、次のオプションをオンにします。

     アプリケーション開発機能:
    - .NET 拡張機能
    - ASP.NET
    - HTTP 共通機能:使用可能な機能をすべて追加します
    - 状態と診断:使用可能な機能をすべて追加します
    - セキュリティ:
        - 基本認証
        - Windows 認証

5. **[Windows プロセス アクティブ化サービス]** とその子項目をすべて選択します。

6. **[機能]** で、 **[Microsoft .NET framework 3.5.1]** をオンにし、次のオプションをオンにします。

    - Windows Communication Foundation HTTP Activation
    - Windows Communication Foundation Non-HTTP Activation

7. **[機能]** で、 **[Microsoft .NET framework 4.6]** をオンにし、次のオプションをオンにします。

   - 名前付きパイプのアクティブ化
   - TCP のアクティブ化
   - TCP ポート共有

     ![役割と機能の追加ウィザード:役割サービス](media/01-demo-roles.png)

8. すべてのオプションを選択したら、 **[次へ]** をクリックしてインストールします。

9. Windows 機能がインストールされたら、 **[コントロール パネル] \> [システムとセキュリティ] \> [管理ツール]**  に移動し、 **[サービス]** を選択します。 下にスクロールし、次のサービスが実行されていることと、それらが **[自動]** に設定されていることを確認します。

    - **NetTcpPortSharing**
    - **Net.Pipe リスナー アダプター**
    - **Net.tcp リスナー アダプター**

10. IIS と WAS のサポートを構成するには、メニューから **[Micro Focus Enterprise Developer Command Prompt (64 bit)]** を探し、 **[管理者]** として実行します。

11. 「**wassetup –i**」と入力し、**Enter** キーを押します。

12. スクリプトが実行されたら、ウィンドウを閉じることができます。

## <a name="configure-the-local-system-account-for-sql-server"></a>SQL Server 用のローカル システム アカウントを構成する

一部の Enterprise Server プロセスでは、SQL Server にサインインし、データベースやその他のオブジェクトを作成する必要があります。 これらのプロセスではローカル システム アカウントが使用されるため、そのアカウントに sysadmin 権限を付与する必要があります。

1. **SSMS** を起動し、 **[接続]** をクリックして、ローカルの SQLEXPRESS サーバーに Windows 認証を使って接続します。 これは **[サーバー名]** の一覧から選択できます。

2. 左側で、 **[セキュリティ]** フォルダーを展開し、 **[ログイン]** を選択します。

3. **[NT AUTHORITY\\SYSTEM]** を選択し、 **[プロパティ]** を選択します。

4. **[サーバー ロール]** を選択し、 **[sysadmin]** をオンにします。

     ![SSMS の [オブジェクト エクスプローラー] ウィンドウ:ログインのプロパティ](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>BankDemo データベースとそのすべてのオブジェクトを作成する

1. **Windows エクスプローラー**を開き、**C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL** に移動します。

2. **BankDemoCreateAll.SQL** ファイルの内容をクリップボードにコピーします。

3. **SSMS** を開きます。 右側で、 **[サーバー]** をクリックし、 **[新しいクエリ]** を選択します。

4. クリップボードの内容を **[新しいクエリ]** ボックス内に貼り付けます。

5. クエリの上の **[コマンド]** タブから **[実行]** をクリックして SQL を実行します。

クエリはエラーなく実行されます。 クエリが完了すると、BankDemo アプリケーション用のサンプル データベースが作成されます。

![SQLQuery1.sql の出力](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>データベース テーブルとオブジェクトが作成されたことを確認する

1. **BANKDEMO** データベースを右クリックし、 **[更新]** を選択します。

2. **[データベース]** を展開し、 **[テーブル]** を選択します。 画面表示は次のようになります。

     ![オブジェクト エクスプローラーで展開された BANKDEMO テーブル](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Enterprise Developer でアプリケーションをビルドする

1. Visual Studio を開いて、サインインします。

2. **[ファイル]** メニュー オプションで、 **[プロジェクト/ソリューションを開く]** を選択し、**C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo** に移動して、**sln** ファイルを選択します。

3. 少し時間をかけてオブジェクトを確認します。 ソリューション エクスプローラーには、CBL 拡張子が付いた COBOL プログラムのほか、CopyBook (CPY) や JCL が表示されます。

4. **BankDemo2** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

    > [!NOTE]
    > BankDemo プロジェクトでは、このデモでは使用されない HCOSS (Host Compatibility Option for SQL Server) が使用されます。

5. **ソリューション エクスプローラー**で、**BankDemo2** プロジェクトを右クリックし、 **[ビルド]** を選択します。

    > [!NOTE]
    > ソリューション レベルでビルドするとエラーが発生します (HCOSS が構成されていないため)。

6. プロジェクトがビルドされたら、 **[出力]** ウィンドウで結果を確認します。 出力は下の図のようになります。

     ![ビルドの成功を示す出力ウィンドウ](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>BankDemo アプリケーションをリージョン データベースにデプロイする

1. Enterprise Developer のコマンド プロンプト (64 ビット) を管理者として開きます。

2. **%PUBLIC%\\Documents\\Micro Focus\\Enterprise Developer\\samples\\Mainframe\\CICS\\DotNet\\BankDemo** に移動します。

3. コマンド プロンプトで、**bankdemodbdeploy** を実行し、デプロイ先となるデータベースのパラメーターを指定します。次に例を示します。

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> バックスラッシュ (\\) ではなく、フォワード スラッシュ (/) を使用してください。 このスクリプトの実行には少し時間がかかります。

![[Administration:Enterprise Developer Command Prompt]\(管理: Enterprise Developer コマンド プロンプト\) ウィンドウ](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Enterprise Administrator for .NET で BankDemo リージョンを作成する

1. **[Enterprise Server for .NET Administration]\(Enterprise Server for .NET の管理\)** UI を開きます。

2. MMC スナップインを起動するには、Windows の **[スタート]** メニューから **[Micro Focus Enterprise Developer] \> [構成] \> [Enterprise Server for .NET Admin]\(Enterprise Server for .NET の管理\)** を選択します(Windows Server の場合は、 **[Micro Focus Enterprise Developer] \> [Enterprise Server for .NET Admin]\(Enterprise Server for .NET の管理\)** を選択します)。

3. 左側のウィンドウで **[Regions]\(リージョン\)** コンテナーを展開し、 **[CICS]** を右クリックします。

4. **[Define Region]\(リージョンの定義\)** を選択して、(ローカルの) データベースでホストされる、**BANKDEMO** という新しい CICS リージョンを作成します。

5. データベース サーバー インスタンスを指定し、 **[Next]\(次へ\)** をクリックした後、リージョン名として「**BANKDEMO**」と入力します。

     ![[Define Region]\(リージョンの定義\) ダイアログ ボックス](media/07-demo-cics.png)

6. リージョンをまたいだデータベース用のリージョン定義ファイルを選択するには、**C:\\Users\\Public\\Documents\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo** で **region\_bankdemo\_db.config** を探します。

     ![Define Region - Region name:BANKDEMO](media/08-demo-cics.png)

7. **[完了]** をクリックします。

## <a name="create-xa-resource-definitions"></a>XA リソース定義を作成する

1. **[Enterprise Server for .NET Administration]\(Enterprise Server for .NET の管理\)** UI の左側のウィンドウで、 **[System]\(システム\)** を展開し、 **[XA Resource Definitions]\(XA リソース定義\)** を展開します。 この設定では、リージョンが Enterprise Server やアプリケーション データベースとどのように連携するかを定義します。

2. **[XA Resource Definitions]\(XA リソース定義\)** を右クリックし、 **[Add Server Instance]\(サーバー インスタンスの追加\)** を選択します。

3. ドロップダウン ボックスで、 **[Database Service Instance]\(データベース サービス インスタンス\)** を選択します。 これはローカル コンピューター SQLEXPRESS になります。

4. **[XA Resource Definitions]\(XA リソース定義\)(machinename\\sqlexpress)** コンテナーの下からインスタンスを選択し、 **[Add]\(追加\)** をクリックします。

5. **[Database XA Resource Definition]\(データベース XA リソース定義\)** を選択し、 **[Name]\(名前\)** と **[Region]\(リージョン\)** に「**BANKDEMO**」と入力します。

     ![[New Database XA Resource Definition]\(新しいデータベース XA リソース定義\) 画面](media/09-demo-xa.png)

6. 省略記号 ( **…** ) をクリックして、Connection String \(接続文字列\) ウィザードを起動します。 **[Server Name]\(サーバー名\)** には、「 **(local)\\SQLEXPRESS**」と入力します。 **[Logon]\(ログオン\)** では、 **[Windows Authentication]\(Windows 認証\)** を選択します。 データベース名については、「**BANKDEMO**」と入力します

     ![[Edit Connection String]\(接続文字列の編集\) 画面](media/10-demo-string.png)

7. 接続をテストします。

## <a name="start-the-bankdemo-region"></a>BANKDEMO リージョンを起動する

> [!NOTE]
> 最初の手順は重要です。このリージョンでは、先ほど作成した XA リソース定義を使用するように設定する必要があります。

1. **[Regions Container]\(リージョン コンテナー\)** の下の **[BANKDEMO CICS Region]\(BANKDEMO CICS リージョン\)** に移動し、 **[Actions]\(アクション\)** ウィンドウから **[Edit Region Startup File]\(リージョン スタートアップ ファイルの編集\)** を選択します。 SQL プロパティまで下へスクロールし、**XA リソース名**として「**bankdemo**」と入力するか、省略記号を使用して選択します。

2. **[Save]\(保存\)** をクリックして変更を保存します。

3. **[Console]\(コンソール\)** ウィンドウで **[BANKDEMO CICS Region]\(BANKDEMO CICS リージョン\)** を右クリックし、 **[Start/Stop Region]\(リージョンの起動/停止\)** を選択します。

4. 中央のウィンドウに表示された **[Start/Stop Region]\(リージョンの起動/停止\)** ボックスの下部で、 **[Start]\(起動\)** を選択します。 数秒後、リージョンが起動します。

     ![[SQL Start/Stop]\(SQLの起動/停止\) ボックス](media/11-demo-sql.png)

     ![[CICS Region BANKDEMO - Started]\(CICS リージョン BANKDEMO - 起動済み\) 画面](media/12-demo-cics.png)

## <a name="create-a-listener"></a>リスナーを作成する

BankDemo アプリケーションにアクセスする TN3270 セッション用のリスナーを作成します。

1. 左側のウィンドウで、 **[Configuration Editors]\(構成エディター\)** を展開し、 **[Listener]\(リスナー\)** を選択します。

2. **[Open File]\(ファイルを開く\)** アイコンをクリックし、**seelistener.exe.config** ファイルを選択します。 このファイルは編集され、Enterprise Server が起動するたびに読み込まれます。

3. 以前に定義された 2 つのリージョン (ESDEMO と JCLDEMO) があることを確認します。

4. BANKDEMO 用の新しいリージョンを作成するには、 **[Regions]\(リージョン)** を右クリックし、 **[Add Region]\(リージョンの追加\)** を選択します。

5. **[BANKDEMO Region]\(BANKDEMO リージョン\)** を選択します。

6. **[BANKDEMO Region]\(BANKDEMO リージョン\)** を右クリックし、 **[Add Channel]\(チャンネルの追加\)** を選択して TN3270 チャネルを追加します。

7. **[Name]\(名前)** に、「**TN3270**」と入力します。 **[Port]\(ポート)** には、「**9024**」と入力します ESDEMO アプリケーションでポート 9230 が使用されるので、別のポートを使用する必要があります。

8. ファイルを保存するには、 **[保存]** アイコンをクリックするか、 **[ファイル]** \> **[保存]** の順に選択します。

9. リスナーを起動するには、 **[Start Listener]\(リスナーの起動\)** アイコンをクリックするか、 **[Options]\(オプション\)** \> **[Start Listener]\(リスナーの起動\)** を選択します。

     ![[Listener Configuration Editor]\(リスナー構成エディター\) ウィンドウ](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>BankDemo アプリケーションにアクセスするように Rumba を構成する

最後に、Rumba (3270 エミュレーター) を使用して 3270 セッションを構成する必要があります。 この手順により、作成したリスナーを通じて BankDemo アプリケーションにアクセスできるようになります。

1. Windows の **[スタート]** メニューから、Rumba Desktop を起動します。

2. **[Connections]\(接続\)** メニュー項目で、 **[TN3270]** を選択します。

3. **[Insert]\(挿入\)** をクリックし、IP アドレスを「**127.0.0.1**」、ユーザー定義ポートを「**9024**」と入力します。

4. ダイアログ ボックスの下部で、 **[Connect]\(作成\)** をクリックします。 黒い CICS 画面が表示されます。

5. 「**bank**」と入力して BankDemo アプリケーションの初期画面である 3270 画面を表示します。

6. ユーザー ID は「**B0001**」と入力し、パスワードは任意の文字列を入力します。 初期画面の BANK20 が表示されます。

![Mainframe Display のようこそ画面](media/14-demo.png)
![Mainframe Display - Rumba - サブシステム デモ画面](media/15-demo.png)

お疲れさまでした。 これで、Micro Focus Enterprise Server を使用して CICS アプリケーションを Azure で実行できました。

## <a name="next-steps"></a>次のステップ

- [Azure の Docker コンテナーで Enterprise Server を実行する](run-enterprise-server-container.md)
- [メインフレーム移行 - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [トラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [メインフレームから Azure への移行の解明](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
