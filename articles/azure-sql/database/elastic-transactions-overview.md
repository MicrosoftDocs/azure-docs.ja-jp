---
title: クラウド データベースにまたがる分散トランザクション (プレビュー)
description: Azure SQL Database と Azure SQL Managed Instance を使用したエラスティック データベース トランザクションの概要。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793349"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>クラウド データベースにまたがる分散トランザクション (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance のエラスティック データベース トランザクションは、複数のデータベースにまたがるトランザクションを実行する機能です。 エラスティック データベース トランザクションは、.NET アプリケーションから ADO.NET を介して利用できます。[System.Transaction](/dotnet/api/system.transactions) クラスを使用することで、これまでに培ったプログラミングの経験を活かすことが可能です。 ライブラリを入手するには、[.NET Framework 4.6.1 (Web インストーラー)](https://www.microsoft.com/download/details.aspx?id=49981) をご覧ください。
さらに、Managed Instance 分散トランザクションは、[Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) で使用できます。

従来、このようなシナリオをオンプレミスで実現するためには通常、Microsoft 分散トランザクション コーディネーター (MSDTC) が必要でした。 Azure における PaaS (Platform-as-a-Service) アプリケーションでは MSDTC が利用できないため、分散トランザクションの調整機能が SQL Database または Managed Instance に直接統合されました。 アプリケーションは、任意のデータベースに接続して分散トランザクションを開始できます。すると、いずれかのデータベースまたはサーバーによって分散トランザクションが透過的に調整されます。そのようすを示したのが次の図です。

このドキュメントでは、"分散トランザクション" と "エラスティック データベース トランザクション" を同義語と見なし、同じ意味で使用しています。

  ![エラスティック データベース トランザクションを使用した Azure SQL Database での分散トランザクション ][1]

## <a name="common-scenarios"></a>一般的なシナリオ

エラスティック データベース トランザクションの特長は、複数の異なるデータベースに格納されているデータに対して不可分な変更をアプリケーションから実行できることです。 プレビュー版では、C# と .NET によるクライアント側の開発に重点が置かれています。 [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) を使用したサーバー側のエクスペリエンス (ストアド プロシージャまたはサーバー側スクリプトで記述されたコード) は、Managed Instance のみで使用できます。
> [!IMPORTANT]
> プレビューの場合、Azure SQL Database と Azure SQL Managed Instance との間でエラスティック データベース トランザクションを実行することは、現時点ではサポートされていません。 エラスティック データベース トランザクションは、SQL Database のセットまたは Managed Instance のセットのどちらかのみにまたがって使用できます。

エラスティック データベース トランザクションが想定しているシナリオは次のとおりです。

* Azure におけるマルチデータベース アプリケーション:このシナリオでは、データが垂直にパーティション分割され、SQL Database または Managed Instance 内の複数のデータベースに格納されます (各データベースに異なる種類のデータが格納されます)。 場合によっては、特定の操作で変更対象となるデータが複数のデータベースにまたがって保存されていることがあります。 そこでアプリケーションからエラスティック データベース トランザクションを使用し、複数のデータベースに対する変更を調整し、原子性 (不可分な状態) を維持します。
* Azure におけるシャード化されたデータベース アプリケーション:このシナリオでは、データ層で [Elastic Database クライアント ライブラリ](elastic-database-client-library.md)または自己シャーディングを使用して、SQL Database または Managed Instance 内の複数のデータベースに対して水平にデータをパーティション分割します。 シャーディングされたマルチテナント アプリケーションで、不可分な変更を複数のテナントにまたがって実行しなければならないケースはその代表的な事例です。 たとえば、異なるデータベースに存在している 2 つのテナント間の転送が考えられます。 もう 1 つの事例としては、大規模なテナントの容量のニーズに応えるために、きめ細かなシャーディングを行うケースです。当然、同じテナント用の複数のデータベースに、不可分な操作を分散させる必要があります。 また、複数のデータベースにレプリケートされた参照データに対して不可分な更新を行うケースもあります。 こうした境界に沿って複数のデータベースに対して行う不可分なトランザクション操作が、このプレビュー機能によって調整できるようになりました。
  エラスティック データベース トランザクションは、2 フェーズ コミットを使用することで、データベース間のトランザクションの原子性を確保しています。 1 回のトランザクションの中で同時に扱うデータベースが 100 個未満のトランザクションには、この方法が適しています。 これらの制限に強制力はありませんが、制限を超えたときにエラスティック データベース トランザクションのパフォーマンスと成功率に生じる影響を見込んでおくことが必要です。

## <a name="installation-and-migration"></a>インストールと移行

エラスティック データベース トランザクションに必要な機能は、.NET ライブラリ System.Data.dll と System.Transactions.dll に対する更新プログラムを通じて提供されます。 これらの DLL によって、必要なときに確実に 2 フェーズ コミットが適用され、原子性が確保されます。 エラスティック データベース トランザクションを使ったアプリケーションを開発するには、 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 以降のバージョンをインストールしてください。 それより前のバージョンの .NET framework を実行していると、分散トランザクションへの昇格に失敗して例外が発生します。

インストール作業が完了すると、System.Transactions の分散トランザクション API と SQL Database または Managed Instance への接続を使用できるようになります。 これらの API を使った MSDTC アプリケーションが既に存在する場合は、4.6.1 Framework のインストール後、.NET 4.6 の既存のアプリケーションを再ビルドしてください。 プロジェクトのターゲット フレームワークが .NET 4.6 である場合は、新しい Framework バージョンの最新の DLL が自動的に使用され、分散トランザクション API 呼び出しと SQL Database または Managed Instance への接続に成功します。

エラスティック データベース トランザクションを使用するために MSDTC をインストールする必要はありません。 エラスティック データベース トランザクションは、サービスによって (サービス内部で) 直接管理されます。 SQL Database または Managed Instance での分散トランザクションには MSDTC のデプロイが不要であるため、クラウドのシナリオが大幅に単純化されます。 エラスティック データベース トランザクションと必須の .NET Framework をクラウド アプリケーションと併せて Azure にデプロイする方法については、セクション 4 で詳しく説明しています。

## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services の .NET インストール

Azure には、.NET アプリケーションをホストするためのいくつかのサービスが用意されています。 さまざまなサービスを比較するには、「 [Azure App Service、Cloud Services、および Virtual Machines の比較](/azure/architecture/guide/technology-choices/compute-decision-tree)」をご覧ください。 サービスのゲスト OS がエラスティック トランザクションに必要な .NET 4.6.1 より小さい場合は、ゲスト OS を 4.6.1 にアップグレードする必要があります。

Azure App Services では、ゲスト OS のアップグレードは現在サポートされていません。 Azure Virtual Machines では、単に VM にログインし、最新の .NET Framework のインストーラーを実行します。 Azure Cloud Services では、新しいバージョンの .NET のインストールをデプロイのスタートアップ タスクに含める必要があります。 その概念と手順については、「 [クラウド サービスのロールに .NET をインストールする](../../cloud-services/cloud-services-dotnet-install-dotnet.md)」を参照してください。  

.NET 4.6.1 のインストーラーは、Azure クラウド サービスでのブートストラップ プロセス中に、.NET 4.6 のインストーラーよりも一時的なストレージを多く必要とする場合があることに注意してください。 正常かつ確実にインストールするには、次の例に示すように、ServiceDefinition.csdef ファイルの LocalResources セクションと、スタートアップ タスクの環境設定で、Azure クラウド サービスの一時的なストレージを増やす必要があります。

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET 開発エクスペリエンス

### <a name="multi-database-applications"></a>マルチデータベース アプリケーション

次のサンプル コードは、.NET System.Transactions を使ったなじみ深いプログラミング技法によって記述しています。 TransactionScope クラスは、.NET でアンビエント トランザクションを確立するものです。 ("アンビエント トランザクション" とは現在のスレッドで実行されているトランザクションです。)TransactionScope 内で開かれたすべての接続はトランザクションに参加します。 複数の異なるデータベースが参加した場合、そのトランザクションは自動的に分散トランザクションに昇格されます。 トランザクションの最終的な結果は、スコープの完了 (コミットを意味する) を設定することによって制御します。

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>シャード化されたデータベース アプリケーション

SQL Database および Managed Instance のエラスティック データベース トランザクションで、分散トランザクションの調整を行うこともできます。この場合、スケール アウトされたデータ層に使用する接続は、エラスティック データベース クライアント ライブラリの OpenConnectionForKey メソッドを使用して開きます。 たとえば、複数の異なるシャーディング キー値に対する変更で、トランザクションの一貫性を保証するとします。 異なるシャーディング キー値を持ったシャードに対する接続は、OpenConnectionForKey を使って取得することができます。 一般に、トランザクションの保証に分散トランザクションが伴うように、異なるシャードへの接続が取得されます。
次のコード サンプルに、この方法を示します。 ここでは、エラスティック データベース クライアント ライブラリのシャード マップを shardmap という変数で表しています。

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-SQL 開発エクスペリエンス

Transact-SQL を使用したサーバー側の分散トランザクションは、Azure SQL Managed Instance でのみ使用できます。 分散トランザクションは、同じ[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)に属する Managed Instance 間でのみ実行できます。 このシナリオでは、Managed Instance の相互参照のために、[リンク サーバー](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure)を使用する必要があります。

次の Transact-SQL コードの例では、[BEGIN DISTRIBUTED TRANSACTION](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) を使用して分散トランザクションを開始しています。

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>.NET と Transact-SQL の開発環境の組み合わせ

.NET アプリケーションで System.Transaction クラスを使用すると、TransactionScope クラスと Transact-SQL ステートメント BEGIN DISTRIBUTED TRANSACTION を組み合わせることができます。 TransactionScope 内で BEGIN DITRIBUTED TRANSACTION を実行する内部トランザクションは、明示的に分散トランザクションに昇格されます。 また、TransactionScope 内で 2 つ目の SqlConnection が開かれると、それは暗黙的に分散トランザクションに昇格されます。 分散トランザクションが開始されると、それ以降のすべてのトランザクション要求は、送信元が .NET であるか Transact-SQL であるかにかかわらず、親の分散トランザクションに結合されます。 結果として、BEGIN ステートメントによって開始された入れ子になったトランザクション スコープはすべて同じトランザクションになり、COMMIT または ROLLBACK ステートメントは全体的な結果に対して次のように作用します。
 * COMMIT ステートメントは、BEGIN ステートメントによって開始されたトランザクション スコープには作用しません。つまり、TransactionScope オブジェクトに対して Complete() メソッドが呼び出される前に結果がコミットされることはありません。 TransactionScope オブジェクトが完了前に破棄された場合、スコープ内で行われたすべての変更がロールバックされます。
 * ROLLBACK ステートメントを実行すると、TransactionScope 全体がロールバックされます。 TransactionScope 内に新しいトランザクションを参加させる試みは、TransactionScope オブジェクトに対して Complete () を呼び出す試みと同様に、後で失敗します。

トランザクションが分散トランザクションに明示的に昇格される Transact-SQL での例を次に示します。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

次の例は、2 つ目の SqlConnection が TransactionScope 内で開始された後に、暗黙的に分散トランザクションに昇格されるトランザクションを示しています。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Azure SQL Database の複数のサーバーにまたがるトランザクション

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

エラスティック データベース トランザクションは、Azure SQL Database の複数のサーバーに対して実行できます。 トランザクションがサーバーの境界を超えた場合、参加するサーバーが最初に双方向の通信リレーションシップに入る必要があります。 通信リレーションシップが確立されると、2 つのサーバーのいずれのデータベースも、もう一方のサーバーのデータベースを使用してエラスティック トランザクションに参加できます。 2 つのサーバーにまたがるトランザクションでは、サーバーの任意のペア用に通信リレーションシップが用意されている必要があります。

次の PowerShell コマンドレットを使って、エラスティック データベースのトランザクション用のサーバー間通信リレーションシップを管理できます。

* **New-AzSqlServerCommunicationLink**:このコマンドレットを使用して Azure SQL Database で 2 つのサーバー間に新しい通信リレーションシップを構築します。 リレーションシップは対称です。つまり、いずれのサーバーも他方のサーバーとのトランザクションを開始できます。
* **Get-AzSqlServerCommunicationLink**:このコマンドレットを使用して既存の通信リレーションシップとそのプロパティを取得します。
* **Remove-AzSqlServerCommunicationLink**:このコマンドレットを使用して既存の通信リレーションシップを削除します。

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance の複数のサーバーにまたがるトランザクション

分散トランザクションは、Azure SQL Managed Instance の複数のサーバーに対して実行できます。 トランザクションが Managed Instance の境界を越える場合、参加するインスタンスが最初に双方向のセキュリティおよび通信リレーションシップに入る必要があります。 これを行うには、[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)を作成します。これは Azure portal で行うことができます。 Managed Instance が同じ仮想ネットワーク上にない場合は、[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)を設定する必要があります。また、ネットワーク セキュリティ グループのインバウンドおよびアウトバウンド規則で、参加しているすべての仮想ネットワーク上でポート 5024 と 11000 - 12000 を許可する必要があります。

  ![Azure portal のサーバー信頼グループ][3]

次の図は、.NET または Transact-SQL を使用して分散トランザクションを実行できる Managed Instance を含むサーバー信頼グループを示しています。

  ![エラスティック トランザクションを使用した Azure SQL Managed Instance での分散トランザクション][2]

## <a name="monitoring-transaction-status"></a>トランザクションの状態の監視

現在実行されているエラスティック データベース トランザクションの状態と進行状況は、動的管理ビュー (DMV) を使用して監視します。 トランザクションに関連したすべての DMV は、SQL Database と Managed Instance の分散トランザクションにとって重要となります。 対応する DMV の一覧については、「[トランザクション関連の動的管理ビューおよび関数 (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql)」を参照してください。

次の DMV が特に重要となります。

* **sys.dm\_tran\_active\_transactions**:現在アクティブなトランザクションとその状態を一覧表示します。 同じ分散トランザクションに属している子トランザクションは、UOW (Unit Of Work: 作業単位) 列で確認できます。 同じ分散トランザクションに属しているトランザクションはすべて同じ UOW 値を共有します。 詳細については、[DMV ドキュメント](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)を参照してください。
* **sys.dm\_tran\_database\_transactions**:トランザクションに関する追加情報 (ログにおけるトランザクションの位置など) が表示されます。 詳細については、[DMV ドキュメント](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)を参照してください。
* **sys.dm\_tran\_locks**:実行中のトランザクションによって現在保持されているロックの情報が表示されます。 詳細については、[DMV ドキュメント](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)を参照してください。

## <a name="limitations"></a>制限事項

SQL Database のエラスティック データベース トランザクションには現在、次の制限が適用されます。

* サポートされるトランザクションの対象は、SQL Database 内のデータベースに限られます。 その他の [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) リソース プロバイダーや SQL Database 以外のデータベースがエラスティック データベース トランザクションに参加することはできません。 つまり、オンプレミス SQL Server と Azure SQL Database にまたがってエラスティック データベース トランザクションを実行することはできません。 オンプレミスの分散トランザクションについては、引き続き MSDTC をご利用ください。
* サポートされるのは、.NET アプリケーションからクライアント側で調整されるトランザクションだけです。 将来的には、サーバー側の T-SQL サポート (BEGIN DISTRIBUTED TRANSACTION など) が予定されていますが、現時点では利用できません。
* WCF サービスをまたがるトランザクションはサポートされません。 たとえば、トランザクションを実行する WCF サービス メソッドがあるとします。 トランザクション スコープ内にこの呼び出しを囲い込むと、 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)として失敗します。

Managed Instance の分散トランザクションには現在、次の制限が適用されます。

* サポートされるトランザクションの対象は、Managed Instance 内のデータベースに限られます。 その他の [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) リソース プロバイダーや Azure SQL Managed Instance 以外のデータベースが分散トランザクションに参加することはできません。 つまり、オンプレミス SQL Server と Azure SQL Managed Instance にまたがって分散トランザクションを実行することはできません。 オンプレミスの分散トランザクションについては、引き続き MSDTC をご利用ください。
* WCF サービスをまたがるトランザクションはサポートされません。 たとえば、トランザクションを実行する WCF サービス メソッドがあるとします。 トランザクション スコープ内にこの呼び出しを囲い込むと、 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)として失敗します。
* 分散トランザクションに参加するには、Azure SQL Managed Instance が[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)に属している必要があります。
* [サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)の制限事項が分散トランザクションに影響します。
* 分散トランザクションに参加する Managed Instance は、プライベート エンドポイント (デプロイ先の仮想ネットワークのプライベート IP アドレスを使用) を介して接続する必要があり、プライベート FQDN を使用して相互参照される必要があります。 クライアント アプリケーションからプライベート エンドポイントで分散トランザクションを使用できます。 さらに、プライベート エンドポイントを参照するリンク サーバーを Transact-SQL で利用する場合、クライアント アプリケーションからパブリック エンドポイントでも分散トランザクションを使用できます。 この制限について次の図で説明します。
  ![プライベート エンドポイントの接続に関する制限][4]
## <a name="next-steps"></a>次の手順

* 質問がある場合は、[SQL Database に関する Microsoft Q&A 質問ページ](/answers/topics/azure-sql-database.html)から Microsoft にご連絡ください。
* 機能に関する要望については、[SQL Database フィードバック フォーラム](https://feedback.azure.com/forums/217321-sql-database/)または [Managed Instance フォーラム](https://feedback.azure.com/forums/915676-sql-managed-instance)に追加してください。



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
