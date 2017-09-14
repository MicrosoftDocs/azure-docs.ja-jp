---
title: "SQL Server DB を Azure SQL Database に移行する | Microsoft Docs"
description: "SQL Server データベースを Azure SQL Database に移行する方法について説明します。"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>SQL Server データベースを Azure SQL Database に移行する

SQL Server データベースの Azure SQL Database への移動は 3 つの部分から成るプロセスです。最初にデータベースを準備し、次にエクスポートし、それからインポートします。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) を使用して Azure SQL Database に移行するために、SQL Server のデータベースを準備する
> * データベースを BACPAC ファイルにエクスポートする
> * BACPAC ファイルを Azure SQL Database にインポートする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- 最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) がインストールされていること。 SSMS をインストールすると、最新バージョンの SQLPackage コマンド ライン ユーティリティもインストールされます。このユーティリティを使用して、さまざまなデータベース開発タスクを自動化できます。 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) がインストールされていること。
- 移行するデータベースを指定してアクセスできます。 このチュートリアルでは SQL Server 2008 R2 以降のインスタンス上にある [SQL Server 2008 R2 AdventureWorks OLTP データベース](https://msftdbprodsamples.codeplex.com/releases/view/59211)を使用しますが、任意のデータベースを使用してかまいません。 互換性の問題を修正するには、[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を使用してください。

## <a name="prepare-for-migration"></a>移行を準備する

移行の準備をします。 **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** を使用して、Azure SQL Database に移行するデータベースの準備状態を評価するには、以下の手順に従います。

1. **Data Migration Assistant** を開きます。 移行するデータベースがある SQL Server インスタンスに接続できれば、どのコンピューターで DMA を使用してもかまいません。SQL Server インスタンスをホストしているコンピューターに DMA をインストールする必要はありません。

     ![Data Migration Assistant を開く](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. 左側のメニューで **[+ New]** (新規) をクリックし、**[Assessment]** (評価) を選択して、評価プロジェクトを作成します。 **[Project name]** (プロジェクト名) フィールドにプロジェクト名を入力し、その他の設定はすべて既定値のままにして、**[Create]** (作成) をクリックします。 **[Options]** (オプション) ページが開きます。

     ![Data Migration Assistant の新規プロジェクト](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. **[Options]** (オプション) ページで、**[Next]** (次へ) をクリックします。 **[Select sources]** (ソースの選択) ページが開きます。

     ![新規データ移行のオプション](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. **[Select sources]** (ソースの選択) ページで、移行元のサーバーがある SQL Server インスタンスの名前を入力します。 このページのその他の設定も必要に応じて変更します。 **[接続]**をクリックします。

     ![新規データ移行のソース選択](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. **[Select sources]** (ソースの選択) ページの **[Add sources]** (ソースの追加) 領域で、互換性をテストするデータベースのチェック ボックスを選択します。 **[追加]**をクリックします。

     ![新規データ移行のソース選択](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. **[Start Assessment]** (評価の開始) をクリックします。

     ![新規データ移行の評価開始](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. 評価が完了したら、まずは、データベースを移行するための十分な互換性があるかどうかを確認します。 チェック マークが入った緑色の円が表示されていることを確認します。

     ![新規データ移行の互換性評価の結果](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. 結果を確認します。 表示された **[SQL Server feature parity]\(SQL Server 機能パリティ\)** の結果は確認用の既定の結果です。 特に、サポートされない機能と部分的にサポートされる機能、および推奨される対応策に関する情報をよく確認します。 

     ![新規データ移行の評価パリティ](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. 左上にある [**互換性の問題**] をクリックし、互換性の問題を確認します。 特に、移行の阻害要因、動作の変更、各互換性レベルの非推奨機能に関する情報をよく確認します。 AdventureWorks2008R2 データベースの場合は、SQL Server 2008 以降のフルテキスト検索の変更と SQL Server 2000 以降の SERVERPROPERTY('LCID') の変更について確認します。 表示されるリンクから、これらの変更の詳細を見ることもできます。 フルテキスト検索については、多くの検索オプションと設定が変更されています。 

   > [!IMPORTANT] 
   > データベースを Azure SQL Database に移行した後で、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。
   >

10. 必要に応じて、**[Export report]** (レポートのエクスポート) をクリックして、レポートを JSON ファイルとして保存します。
11. Data Migration Assistant を閉じます。

## <a name="export-to-bacpac-file"></a>BACPAC ファイルへのエクスポート 

BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含み、BACPAC の拡張子を持つ ZIP ファイルです。 アーカイブ目的で、または SQL Server から Azure SQL Database などの移行目的で、BACPAC ファイルを Azure Blob Storage またはローカル ストレージに保存できます。 トランザクションの一貫性を維持してエクスポートするために、エクスポート中は書き込み処理が発生しないようにする必要があります。

SQLPackage コマンド ライン ユーティリティを使用して AdventureWorks2008R2 データベースをローカル ストレージにエクスポートするには、以下の手順に従います。

1. Windows コマンド プロンプトを開き、バージョン **130** の SQLPackage があるフォルダー (**C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin** など) にディレクトリを変更します。

2. コマンド プロンプトで次の SQLPackage コマンドを実行して、**AdventureWorks2008R2** データベースを **localhost** から **AdventureWorks2008R2.bacpac** にエクスポートします。 これらの値は、お使いの環境に合わせて変更してください。

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![sqlpackage エクスポート](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

実行が完了すると、sqlpackage 実行可能ファイルが置かれているディレクトリに、生成された BACPAC ファイルが格納されます。 この例では、C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin に格納されます。 

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com/)にログインします。 SQLPackage コマンド ライン ユーティリティを実行しているコンピューターからログインすると、手順 5 でファイアウォール規則を簡単に作成できます。

## <a name="create-a-sql-server-logical-server"></a>SQL サーバー (論理サーバー) を作成する

[SQL サーバー (論理サーバー)](sql-database-features.md) は、複数のデータベースの中央管理ポイントとして機能します。 移行後の Adventure Works OLTP SQL Server データベースを格納する SQL サーバー (論理サーバー) を作成するには、以下の手順に従います。 

1. Azure ポータルの左上隅にある **[新規]** ボタンをクリックします。

2. **[新規]** ページの検索フィールドに「**sql サーバー**」と入力して、検索結果の一覧から **[SQL サーバー (論理サーバー)]** を選択します。

    ![論理サーバーを選択する](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. **[Everything]** \(すべて) ページで、**[SQL server (logical server)]** \(SQL Server (論理サーバー)) をクリックしてから、**[SQL Server (logical server)]** \(SQL Server (論理サーバー)) ページで **[Create]** \(作成) をクリックします。

    ![論理サーバーを作成する](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. 前の画像で示されているように、[SQL server (logical server)] \(SQL Server (論理サーバー)) のフォームに次の情報を入力します。     

   | 設定       | 推奨値 | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **[サーバー名]** | グローバルに一意の名前を入力する | 有効なサーバー名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 | 
   | **[サーバー管理者ログイン]** | 有効な名前を入力する | 有効なログイン名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **パスワード** | 有効なパスワードを入力する | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
   | **サブスクリプション** | サブスクリプションの選択 | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | 既存のリソース グループを選択するか、新しいグループ (**myResourceGroup** など) を作成します。 |  有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **場所** | 有効な新しいサーバーの場所を入力する | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

   ![論理サーバー作成フォームへの入力](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. **[作成]** をクリックして論理サーバーをプロビジョニングします。 プロビジョニングには数分かかります。 

> [!IMPORTANT]
> サーバー名、サーバー管理者ログイン名、パスワードを忘れないようにしてください。 このチュートリアルで、後ほどこれらの値が必要になります。
>

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

SQL Database サービスは、外部のアプリケーションやツールに、サーバーまたはサーバー上のすべてのデータベースへの接続を禁止する[ファイアウォールをサーバーレベルで](sql-database-firewall-configure.md)作成します。それらに接続するためには、特定の IP アドレスに対してファイアウォールを開放するファイアウォール規則が作成されている必要があります。 SQLPackage コマンド ライン ユーティリティを実行しているコンピューターの IP アドレスに対して SQL Database のサーバーレベルのファイアウォール規則を作成するには、以下の手順に従います。 これにより、SQLPackage から Azure SQL Database ファイアウォールを介して SQL サーバー (論理サーバー) に接続できるようになります。 

1. 左側のメニューで **[All resources]** (すべてのリソース) をクリックし、**[All resources]** (すべてのリソース) ページで新しいサーバーをクリックします。 サーバーの [概要] ページが開き、さらに多くの構成オプションが表示されます。

     ![論理サーバーの概要](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. 概要ページの左側のメニューで、**[Settings]** (設定) の下にある **[Firewall]** (ファイアウォール) をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

3. ツールバーの **[クライアント IP の追加]** をクリックし、現在使用しているコンピューターの IP アドレスを追加したら、**[保存]** をクリックします。 この IP アドレスに対してサーバーレベルのファイアウォール規則が作成されます。

     ![サーバーのファイアウォール規則の設定](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. **[OK]**をクリックします。

これで、SQL Server Management Studio やその他の任意のツールを使用して、このサーバー上のすべてのデータベースに、前に作成したサーバー管理者アカウントでこの IP アドレスから接続できるようになりました。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>BACPAC ファイルを Azure SQL Database にインポートする 

最新バージョンの SQLPackage コマンド ライン ユーティリティでは、指定した[サービス レベルとパフォーマンス レベル](sql-database-service-tiers.md)で Azure SQL Database を作成できます。 インポート時はパフォーマンスを最大化するために、高いサービス レベルとパフォーマンス レベルを選択します。インポート後にそれほど高いサービス レベルとパフォーマンス レベルが直ちに必要ない場合は、スケールダウンします。

SQLPackage コマンド ライン ユーティリティを使用して AdventureWorks2008R2 データベースを Azure SQL Database にインポートするには、以下の手順に従います。 このタスクに SQL Server Management Studio を使用することはできますが、ほとんどの運用環境では SQLPackage のご使用をお勧めします。これにより、柔軟性を最大限に高め、最高のパフォーマンス得ることができます。 「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

- コマンド プロンプトで次の SQLPackage コマンドを実行して、**AdventureWorks2008R2** データベースをローカル ストレージから、前の手順で作成した SQL サーバー (論理サーバー) にインポートします。サービス レベルは **Premium**、サービス目標は **P6** とします。 山かっこ内の値を SQL サーバー (論理サーバー) の適切な値で置き換え、新しいデータベースの名前を指定します (山かっこも置き換えます)。 また、データベースのエディションおよびサービスの目標の値を、お使いの環境に合わせて適宜変更することもできます。 このチュートリアルでは、移行したデータベースを **myMigratedDatabase** と呼びます。

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![SQLPackage インポート](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> SQL サーバー (論理サーバー) はポート 1433 でリッスンします。 企業のファイアウォール内から SQL サーバー (論理サーバー) に接続する場合、企業のファイアウォールでこのポートが開放されていないと、正しく接続することができません。
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して接続する

SQL Server Management Studio を使用して、Azure SQL Database サーバーと新しく移行したデータベース (このチュートリアルでは **myMigratedDatabase** という名称) に対する接続を確立します。 SQLPackage を実行したコンピューターとは別のコンピューターで SQL Server Management Studio を実行している場合は、前述の手順に従って、このコンピューターに対するファイアウォール規則を作成してください。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。
   - **[サーバーの種類]**: データベース エンジンを指定します
   - **[サーバー名]**: 完全修飾サーバー名を入力します (**mynewserver20170403.database.windows.net** など)
   - **[認証]**: SQL Server 認証を指定します
   - **[ログイン]**: サーバー管理者アカウントを入力します
   - **[パスワード]**: サーバー管理者アカウントのパスワードを入力します
 
    ![SSMS で接続する](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. **[接続]**をクリックします。 オブジェクト エクスプローラー ウィンドウが開きます。 

4. オブジェクト エクスプローラーで、**Databases** フォルダー、**myMigratedDatabase** フォルダーの順に展開して、サンプル データベース内のオブジェクトを表示します。

## <a name="change-database-properties"></a>データベースのプロパティを変更する

SQL Server Management Studio を使用して、サービス レベル、パフォーマンス レベル、互換性レベルを変更できます。 インポート フェーズ中には、パフォーマンスを最大化するために、パフォーマンス レベルがより高いデータベースへインポートすることをお勧めしますが、インポートの完了後は、インポートされたデータベースを積極的に使用する準備が整うまで、スケールダウンして費用を節約することをお勧めします。 互換性レベルを変更すると、パフォーマンスが向上し、Azure SQL Database サービスの最新機能を使用できるようになります。 古いデータベースを移行する場合、データベースの互換性レベルは、インポートされるデータベースと互換性のある最低サポート レベルで維持されます。 詳細については、「[Azure SQL Database の互換性レベル 130 によるクエリ パフォーマンスの向上](sql-database-compatibility-level-query-performance-130.md)」を参照してください。

1. オブジェクト エクスプローラーで **myMigratedDatabase** を右クリックし、**[新しいクエリ]** をクリックします。 データベースに接続されたクエリ ウィンドウが開きます。

2. 次のコマンドを実行して、サービス レベルを **Standard**、パフォーマンス レベルを **S1** に設定します。

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![サービス レベルを変更する](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. 次のコマンドを実行して、データベースの互換性レベルを **130** に変更します。

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![互換性レベルを変更する](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>次のステップ 
このチュートリアルでは、データベースの準備、エクスポート、インポートを実行しました。 学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure SQL Database に移行するために、SQL Server のデータベースを準備する
> * データベースを BACPAC ファイルにエクスポートする
> * BACPAC ファイルを Azure SQL Database にインポートする

次のチュートリアルに進み、データベースをセキュリティで保護する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure SQL データベースのセキュリティ保護](sql-database-security-tutorial.md)



