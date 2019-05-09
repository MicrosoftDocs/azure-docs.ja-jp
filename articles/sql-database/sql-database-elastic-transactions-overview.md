---
title: クラウド データベースにまたがる分散トランザクション
description: Azure SQL Database を使用した Elastic Database トランザクションの概要
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: d7865d394dfc955a7b24115e747dd77352d89e3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901918"
---
# <a name="distributed-transactions-across-cloud-databases"></a>クラウド データベースにまたがる分散トランザクション

Azure SQL Database (SQL DB) のエラスティック データベース トランザクションは、SQL DB 内の複数のデータベースにまたがるトランザクションを実行する機能です。 SQL DB の Elastic Database トランザクションは、.NET アプリケーションから ADO .NET を介して利用できます。[System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) クラスを使用することで、これまでに培ったプログラミングの経験を活かすことが可能です。 ライブラリを入手するには、[.NET Framework 4.6.1 (Web インストーラー)](https://www.microsoft.com/download/details.aspx?id=49981) をご覧ください。

従来、このようなシナリオをオンプレミスで実現するためには通常、Microsoft 分散トランザクション コーディネーター (MSDTC) が必要でした。 Azure における PaaS (Platform-as-a-Service) アプリケーションでは MSDTC が利用できないため、分散トランザクションの調整機能が SQL DB に直接統合されました。 アプリケーションは、任意の SQL Database に接続して分散トランザクションを開始できます。すると、いずれかのデータベースによって分散トランザクションが透過的に調整されます。そのようすを示したのが次の図です。 

  ![エラスティック データベース トランザクションを使用した Azure SQL Database での分散トランザクション ][1]

## <a name="common-scenarios"></a>一般的なシナリオ

SQL DB のエラスティック データベース トランザクションの特長は、複数の異なる SQL Database に格納されているデータに対して不可分な変更をアプリケーションから実行できることです。 プレビュー版では、C# と .NET によるクライアント側の開発に重点が置かれています。 T-SQL を使用したサーバー側の開発も今後予定されています。  
エラスティック データベース トランザクションが想定しているシナリオは次のとおりです。

* Azure におけるマルチデータベース アプリケーション:このシナリオでは、データが垂直にパーティション分割され、SQL DB 内の複数のデータベースに格納されます (各データベースに異なる種類のデータが格納されます)。 場合によっては、特定の操作で変更対象となるデータが複数のデータベースにまたがって保存されていることがあります。 そこでアプリケーションからエラスティック データベース トランザクションを使用し、複数のデータベースに対する変更を調整し、原子性 (不可分な状態) を維持します。
* Azure におけるシャード化されたデータベース アプリケーション:このシナリオでは、データ層で [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)または自己シャーディングを使用して、SQL DB 内の複数のデータベースに対して水平にデータをパーティション分割します。 シャーディングされたマルチテナント アプリケーションで、不可分な変更を複数のテナントにまたがって実行しなければならないケースはその代表的な事例です。 たとえば、異なるデータベースに存在している 2 つのテナント間の転送が考えられます。 もう 1 つの事例としては、大規模なテナントの容量のニーズに応えるために、きめ細かなシャーディングを行うケースです。当然、同じテナント用の複数のデータベースに、不可分な操作を分散させる必要があります。 また、複数のデータベースにレプリケートされた参照データに対して不可分な更新を行うケースもあります。 こうした境界に沿って複数のデータベースに対して行う不可分なトランザクション操作が、このプレビュー機能によって調整できるようになりました。
  エラスティック データベース トランザクションは、2 フェーズ コミットを使用することで、データベース間のトランザクションの原子性を確保しています。 1 回のトランザクションの中で同時に扱うデータベースが 100 個未満のトランザクションには、この方法が適しています。 これらの制限に強制力はありませんが、制限を超えたときにエラスティック データベース トランザクションのパフォーマンスと成功率に生じる影響を見込んでおくことが必要です。

## <a name="installation-and-migration"></a>インストールと移行

SQL DB のエラスティック データベース トランザクションに必要な機能は、.NET ライブラリ System.Data.dll と System.Transactions.dll に対する更新プログラムを通じて提供されます。 これらの DLL によって、必要なときに確実に 2 フェーズ コミットが適用され、原子性が確保されます。 エラスティック データベース トランザクションを使ったアプリケーションを開発するには、 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 以降のバージョンをインストールしてください。 それより前のバージョンの .NET framework を実行していると、分散トランザクションへの昇格に失敗して例外が発生します。

インストール作業が完了すると、System.Transactions の分散トランザクション API と SQL DB への接続を使用できるようになります。 これらの API を使った MSDTC アプリケーションが既に存在する場合は、4.6.1 Framework のインストール後、単に .NET 4.6 の既存のアプリケーションを再ビルドしてください。 プロジェクトのターゲット フレームワークが .NET 4.6 である場合は、新しい Framework バージョンの最新の DLL が自動的に使用され、分散トランザクション API 呼び出しと SQL DB への接続に成功します。

エラスティック データベース トランザクションを使用するために MSDTC をインストールする必要はありません。 エラスティック データベース トランザクションは、SQL DB 内で直接管理されます。 SQL DB での分散トランザクションには MSDTC のデプロイが不要であるため、クラウドのシナリオが大幅に単純化されます。 エラスティック データベース トランザクションと必須の .NET Framework をクラウド アプリケーションと併せて Azure にデプロイする方法については、セクション 4 で詳しく説明しています。

## <a name="development-experience"></a>開発環境

### <a name="multi-database-applications"></a>マルチデータベース アプリケーション

次のサンプル コードは、.NET System.Transactions を使ったなじみ深いプログラミング技法によって記述しています。 TransactionScope クラスは、.NET でアンビエント トランザクションを確立するものです。 ("アンビエント トランザクション" とは現在のスレッドで実行されているトランザクションです。)TransactionScope 内で開かれたすべての接続はトランザクションに参加します。 複数の異なるデータベースが参加した場合、そのトランザクションは自動的に分散トランザクションに昇格されます。 トランザクションの最終的な結果は、スコープの完了 (コミットを意味する) を設定することによって制御します。

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

### <a name="sharded-database-applications"></a>シャード化されたデータベース アプリケーション

SQL DB のエラスティック データベース トランザクションで、分散トランザクションの調整を行うこともできます。この場合、スケール アウトされたデータ層に使用する接続は、エラスティック データベース クライアント ライブラリの OpenConnectionForKey メソッドを使用して開きます。 たとえば、複数の異なるシャーディング キー値に対する変更で、トランザクションの一貫性を保証するとします。 異なるシャーディング キー値を持ったシャードに対する接続は、OpenConnectionForKey を使って取得することができます。 一般に、トランザクションの保証に分散トランザクションが伴うように、異なるシャードへの接続が取得されます。 次のコード サンプルに、この方法を示します。 ここでは、エラスティック データベース クライアント ライブラリのシャード マップを shardmap という変数で表しています。

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services の .NET インストール

Azure には、.NET アプリケーションをホストするためのいくつかのサービスが用意されています。 さまざまなサービスを比較するには、「 [Azure App Service、Cloud Services、および Virtual Machines の比較](../app-service/overview-compare.md)」をご覧ください。 サービスのゲスト OS がエラスティック トランザクションに必要な .NET 4.6.1 より小さい場合は、ゲスト OS を 4.6.1 にアップグレードする必要があります。 

Azure App Services では、ゲスト OS のアップグレードは現在サポートされていません。 Azure Virtual Machines では、単に VM にログインし、最新の .NET Framework のインストーラーを実行します。 Azure Cloud Services では、新しいバージョンの .NET のインストールをデプロイのスタートアップ タスクに含める必要があります。 その概念と手順については、「 [クラウド サービスのロールに .NET をインストールする](../cloud-services/cloud-services-dotnet-install-dotnet.md)」を参照してください。  

.NET 4.6.1 のインストーラーは、Azure クラウド サービスでのブートストラップ プロセス中に、.NET 4.6 のインストーラーよりも一時的なストレージを多く必要とする場合があることに注意してください。 正常かつ確実にインストールするには、次の例に示すように、ServiceDefinition.csdef ファイルの LocalResources セクションと、スタートアップ タスクの環境設定で、Azure クラウド サービスの一時的なストレージを増やす必要があります。

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

## <a name="transactions-across-multiple-servers"></a>複数のサーバーにまたがるトランザクション

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

エラスティック データベース トランザクションは、Azure SQL Database のさまざまな SQL Database サーバーでサポートされています。 トランザクションが SQL Database サーバーの境界を超える場合、参加するサーバーが最初に双方向の通信リレーションシップに入る必要があります。 通信リレーションシップが確立されると、2 つのサーバーのいずれのデータベースも、もう一方のサーバーのデータベースを使用してエラスティック トランザクションに参加できます。 2 つの SQL Database サーバーにまたがるトランザクションでは、SQL Database サーバーの任意のペア用に通信リレーションシップが用意されている必要があります。

次の PowerShell コマンドレットを使って、エラスティック データベースのトランザクション用のサーバー間通信リレーションシップを管理できます。

* **New-AzSqlServerCommunicationLink**:このコマンドレットを使用して Azure SQL Database で 2 つの SQL Database サーバー間に新しい通信リレーションシップを構築します。 リレーションシップは対象です。つまり、いずれのサーバーも他方のサーバーとのトランザクションを開始できます。
* **Get-AzSqlServerCommunicationLink**:このコマンドレットを使用して既存の通信リレーションシップとそのプロパティを取得します。
* **Remove-AzSqlServerCommunicationLink**:このコマンドレットを使用して既存の通信リレーションシップを削除します。 

## <a name="monitoring-transaction-status"></a>トランザクションの状態の監視

現在実行されているエラスティック データベース トランザクションの状態と進行状況は、SQL DB の動的管理ビュー (DMV) を使用して監視します。 トランザクションに関連したすべての DMV は、SQL DB の分散トランザクションにとって重要となります。 対応する DMV の一覧については、「[トランザクション関連の動的管理ビューおよび関数 (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)」を参照してください。

次の DMV が特に重要となります。

* **sys.dm\_tran\_active\_transactions**:現在アクティブなトランザクションとその状態を一覧表示します。 同じ分散トランザクションに属している子トランザクションは、UOW (Unit Of Work: 作業単位) 列で確認できます。 同じ分散トランザクションに属しているトランザクションはすべて同じ UOW 値を共有します。 詳細については、[DMV ドキュメント](https://msdn.microsoft.com/library/ms174302.aspx)をご覧ください。
* **sys.dm\_tran\_database\_transactions**:トランザクションに関する追加情報 (ログにおけるトランザクションの位置など) が表示されます。 詳細については、[DMV ドキュメント](https://msdn.microsoft.com/library/ms186957.aspx)をご覧ください。
* **sys.dm\_tran\_locks**:実行中のトランザクションによって現在保持されているロックの情報が表示されます。 詳細については、[DMV ドキュメント](https://msdn.microsoft.com/library/ms190345.aspx)をご覧ください。

## <a name="limitations"></a>制限事項

SQL DB のエラスティック データベース トランザクションには現在、次の制限が適用されます。

* サポートされるトランザクションの対象は、SQL DB 内のデータベースに限られます。 その他の [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) リソース プロバイダーや SQL DB 以外のデータベースがエラスティック データベース トランザクションに参加することはできません。 つまり、オンプレミス SQL Server と Azure SQL Database にまたがってエラスティック データベース トランザクションを実行することはできません。 オンプレミスの分散トランザクションについては、引き続き MSDTC をご利用ください。 
* サポートされるのは、.NET アプリケーションからクライアント側で調整されるトランザクションだけです。 将来的には、サーバー側の T-SQL サポート (BEGIN DISTRIBUTED TRANSACTION など) が予定されていますが、現時点では利用できません。 
* WCF サービスをまたがるトランザクションはサポートされません。 たとえば、トランザクションを実行する WCF サービス メソッドがあるとします。 トランザクション スコープ内にこの呼び出しを囲い込むと、 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)として失敗します。

## <a name="next-steps"></a>次の手順

質問がある場合は、[SQL Database のフォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)に投稿してください。機能に関するご要望は、[SQL Database に関するフィードバック フォーラム](https://feedback.azure.com/forums/217321-sql-database/)にお寄せください。

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
