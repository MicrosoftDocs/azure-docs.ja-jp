---
title: Azure SQL Database の Geo レプリケーションを使用した SaaS アプリのディザスター リカバリー | Microsoft Docs
description: 障害が発生した場合に Azure SQL Database の geo レプリカを使用して、マルチテナント SaaS アプリを復旧する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ee10f3d1b9db79eff199581a67c40196315b73f6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872047"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>データベースの geo レプリケーションを使用したマルチテナント SaaS アプリケーションのディザスター リカバリー

このチュートリアルでは、テナント単位データベース モデルを使用して実装されているマルチテナント SaaS アプリケーションの完全なディザスター リカバリー シナリオを見ていきます。 障害からアプリを保護するには、[_geo レプリケーション_](sql-database-geo-replication-overview.md) を使用して、代替復旧リージョンにカタログ データベースとテナント データベースのレプリカを作成します。 障害が発生した場合は、迅速にレプリカにフェールオーバーして、通常の業務を再開します。 フェールオーバー時には、元のリージョンのデータベースが、復旧リージョン内のデータベースのセカンダリ レプリカになります。 これらのレプリカがオンラインに戻ると、復旧リージョン内のデータベースの状態に自動的に追いつきます。 障害が解決した後は、元の運用リージョン内のデータベースにフェールバックします。

このチュートリアルでは、フェールオーバーとフェールバック両方のワークフローについて説明します。 学習内容は次のとおりです。
> [!div class="checklist"]
> 
> * データベースとエラスティック プールの構成情報をテナントのカタログに同期する
> * アプリケーション、サーバー、プールで構成される復旧環境を代替リージョンにセットアップする
> * "_geo レプリケーション_" を使用してカタログ データベースとテナント データベースを復旧リージョンにレプリケートする
> * アプリケーションおよびカタログ データベースとテナント データベースを復旧リージョンにフェールオーバーする 
> * 障害が解決した後、アプリケーション、カタログ データベース、テナント データベースを元のリージョンにフェールバックする
> * 各テナントのデータベースのプライマリの場所を追跡するため、各テナント データベースがフェールオーバーしたらカタログを更新する
> * 待機時間を減らすため、アプリケーションとプライマリ テナント データベースが常に同じ Azure リージョンに併置されるようにする  
 

このチュートリアルを始める前に、次の前提条件を満たしておく必要があります。
* Wingtip Tickets SaaS テナント単位データベース アプリをデプロイする。 5 分未満でデプロイするには、[テナントごとのデータベースを使用する Wingtip Tickets SaaS アプリケーションのデプロイと探索](saas-dbpertenant-get-started-deploy.md)に関する記事を参照してください。  
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>geo レプリケーション復旧パターンの概要

![復旧のアーキテクチャ](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
ディザスター リカバリー (DR) は、コンプライアンス上の理由またはビジネス継続性のため、多くのアプリケーションにとって重要な考慮事項です。 長時間にわたるサービス障害が発生しても、適切に準備された DR 計画があればビジネスの中断を最小限にできます。 geo レプリケーションを使うと、短い告知期間でフェールオーバーできる復旧リージョンにデータベース レプリカを保持することにより、最も低い RPO と RTO が提供されます。

geo レプリケーションに基づく DR プランは、3 つの部分から構成されます。
* セットアップ - 復旧環境の作成とメンテナンス
* 復旧 - 障害が発生した場合の、復旧環境へのアプリとデータベースのフェールオーバー
* 復帰 - アプリケーションが解決された後の、元のリージョンへのアプリとデータベースのフェールバック 

すべての部分を慎重に検討する必要があり、規模が大きい場合は特に重要です。 全体として、計画では複数の目標を達成する必要があります。

* セットアップ
    * 復旧リージョン内にミラー イメージ環境を確立して維持します。 この復旧環境にエラスティック プールを作成し、任意のデータベースをレプリケートして、復旧リージョンに容量を確保します。 この環境の維持には、プロビジョニングされた新しいテナント データベースのレプリケートが含まれます。  
* 復旧
    * スケールダウンされた復旧環境を使用して日常的なコストを最小にする場合は、プールとデータベースをスケールアップして復旧リージョンに完全な運用容量を取得する必要があります
    * できるだけ早く復旧リージョンで新しいテナントのプロビジョニングを有効にします  
    * 優先順でテナントが復元されるように最適化します
    * 可能であれば、並列で手順を実行して、できる限り速くテナントがオンラインになるように最適化します
    * 障害に対する回復力があり、再起動可能かつべき等であるようにします
    * 元のリージョンがオンラインに戻った場合に、プロセスの途中でキャンセルできるようにします
* 復帰 
    * テナントに影響を最小限にして復旧リージョンから元のリージョンのレプリカにデータベースをフェールオーバーします。データの損失がなく、テナントごとのオフライン期間が最低限になるようにします。   

このチュートリアルでは、Azure SQL Database と Azure プラットフォームの以下の機能を使って、これらの課題に対応します。

* [Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)は、できるだけ早くすべての必要な容量を確保します。 復旧リージョン内に運用サーバーとエラスティック プールのミラー イメージをプロビジョニングするには、Azure Resource Manager テンプレートを使います。
* [geo レプリケーション](sql-database-geo-replication-overview.md)は、すべてのデータベースのレプリケートされた読み取り専用のセカンダリを非同期的に作成します。 障害時には、復旧リージョンのレプリカにフェールオーバーします。  障害が解決した後で、データを失うことなく元のリージョン内のデータベースにフェールバックします。
* テナントの優先順に送信される[非同期](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)フェールオーバー操作は、大量のデータベースのフェールオーバー時間を最小限に抑えます。
* [シャード管理復旧機能](sql-database-elastic-database-recovery-manager.md)は、復旧および復帰の間にカタログのデータベース エントリを変更します。 これらの機能により、アプリは、再構成しなくても、場所に関係なく、テナント データベースに接続できます。
* [SQL サーバーの DNS エイリアス](dns-alias-overview.md)は、アプリが動作しているリージョンに関係なく、新しいテナントのシームレスなプロビジョニングを可能にします。 また、DNS エイリアスは、カタログの場所に関係なく、カタログ同期プロセスがアクティブなカタログに接続できるようにするためにも使われます。

## <a name="get-the-disaster-recovery-scripts"></a>ディザスター リカバリー スクリプトを取得する 

> [!IMPORTANT]
> すべての Wingtip Tickets 管理スクリプトと同様に、DR スクリプトはサンプル品質なので、運用環境では使わないでください。 

このチュートリアルで使われる復旧スクリプトと、Wingtip アプリケーションのソース コードは、[Wingtip Tickets SaaS のテナントごとのデータベースの GitHub リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)で入手できます。 Wingtip Tickets 管理スクリプトをダウンロードしてブロック解除する手順に関する[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)を参照してください。

## <a name="tutorial-overview"></a>チュートリアルの概要
このチュートリアルでは最初に、geo レプリケーションを使用して、異なるリージョンに Wingtip Tickets アプリケーションとそのデータベースのレプリカを作成します。 次に、このリージョンにフェールオーバーして、障害からの復旧をシミュレートします。 完了すると、アプリケーションは復旧リージョンで完全に機能するようになります。

その後、別の復帰手順において、復旧リージョンのカタログとテナント データベースを元のリージョンにフェールオーバーします。 復帰の期間中、アプリケーションとデータベースは使用可能な状態のままになります。 完了すると、アプリケーションは元のリージョンで完全に機能するようになります。

> [!Note]
> アプリケーションは、アプリケーションが展開されたリージョンの "_ペア リージョン_" に復旧されます。 詳細については、「[Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」をご覧ください。

## <a name="review-the-healthy-state-of-the-application"></a>アプリケーションの正常性状態を確認する

復旧プロセスを始める前に、アプリケーションの通常の正常な状態を確認します。
1. Web ブラウザーで、Wingtip Tickets イベント ハブ (http://events.wingtip-dpt.&lt ;user&gt;.trafficmanager.net - &lt; user&gt; は実際の展開でのユーザーの値に置き換えます) を開きます。
    * ページの下部までスクロールし、フッターでカタログ サーバー名と場所を確認します。 場所は、アプリを展開したリージョンです。
    *ヒント:場所をマウスでポイントすると表示が拡大されます。* 
    ![元のリージョンでのイベント ハブの正常な状態](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Contoso Concert Hall テナントをクリックして、そのイベント ページを開きます。
    * フッターで、テナントのサーバー名を確認します。 場所は、カタログ サーバーの場所と同じです。

3. [Azure Portal](https://portal.azure.com) で、アプリが展開されているリソース グループを開きます
    * サーバーが展開されているリージョンを確認します。 

## <a name="sync-tenant-configuration-into-catalog"></a>テナントの構成をカタログに同期する

このタスクでは、サーバー、エラスティック プール、およびデータベースの構成をテナント カタログに同期するプロセスを開始します。 このプロセスは、カタログ内のこの情報を最新の状態に維持します。  このプロセスは、元のリージョンでも復旧リージョンでも、アクティブなカタログで動作します。 構成情報は、復旧プロセスの一部として復旧環境が元の環境と一致していることを確認するために、また後の復帰プロセスでは元のリージョンが復旧環境で行われた変更と整合していることを確認するために、使用されます。 カタログは、テナント リソースの復旧状態の追跡にも使われます。

> [!IMPORTANT]
> わかりやすくするため、これらのチュートリアルでは、同期プロセスおよびその他の実行時間の長い復旧プロセスと復帰プロセスは、ご使用のクライアントのユーザー ログインで実行されるローカル PowerShell ジョブまたはセッションとして実装されています。 ログイン時に発行される認証トークンは、数時間で有効期限が切れ、ジョブは失敗するようになります。 運用のシナリオでは、実行時間の長いプロセスは、サービス プリンシパルで実行される、何らかの信頼性の高い Azure サービスとしてを実装する必要があります。 「[Azure PowerShell を使用して資格情報でのサービス プリンシパルを作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)」をご覧ください。

1. _PowerShell ISE_ で、...\Learning Modules\UserConfig.psm1 ファイルを開きます。 10 行目と 11 行目の `<resourcegroup>` および `<user>` は、アプリを展開したときに使った値に置き換えます。  ファイルを保存します。

2. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開き、次のように設定します。
    * **$DemoScenario = 1**: テナント サーバーとプールの構成をカタログに同期するバックグラウンド ジョブを開始します

3. **F5** キーを押して、同期スクリプトを実行します。 新しい PowerShell セッションが開き、テナント リソースの構成が同期されます。
![同期プロセス](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

PowerShell ウィンドウはバックグラウンドで実行させたままにして、チュートリアルの残りの部分を続けます。 

> [!Note]
> 同期プロセスは、DNS エイリアスを使ってカタログに接続します。 このエイリアスは、復元および復帰の間に、アクティブなカタログを指すように変更されます。 同期プロセスは、復旧リージョンで行われたデータベースまたはプールの構成の変更を使って、カタログを最新の状態に維持します。  復帰では、これらの変更が元のリージョンの同等のリソースに適用されます。

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>復旧リージョンにセカンダリ データベース レプリカを作成する

このタスクでは、重複するアプリ インスタンスを展開してカタログとすべてのテナント データベースを復旧リージョンにレプリケートするプロセスを開始します。

> [!Note]
> このチュートリアルでは、geo レプリケーションの保護を Wingtip Tickets サンプル アプリケーションに追加します。 geo レプリケーションを使うアプリケーションの運用シナリオでは、各テナントが geo レプリケートされたデータベースで最初からプロビジョニングされます。 「[Azure SQL Database を使用した高可用性サービスの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)」をご覧ください。

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開き、次の値を設定します。
    * **$DemoScenario = 2**: ミラー イメージ復旧環境を作成し、カタログ データベースとテナント データベースをレプリケートします

2. **F5** キーを押して、スクリプトを実行します。 レプリカを作成する新しい PowerShell セッションが開かれます。
![同期プロセス](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>通常のアプリケーションの状態を確認する

この時点で、アプリケーションは元のリージョンで正常に実行され、geo レプリケーションによって保護されています。  すべてのデータベースに対し、読み取り専用のセカンダリ レプリカが復旧リージョンに存在します。 

1. Azure Portal でリソース グループを表示すると、-recovery というサフィックスが付いたリソース グループが復旧リージョンに作成されていることがわかります。 

2. 復旧リソース グループでリソースを調べます。  

3. _tenants1-dpt-&lt;ユーザー&gt;-recovery_ サーバーで、Contoso Concert Hall データベースをクリックします。  左側で [geo レプリケーション] をクリックします。 

    ![Contoso Concert の geo レプリケーション リンク](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Azure リージョンのマップで、元のリージョンのプライマリと、復旧リージョンのセカンダリの間の geo レプリケーション リンクに注意してください。  

## <a name="fail-over-the-application-into-the-recovery-region"></a>アプリケーションを復旧リージョンにフェールオーバーする

### <a name="geo-replication-recovery-process-overview"></a>geo レプリケーション復旧プロセスの概要

復旧スクリプトでは、次のタスクが実行されます。

1. 元のリージョンで Web アプリの Traffic Manager エンドポイントを無効にします。 エンドポイントを無効にすることで、復旧の間に元のリージョンがオンラインになったときに、ユーザーが無効な状態のアプリに接続するのを防ぎます。

1. 復旧リージョンのカタログ データベースの強制フェールオーバーを使って、それをプライマリ データベースにし、復旧カタログ サーバーを指すように別名 _activecatalog_ を更新します。

1. 復旧リージョンのテナント サーバーを指すように、別名 _newtenant_ を更新します。 この別名を変更することで、新しいテナントのデータベースが復旧リージョンでプロビジョニングされるようにします。 

1. フェールオーバーする前に、復旧カタログの既存のすべてのテナントをオフラインとマークして、テナント データベースにアクセスできないようにします。

1. 復旧リージョンのすべてのエラスティック プールとレプリケートされた単一データベースの構成を更新し、元のリージョンでの構成をミラーリングします。 (このタスクは、復旧環境のプールまたはレプリケートされたデータベースが、コスト削減のために通常運用中にスケールダウンされている場合にのみ必要です)。

1. 復旧リージョンで Web アプリの Traffic Manager エンドポイントを有効にします。 このエンドポイントを有効にすると、アプリケーションは新しいテナントをプロビジョニングできるようになります。 この段階では、既存のテナントはまだオフラインです。

1. 要求のバッチを送信し、優先順位に従ってデータベースを強制的にフェールオーバーします。
    * バッチは、データベースがすべてのプールに並列にフェールオーバーされるように編成されています。
    * フェールオーバー要求は非同期操作を使用して送信されるので、迅速に送信され、複数の要求を同時に処理できます。

   > [!Note]
   > 障害のシナリオでは、元のリージョンのプライマリ データベースはオフラインになっています。  セカンダリで強制フェールオーバーを実行すると、プライマリへの接続は切断され、キューに残っているトランザクションの適用は試みられません。 このチュートリアルのような DR ドリル シナリオでは、フェールオーバー時に更新アクティビティが存在していると、データ損失の可能性があります。 後で、復帰の間に、復旧リージョンのデータベースを元のリージョンにフェールバックするときは、データの損失がないように通常のフェールオーバーが使用されます。

1. SQL データベース サービスを監視して、データベースがフェールオーバーされたことを確認します。 フェールオーバーされたテナント データベースは、カタログを更新して、テナント データベースの復旧状態を記録し、テナントをオンラインとしてマークします。
    * テナントがカタログでオンラインとしてマークされるとすぐに、アプリケーションはテナント データベースにアクセスできるようになります。
    * テナント データベースでの rowversion 値の合計が、カタログに格納されます。 この値はフィンガープリントとして機能し、復帰プロセスはこの値を使うことにより、データベースが復旧リージョンで更新されているかどうかを判断できます。

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>スクリプトを実行して復旧リージョンにフェールオーバーする

それでは、アプリケーションが展開されているリージョンで障害が発生したものと想定して、復旧スクリプトを実行します。

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開き、次の値を設定します。
    * **$DemoScenario = 3**: レプリカにフェールオーバーして、アプリを復旧リージョンに復旧します

2. **F5** キーを押して、スクリプトを実行します。  
    * 新しい PowerShell ウィンドウでスクリプトが開き、並列に実行される一連の PowerShell ジョブが開始されます。 これらのジョブは、テナント データベースを復旧リージョンにフェールオーバーします。
    * 復旧リージョンは、アプリケーションを展開した Azure リージョンに関連付けられている "_ペア リージョン_" です。 詳細については、「[Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」をご覧ください。 

3. PowerShell ウィンドウで復旧プロセスの状態を監視します。
    ![フェールオーバー プロセス](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> 復旧ジョブのコードを調べるには、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs フォルダーにある PowerShell スクリプトをご覧ください。

### <a name="review-the-application-state-during-recovery"></a>復旧中にアプリケーションの状態を確認する

Traffic Manager でアプリケーション エンドポイントが無効になっている間は、アプリケーションを使用できません。 カタログが復旧リージョンにフェールオーバーされて、すべてのテナントがオフラインとしてマークされると、アプリケーションはオンラインに戻されます。 アプリケーションは使用可能ですが、データベースがフェールオーバーされるまでは、イベント ハブ内で各テナントはオフラインと表示されます。 オフラインのテナント データベースを処理するようにアプリケーションを設計することが重要です。

1. カタログ データベースが復旧された後すぐ、Web ブラウザーで Wingtip Tickets イベント ハブを更新します。
   * フッターで、カタログ サーバー名に _-recovery_ というサフィックスが付いていて、復旧リージョンに存在することを確認します。
   * まだ復元されていないテナントはオフラインとしてマークされていて、選択できないことを確認します。  

     > [!Note]
     > 復旧するデータベースの数が少ないときは、復旧が完了する前にブラウザーを更新できないことがあるので、オフライン状態のテナントを確認できない場合があります。 
 
     ![オフラインのイベント ハブ](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * オフラインのテナントの [イベント] ページを直接開いた場合は、"テナントはオフライン" であることを示す通知が表示されます。 たとえば、Contoso Concert Hall がオフラインのときに、 http://events.wingtip-dpt.&lt ;ユーザー&gt;.trafficmanager.net/contosoconcerthall を開いてみます。![ Contoso オフライン ページ](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>復旧リージョン内に新しいテナントをプロビジョニングする
既存のすべてのテナント データベースがフェールオーバーされる前であっても、復旧リージョンで新しいテナントをプロビジョニングできます。  

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開き、次のプロパティを設定します。
    * **$DemoScenario = 4**: 復旧リージョンで新しいテナントをプロビジョニングします

2. **F5** キーを押してスクリプトを実行し、新しいテナントをプロビジョニングします。 

3. 完了すると、Hawthorn Hall のイベント ページがブラウザーで開きます。 Hawthorn Hall データベースが復旧リージョンにプロビジョニングされたことをフッターで確認します。
    ![[Hawthorn Hall イベント] ページ](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. ブラウザーで、Wingtip Tickets イベント ハブ ページを更新して、Hawthorn ホールが含まれることを確認します。 
    * 他のテナントの復元を待たずに Hawthorn Hall をプロビジョニングした場合、他のテナントはまだオフラインになっている可能性があります。


## <a name="review-the-recovered-state-of-the-application"></a>アプリケーションの復旧された状態を確認する

復旧プロセスが完了すると、アプリケーションとすべてのテナントは、復旧リージョンで完全に機能するようになります。 

1. すべてのテナントが復旧されたことが PowerShell コンソール ウィンドウの表示で示されたら、イベント ハブを更新します。  新しいテナント Hawthorn Hall も含めて、すべてのテナントがオンラインと表示されます。

    ![イベント ハブの復旧されたテナントと新しいテナント](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. [Azure Portal](https://portal.azure.com) で、リソース グループの一覧を開きます。  
    * 展開したリソース グループに加えて、 _-recovery_ というサフィックスが付いた復旧リソース グループが表示されることを確認します。  復旧リソース グループには、復旧プロセスの間に作成されたすべてのリソースと、停止中に作成された新しいリソースが含まれます。  

3. 復旧リソース グループを開き、次の項目を確認します。
   * サフィックス _-recovery_ が付いた、catalog サーバーと tenants1 サーバーの復旧バージョン。  これらのサーバー上の復元されたカタログ データベースとテナント データベースはすべて、元のリージョンで使われていた名前のままです。

   * _tenants2-dpt-&lt;ユーザー&gt;-recovery_ SQL サーバー。  このサーバーは、停止中の新しいテナントのプロビジョニングに使われます。
   * _events-wingtip-dpt-&lt;復旧リージョン&gt;-&lt;ユーザー>_ という名前の App Service。これは、Events アプリの復旧インスタンスです。 

     ![Azure 復旧リソース](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. _tenants2-dpt-&lt;ユーザー&gt;-recovery_ SQL サーバーを開きます。  データベース _hawthornhall_ とエラスティック プール _Pool1_ が含まれることを確認します。  _hawthornhall_ データベースは、_Pool1_ エラスティック プール内のエラスティック データベースとして構成されています。

5. リソース グループに戻り、_tenants1-dpt-&lt;ユーザー&gt;-recovery_ サーバーで、Contoso Concert Hall データベースをクリックします。 左側で [geo レプリケーション] をクリックします。
    
    ![フェールオーバー後の Contoso データベース](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>テナントのデータを変更する 
このタスクでは、テナント データベースの 1 つを更新します。 

1. ブラウザーで、Contoso Concert Hall のイベント一覧を探し、最後のイベントの名前を書き留めます。
2. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開き、次の値を設定します。
    * **$DemoScenario = 5**: 復旧リージョン内のテナントからイベントを削除します
3. **F5** キーを押してスクリプトを実行します
4. Contoso Concert Hall のイベント ページ (http://events.wingtip-dpt.&lt ;ユーザー&gt;.trafficmanager.net/contosoconcerthall - &lt; ユーザー&gt; は実際の展開のユーザーの値に置き換えます) を更新し、最後のイベントが削除されていることを確認します。

## <a name="repatriate-the-application-to-its-original-production-region"></a>アプリケーションを元の運用リージョンに復帰する

このタスクでは、アプリケーションをその元のリージョンに復帰します。 実際のシナリオでは、障害が解決されたら復帰を開始します。

### <a name="repatriation-process-overview"></a>復帰プロセスの概要

![復帰のアーキテクチャ](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

復帰プロセス:
1. 未処理または実行中のデータベース復元要求を取り消します。
2. 元のリージョンのテナント サーバーを指すように、別名 _newtenant_ を更新します。 この別名を変更することで、新しいテナントのデータベースが元のリージョンでプロビジョニングされるようにします。
3. 変更されたテナント データを元のリージョンにシードします
4. 優先順位に従ってテナント データベースをフェールオーバーします。

フェールオーバーにより、データベースは実質的に元のリージョンに移動します。 データベースがフェールオーバーするとき、開いている接続はすべて削除され、データベースは何秒間か利用できなくなります。 アプリケーションでは、再び接続するように再試行ロジックを作成しておく必要があります。  通常、この短い切断が認識されることはありませんが、データベースの復帰は勤務時間外に行うのがよいかもしれません。 


### <a name="run-the-repatriation-script"></a>復帰スクリプトを実行する
障害が解決したものとして、復帰スクリプトを実行します。

1. *PowerShell ISE* で、...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 スクリプトを開きます。

2. PowerShell インスタンスでカタログ同期プロセスがまだ実行していることを確認します。  必要な場合は、次のように設定して再起動します。
    * **$DemoScenario = 1**: テナント サーバー、プール、およびデータベースの構成情報のカタログへの同期を開始します
    * **F5** キーを押して、スクリプトを実行します。

3.  復帰プロセスを開始するには、次のように設定します。
    * **$DemoScenario = 6**: アプリを元のリージョンに復帰します
    * **F5** キーを押して、新しい PowerShell ウィンドウで復旧スクリプトを実行します。  復帰には数分かかり、PowerShell ウィンドウで監視できます。
    ![復帰プロセス](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. スクリプトが実行されている間に、イベント ハブのページ (http://events.wingtip-dpt.&lt ;ユーザー&gt;.trafficmanager.net) を更新します。
    * すべてのテナントがオンラインであり、このプロセス全体を通じてアクセスできることを確認します。

5. 復帰が完了した後、イベント ハブを更新し、Hawthorn Hall のイベント ページを開きます。 このデータベースが元のリージョンに復帰していることを確認します。
    ![復帰したイベント ハブ](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>アプリとデータベースが併置されるようにアプリケーションを設計する 
このアプリケーションは、テナント データベースと同じリージョン内のインスタンスから常に接続するように設計されています。 このように設計すると、アプリケーションとデータベースの間の待機時間が減少します。 この最適化では、データベースとアプリの間の対話の方が、ユーザーとアプリの間の対話より頻繁に行われるものと想定されています。  

復帰の過程で、しばらくの間、テナント データベースが復旧リージョンと元のリージョンに分散することがあります。 データベースごとに、アプリはテナント サーバーの名前で DNS 参照を行って、データベースが存在するリージョンを調べます。 SQL Database では、サーバー名は別名です。 別名のサーバー名には、リージョンの名前が含まれています。 アプリケーションとデータベースのリージョンが異なる場合、アプリケーションはデータベース サーバーと同じリージョン内のインスタンスにリダイレクトします。  データベースと同じリージョン内のインスタンスにリダイレクトすることで、アプリとデータベース間の待機時間を最小限に抑えられます。 

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> 
> * データベースとエラスティック プールの構成情報をテナントのカタログに同期する
> * アプリケーション、サーバー、プールで構成される復旧環境を代替リージョンにセットアップする
> * "_geo レプリケーション_" を使用してカタログ データベースとテナント データベースを復旧リージョンにレプリケートする
> * アプリケーションおよびカタログ データベースとテナント データベースを復旧リージョンにフェールオーバーする 
> * 障害が解決した後、アプリケーション、カタログ データベース、テナント データベースを元のリージョンにフェールバックする

Azure SQL データベースで提供されているビジネス継続性を有効にするためのテクノロジについては、「[ビジネス継続性の概要](sql-database-business-continuity.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
