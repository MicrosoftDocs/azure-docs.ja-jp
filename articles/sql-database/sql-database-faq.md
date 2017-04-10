---
title: "Azure SQL Database に関する FAQ | Microsoft Docs"
description: "クラウド データベースと Azure SQL Database、Microsoft のリレーショナル データベース管理システム (RDBMS)、およびクラウド内のサービスとしてのデータベースのよくある質問に対する回答です。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: ef58f595ff58b9156df813cf92e2306654a8b3ed
ms.lasthandoff: 03/15/2017


---
# <a name="sql-database-faq"></a>SQL Database に関する FAQ

## <a name="what-is-the-current-version-of-sql-database"></a>SQL Database の現在のバージョンは何ですか。
SQL Database の現在のバージョンは V12 です。 バージョン V11 は廃止されました。

## <a name="what-is-the-sla-for-sql-database"></a>SQL Database の SLA はどうなっていますか。
Microsoft は、お客様の Basic、Standard、または Premium エディションの Microsoft Azure SQL Database (シングルまたはエラスティック) と Microsoft のインターネット ゲートウェイの間で 99.99% の時間接続できることを保証します。 詳細については、[SLA](http://azure.microsoft.com/support/legal/sla/) のページを参照してください。

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>サーバー管理者のパスワードをリセットするにはどうすればよいですか。
[Azure Portal](https://portal.azure.com) で **[SQL Server]** をクリックし、一覧からサーバーを選択して、**[パスワードのリセット]** をクリックします。

## <a name="how-do-i-manage-databases-and-logins"></a>データベースとログインはどのように管理しますか。
[データベースとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>承認した IP アドレスにのみサーバーへのアクセスを許可するにはどうすればよいですか。
「 [方法: ファイアウォール設定を構成する (SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>SQL Database の使用量は請求書にどのように表示されますか。
SQL Database では、予測可能な&1; 時間単位のレートで、サービス階層と、Single Database の場合はパフォーマンス レベル、エラスティック プールの場合は eDTU の両方に基づいて請求が行われます。 実際の使用量は時間単位でコンピューティングされるため、表示される請求額は&1; 時間分に満たない場合があります。 たとえば、1 か月間にデータベースが存在したのが 12 時間だった場合、請求書には半日分の使用が示されます。 また、請求書にはサービス階層とパフォーマンス レベルおよびプールあたりの eDTU の内訳が記載されるため、それぞれが&1; か月間に発生したデータベース日数を簡単に把握できます。

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Single Database がアクティブであったのが&1; 時間に満たない場合や上位のサービス階層の使用が&1; 時間に満たない場合はどうなりますか。
使用状況やデータベースがアクティブであったのが&1; 時間未満であったことに関係なく、データベースが存在していた&1; 時間単位で、その時間に使用された最上位のサービス階層とパフォーマンス レベルで課金は行われます。 たとえば、Single Database を作成し、それを&5; 分後に削除した場合、請求書にはデータベース時間として&1; 時間の請求が表示されます。 

例

* Basic データベースを作成し、それをすぐに Standard S1 にアップグレードした場合、最初の&1; 時間分として Standard S1 のレートが課金されます。
* データベースを午後 10 時 00 分に Basic から Premium にアップグレードし始めて、 午前 1 時 35 分にアップグレードが完了した場合、 翌日の午前 1 時 00 分から Premium 料金での課金が始まります。 
* 午前 11 時 00 分に、データベースを Premium から Basic にダウングレードし始めて、 午後 2 時 15 分に完了した場合、Premium の料金が課金されるのは午後 3 時 00 分までで、それ以降は Basic の料金が課金されます。

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>エラスティック プールの使用量は請求書にどのように表示されますか。プールあたりの eDTU を変更した場合どうなりますか。
エラスティック プールの料金は、[価格ページ](https://azure.microsoft.com/pricing/details/sql-database/)のプールあたりの最大 eDTU 数の部分に記載の単位に従い、請求書にエラスティック DTU (eDTU) として表示されます。 エラスティック プールでは、データベース単位の請求はありません。 課金は、使用量やプールがアクティブであったのが&1; 時間に満たないということに関係なく、プールが存在していた時間の最上位の eDTU を使用して&1; 時間単位で行われます。 

例

* 午前 11 時 18 分に 200 eDTU の Standard エラスティック プールを作成し、プールに 5 つのデータベースを追加した場合、午前 11 時から始まって、その日中は 1 時間 200 eDTU が 課金されます。
* 2 日目には、データベース 1 が午前 5 時 5 分から 50 eDTU の消費を開始し、その日はそれが維持されます。 データベース 2 ～ 5 は、0 ～ 80 eDTU の間で変動します。 その日、その&1; 日間異なる eDTU を消費するデータベースが他に&5; つ追加されました。 2 日目は、丸 1 日 200 eDTU が課金されます。 
* 3 日目の午前 5 時に、 さらにデータベースが 15 追加されました。 1 日中データベースの使用量は増え、午後 8 時 5 分にプールの eDTU を 200 から 400 に増やすことに決めました。 午後 8 時まで 200 eDTU レベルで課金され、残りの 4 時間は 400 eDTU に増やされました。 

## <a name="elastic-pool-billing-and-pricing-information"></a>エラスティック プールの課金と価格情報
エラスティック プールは、次の特性ごとに課金されます。

* エラスティック プールは、プールにデータベースがない場合でも、その作成時に課金されます。
* エラスティック プールは&1; 時間ごとに課金されます。 これは、シングル データベースのパフォーマンス レベルと同じ使用状況測定の頻度です。
* エラスティック プールのサイズが変更され、eDTU の数が変わった場合には、サイズ変更の操作が完了するまでは新しい eDTU の量に応じた課金はされません。 これは、Single Database のパフォーマンス レベルを変更する場合と同様のパターンに従っています。
* エラスティック プールの価格は、プールの eDTU 数に基づきます。 エラスティック プールの価格は、内部のエラスティック データベースの数および使用率とは関係ありません。
* 価格は、(プールの eDTU 数) x (eDTU あたりの単価) で計算されます。

エラスティック プールの eDTU 単価は、同じサービス階層の Single Database の DTU 単価よりも高くなります。 詳細については、「 [SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 

eDtu およびサービス レベルについては、 [SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)に関するページをご覧ください。

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>エラスティック プールでアクティブ geo レプリケーションを使用した場合、請求書にはどのように表示されますか。
Elastic Database で [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md) を使用した場合、Single Database とは異なり、請求書には直接の影響はありません。  各プール (プライマリ プールおよびセカンダリ プール) にプロビジョニングされた eDTU に対してのみ課金されます。

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>監査機能を使用すると請求書にどのような影響がありますか。
監査機能は、追加料金なしで SQL Database サービスに組み込まれており、Basic、Standard、Premium データベースで利用可能です。 ただし、監査ログを保存する場合、監査機能は Azure Storage アカウントを使用するので、監査ログのサイズに応じて Azure Storage のテーブルとキューの料金が課されます。

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Single Database とエラスティック プールに適したサービス階層とパフォーマンス レベルはどのように調べることができますか。
使用できるツールが少数あります。 

* オンプレミスのデータベースの場合、必要なデータベースと DTU の推奨を行い、エラスティック プール用の複数のデータベースを評価する、[DTU サイズ設定サポート ツール](http://dtucalculator.azurewebsites.net/)を使用できます。
* 使用履歴からプールの Single Database に利点があると判断された場合、Azure インテリジェント エンジンはエラスティック プールを推奨します。 「[Azure Portal を使用したエラスティック プールの監視と管理](sql-database-elastic-pool-manage-portal.md)」を参照してください。 自分で計算する方法については、[エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)を参照してください。
* Single Database の調整については、「 [Single Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md)」を参照してください。

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Single Database のサービス階層またはパフォーマンス レベルは何回変更できますか。
V12 データベースでは、サービス階層 (Basic、Standard、および Premium) または (S1 から S2 などの) サービス階層のパフォーマンス レベルは、必要な回数だけ変更できます。 以前のバージョンのデータベースでは、サービス階層またはパフォーマンス レベルは、24 時間で合計 4 回変更できます。

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>各プールの eDTU はどのくらいの頻度で調整できますか。
頻度に制限はありません。必要なだけ調整できます。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Single Database のサービス階層またはパフォーマンス レベルの変更や、エラスティック プールからのデータベースの出し入れには、どれくらいの時間がかかりますか。
データベースのサービス階層の変更やそれのプールからの出し入れでは、バック グラウンド操作でプラットフォームにデータベースをコピーする必要があります。 サービス階層の変更は、データベースのサイズに応じて数分から数時間かかることがあります。 いずれの場合も、データベースはオンラインを維持し、移動中も使用できます。 Single Database の変更の詳細については、「 [データベースのサービス階層の変更](sql-database-service-tiers.md)」を参照してください。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Single Database と Elastic Database はどのように使い分けできますか。
一般に、エラスティック プールは、典型的な[サービスとしてのソフトウェア (SaaS) アプリケーションのパターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)用に設計されており、データベースは顧客またはテナントごとに&1; つです。 個々のデータベースを購入し、さまざまな、またはピーク時の需要に合わせてオーバープロビジョニングすることはコスト効率がよくありません。 プールを使用すると、プールでパフォーマンスは全体的に管理され、データベースは自動的にスケールアップおよびダウンされます。 使用パターンから利益があると判断される場合、Azure インテリジェント エンジンはデータベースにプールを推奨します。 詳しくは、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」をご覧ください。

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>バックアップ ストレージとして、プロビジョニングされている最大のデータベース ストレージの 200% までが提供されるというのはどういう意味ですか。
バックアップ ストレージは、[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)と [geo リストア](sql-database-recovery-using-backups.md#geo-restore)に使用される自動化されたデータベース バックアップに関連付けられたストレージです。 Microsoft Azure SQL Database は、バックアップ ストレージとして、プロビジョニングされている最大のデータベース ストレージの 200% までを追加のコストなしで提供します。 たとえば、プロビジョニングされたデータベース サイズが 250 GB の Standard データベース インスタンスの場合、500 GB のバックアップ ストレージが追加のコストなしで提供されます。 提供されるバックアップ ストレージをデータベースが超過している場合は、Azure サポートに連絡して保有期間を短縮するか、または読み取りアクセスのジオ (主要地域) 冗長ストレージ (RA-GRS) の標準料金をお支払いいただいてバックアップ ストレージを追加することができます。 RA-GRS の請求の詳細については、「Storage の料金詳細」を参照してください。

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Web または Business を新しいサービス階層に移行する際に何を理解しておけばよいでしょうか。
Azure SQL Web データベースおよび Business データベースは提供終了になりました。 Basic、Standard、Premium、および Elastic 階層は、提供終了になった Web データベースおよび Business データベースの代わりです。 別途、この移行期間に役立つ FAQ を用意しています。 [Web および Business Edition の終了に関する FAQ](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>同じ Azure 地理的条件内で&2; つのリージョン間のデータベースを geo レプリケートする場合に予想されるレプリケーションの遅延はどのくらいですか。
現在、5 秒間の RPO をサポートしているため、geo セカンダリが Azure 推奨のペアになっているリージョンでホストされ、同じサービス レベルである限り、レプリケーションの遅延はそれより短くなっています。

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>geo セカンダリをプライマリ データベースと同じリージョンに作成する場合に予想されるレプリケーションの遅延はどのくらいですか。
実験データによれば、Azure 推奨のペアになっているリージョンが使用されている場合は、リージョン内およびリージョン間のレプリケーションの遅延に大きな差はありません。 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>geo レプリケーションが設定されている場合、2 つのリージョン間にネットワーク エラーが発生すると、再試行ロジックはどのように動作しますか。
接続が切断されると、接続を再確立するために 10 秒ごとに再試行します。

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>プライマリ データベースでの重要な変更がレプリケートされていることを保証するには、どうすればよいでしょうか。
geo セカンダリは非同期レプリカであり、プライマリとの完全な同期を維持しようとはしていません。 ただし、重要な変更 (パスワードの更新など) を確実にレプリケートするために、強制同期を行う方法が用意されています。 強制同期を実行すると、コミットされたトランザクションがすべてレプリケートされるまで呼び出しスレッドがブロックされるため、パフォーマンスに影響します。 詳細については、[sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) に関するページをご覧ください。 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>プライマリ データベースと geo セカンダリの間のレプリケーションの遅延を監視するために、どのツールを使用できますか。
DMV を使ってプライマリ データベースと geo セカンダリの間のリアルタイムのレプリケーションの遅延を公開しています。 詳細については、[sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx) に関するページをご覧ください。

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>同じサブスクリプション内の別のサーバーにデータベースを移動するには
* [Azure Portal](https://portal.azure.com) で **[SQL データベース]** をクリックし、一覧からデータベースを選択してから、**[コピー]** をクリックします。 詳細については、「 [Azure SQL データベースのコピー](sql-database-copy.md) 」を参照してください。

## <a name="to-move-a-database-between-subscriptions"></a>サブスクリプション間でデータベースを移動するには
* [Azure Portal](https://portal.azure.com) で **[SQL Server]** をクリックし、一覧からデータベースをホストするサーバーを選択します。 **[移動]**をクリックし、移動するリソースと移動先のサブスクリプションを選択します。

