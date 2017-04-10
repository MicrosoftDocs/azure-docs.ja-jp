---
title: "Azure Portal を使用した Azure SQL Database V12 へのアップグレード | Microsoft Docs"
description: "Web データベースと Business データベースのアップグレード方法を含む Azure SQL Database V12 へのアップグレード方法を説明します。また、Azure ポータルを使用してエラスティック プールにデータベースを直接移行することで V11 サーバーをアップグレードする方法も説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 81a59ada-c459-4437-ad25-4346b75454ac
ms.service: sql-database
ms.custom: V11
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/08/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: bc7fcebaebe510e1ce00e6f55b4db7787e5ce53c


---
# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Azure ポータルを使用した Azure SQL Database V12 へのアップグレード
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-upgrade-server-portal.md)
> * [PowerShell](sql-database-upgrade-server-powershell.md)
> 
> 

SQL Database V12 が最新バージョンであるため、SQL Database V12 にアップグレードすることをお勧めします。 Azure SQL Database と SQL Server を比較した機能マトリックスについては、「[Azure SQL Database の機能](sql-database-features.md)」を参照してください。

この記事では、既存の SQL Database V11 サーバーとデータベースを SQL Database V12 にアップグレードする方法について説明します。

V12 にアップグレードする過程で、すべての Web および Business データベースを新しいサービス レベルにアップグレードするため、Web および Business データベースのアップグレードに関する説明も含まれています。

また、[エラスティック プール](sql-database-elastic-pool.md)への移行は、スタンドアロン データベースの場合に個々のパフォーマンス レベル (価格レベル) にアップグレードするよりもコスト効率が良くなる可能性があります。 プールを使用してデータベース管理を簡略化することもできます。これは、個々のデータベースのパフォーマンス レベルを個別に管理するのではなく、プールのパフォーマンス設定を管理するだけでよいからです。 複数のサーバーにデータベースがある場合は、それらを同じサーバーに移動し、それらを 1 つのプールに置くと便利です。 簡単に、[PowerShell を使用して、V11 サーバーからエラスティック プールにデータベースを自動で直接移行](sql-database-upgrade-server-powershell.md)できます。 ポータルを使用して V11 データベースをプールに移行することもできますが、ポータルにはプールを作成するための V12 サーバーが既にある必要があります。 [プールからメリットを得られるデータベース](sql-database-elastic-pool-guidance.md)がある場合は、この記事の後の方に記載されている説明を参照して、サーバーのアップグレード後にプールを作成してください。

アップグレード操作全体にわたって、データベースはオンラインのままであり、機能し続けます。 新しいパフォーマンス レベルに実際に切り替えるときには、データベースへの接続が非常に短い時間、一時的に解除される場合があります。多くの場合、これは 90 秒程度ですが、最長で 5 分間ほど解除される場合もあります。 [接続が終了した場合に発生する一時的なエラーに対処するための機能](sql-database-connectivity-issues.md)がアプリケーションに備わっている場合は、アップグレードの終了時の接続解除に対して保護するだけで十分です。

SQL Database V12 へのアップグレードを元に戻すことはできません。 アップグレードした後に、サーバーを V11 に戻すことはできません。

V12 にアップグレードした後、[サービス レベルの推奨事項](sql-database-service-tier-advisor.md)と[エラスティック プールのパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)は、サービスが新しいサーバーでのワークロードを評価し終わるまで、すぐには利用できません。 V11 サーバーの推奨履歴は V12 サーバーには適用されないため、履歴は保持されません。

## <a name="prepare-to-upgrade"></a>アップグレードの準備
* **すべての Web と Business データベースのアップグレード**: 後述の「[すべての Web データベースと Business データベースのアップグレード](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases)」か、「[エラスティック プールの監視と管理 (PowerShell)](sql-database-elastic-pool-manage-powershell.md)」をご覧ください。
* **geo レプリケーションをレビューして一時停止**: Azure SQL Database が geo レプリケーション用に構成されている場合、現在の構成を文書化し、 [geo レプリケーションを停止](sql-database-geo-replication-portal.md#remove-secondary-database)する必要があります。 アップグレードが完了したら、geo レプリケーション用にデータベースを再構成します。
* **Azure VM にクライアントがある場合、これらのポートを開く**: クライアントが Azure 仮想マシン (VM) で実行されるときに、クライアント プログラムが SQL Database V12 に接続する場合、VM でポート範囲の 11000 ～ 11999 と 14000 ～ 14999 を開く必要があります。 詳細については、「 [SQL Database V12のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」をご覧ください。

## <a name="start-the-upgrade"></a>アップグレードを開始する
1. [Azure Portal](https://portal.azure.com/) で、**[参照]** > **[SQL サーバー]** を選択し、アップグレードする v2.0 サーバーを選択します。
2. **[最新の SQL Database Update]** を選択し、**[このサーバーをアップグレード]** を選択します。
   
      ![サーバーのアップグレード][1]
3. 最新の SQL Database Update へのサーバーのアップグレードは永続的であり、元に戻すことはできません。 アップグレードを確認するには、サーバーの名前を入力し、 **[OK]**をクリックします。

## <a name="upgrade-all-web-and-business-databases"></a>すべての Web および Business データベースのアップグレード
サーバーに Web データベースまたは Business データベースがある場合は、それらをアップグレードする必要があります。 SQL Database V12 へのアップグレードの過程で、すべての Web/Business データベースを新しいサービス レベルに更新します。    

アップグレードする際、SQL Database サービスによって、各データベースに適したサービス層とパフォーマンス レベル (価格レベル) が推奨されます。 サービスでは、データベースの使用履歴を分析することで、既存データベースのワークロードを実行するための最適なレベルを使用することが推奨されます。

1. **[このサーバーをアップグレード]** ブレードで、各データベースを選択して見直しを行い、アップグレードするための推奨価格レベルを選択します。 利用できる価格レベルはいつでも参照でき、お使いの環境に最適な価格レベルを選択できます。

     ![データベース][2]


1. 提案されているレベルをクリックすると、**[価格レベルの選択]** ブレードが表示されます。このブレードでレベルを選択し、**[選択]** をクリックして、そのレベルに変更できます。 Web データベースまたは Business データベースごとに新しいレベルを選択します。
   
    SQL Database は特定のサービス レベルやパフォーマンス レベルに固定されるわけではないことに注意してください。このため、データベースの要件の変化に応じて、使用可能なサービス レベルとパフォーマンス レベル間での変更は簡単に行えます。 実際、Basic、Standard、Premium の SQL Database は時間単位で課金され、各データベースは 24 時間に 4 回スケールアップまたはスケールダウンすることができます。
   
    ![推奨事項][6]

サーバー上のすべてのデータベースを処理した後、アップグレードを開始できます。

## <a name="confirm-the-upgrade"></a>アップグレードの確認
1. サーバー上のすべてのデータベースをアップグレードの対象にしたら、アップグレードを実行することを確認するために**サーバー名を入力**し、**[OK]** をクリックします。
   
    ![アップグレードの確認][3]
2. アップグレードが開始され、進行中であることを通知するメッセージが表示されます。 アップグレード処理が始まります。 お使いのデータベースの詳細によっては、V12 へのアップグレードに時間がかかることがあります。 その間、サーバー上のすべてのデータベースはオンラインのままですが、サーバーとデータベースの管理アクションは制限されます。
   
    ![アップグレードが進行中][4]
   
    新しいパフォーマンス レベルに実際に移行するときには、データベースへの接続が非常に短い時間 (通常は数秒)、一時的に解除される場合があります。 接続が終了した場合に発生する一時的なエラーに対処するための機能 (再試行ロジック) がアプリケーションに備わっている場合は、アップグレードの終了に発生する接続解除に備えるだけで十分です。
3. アップグレード操作が完了したら、**[最新の更新プログラム]** ブレードに **[有効]** が表示されます。
   
    ![V12 の有効化][5]  

## <a name="move-your-databases-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動
[Azure ポータル](https://portal.azure.com/) で、V12 サーバーを参照して **[プールの追加]**をクリックします。

または

**[ここをクリックして、このサーバーの推奨エラスティック プールを表示します]**というメッセージが表示されたら、それをクリックして、サーバーのデータベースに最適化されたプールを簡単に作成できます。 詳細については、「[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)」をご覧ください。

![サーバーへのプールの追加][7]

「[エラスティック プールの作成](sql-database-elastic-pool.md)」の記事の説明に従って、プールの作成を完了します。

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>SQL Database V12 へのアップグレード後のデータベースの監視
> [!IMPORTANT]
> 新しい V12 の機能を利用するには、最新バージョンの SQL Server Management Studio (SSMS) にアップグレードします。 [SQL Server Management Studio をダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)します。
> 
> 

アップグレード後、アプリケーションが希望のパフォーマンスで実行されていることを確認し、必要に応じて設定を最適化できるように、データベースを積極的に監視します。

個々のデータベースを監視するだけでなく、[Azure Portal](sql-database-elastic-pool-manage-portal.md) または [PowerShell](sql-database-elastic-pool-manage-powershell.md) を使用してエラスティック プールを監視することもできます。

**リソース消費データ:** Basic、Standard、Premium の各データベースの場合、ユーザー データベース内の [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV から、リソース消費データを利用できます。 この DMV は、直近 1 時間の操作に関する 15 秒おきのほぼリアル タイムのリソース消費情報を提供します。 1 つの間隔内の DTU 消費割合は、CPU、IO、ログにおける最大消費割合として算出されます。 次は、過去 1 時間における DTU の平均消費割合を算出するクエリです。

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

その他の監視情報

* [スタンドアロン データベースの場合の Azure SQL Database のパフォーマンス ガイダンス](http://msdn.microsoft.com/library/azure/dn369873.aspx)。
* [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)。
* [動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)

**アラート:** アップグレードしたデータベースの DTU 消費が特定の高レベルに近づいた場合に通知を表示するには、Azure ポータルで 'アラート' を設定します。 データベース アラートは、Azure ポータルで、DTU、CPU、IO、ログといったさまざまなパフォーマンス指標に対して設定できます。 データベースを参照し、**[設定]** ブレードで **[アラート ルール]** を選択します。

たとえば、DTU の平均割合の値が 5 分間にわたって 75% を超過したような場合に、「DTU 割合」に関する電子メール アラートを送るように設定できます。 アラート通知の構成方法については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [プールに関する推奨事項を確認し、プールを作成する](sql-database-elastic-pool-create-portal.md)。
* [データベースのサービス レベルとパフォーマンス レベルを変更する](sql-database-scale-up.md)。

## <a name="related-links"></a>関連リンク
* [SQL Database の機能](sql-database-features.md)
* [SQL Database V12 へのアップグレードの計画と準備](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png



<!--HONumber=Dec16_HO2-->


