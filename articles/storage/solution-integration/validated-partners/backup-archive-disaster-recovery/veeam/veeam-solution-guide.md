---
title: Veeam を使用して Azure にデータをバックアップする
titleSuffix: Azure Blob Storage Docs
description: Web ページには、検討すべき要素の概要と、Veeam Backup and Recovery のストレージ ターゲットおよび回復場所として Azure を利用するために従う手順が示されています
keywords: Veeam, クラウドへのバックアップ, バックアップ, Azure へのバックアップ, ディザスター リカバリー, ビジネス継続性
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 6d4b005a3f9c79ff14f5ba4053dc651c1ede56e0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124433"
---
# <a name="backup-to-azure-with-veeam"></a>Veeam を使用した Azure へのバックアップ

この記事では、Veeam インフラストラクチャと Azure Blob Storage を統合するためのガイドを提供します。 これには前提条件、Azure Storage の原則、実装、および運用に関するガイダンスが含まれます。 この記事では、プライマリ サイト内での通常の運用を妨げる障害発生時に、Azure をオフサイト バックアップ ターゲットおよび復元サイトとして使用する方法についてのみ説明します。 また、Veeam により、障害が発生した場合により迅速に起動して復旧するようにスタンバイ VM を準備する手段として、RTO がより短いソリューションである Veeam Replication が提供され、Azure 運用環境内のリソースが保護されます。 Veeam には、Azure および Office 365 リソースをバックアップするための専用ツールも用意されています。 このドキュメントではこれらの機能については説明しません。 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>オンプレミスから Azure および Azure 内のデプロイに関する参照アーキテクチャ

![Veeam から Azure の参照アーキテクチャ](../media/veeam-architecture.png)

既存の Veeam デプロイは、クラウド バックアップ リポジトリとして Azure Storage アカウントまたは複数のアカウントを追加することで、Azure と簡単に統合できます。 また、Veeam では、オンプレミスから Azure 内にバックアップを回復することもできます。これにより、Azure のオンデマンド復旧サイトが提供されます。

## <a name="veeam-interoperability-matrix"></a>Veeam の相互運用性マトリックス
| ワークロード | GPv2 と Blob Storage | クール層のサポート | アーカイブ層のサポート | Data Box ファミリのサポート |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| オンプレミスの VM またはデータ | v10a | v10a | NA | 10a* |
| Azure VM | v10a | v10a | NA | 10a* |
| Azure BLOB | v10a | v10a | NA | 10a* |
| Azure Files | v10a | v10a | NA | 10a* | 

> [!Note]
Veeam Backup and Replication では、Azure Data Box に対してのみ REST API がサポートされるため、Azure Data Box Disk はサポートされません。 Azure Blob Storage のアーカイブ層のサポートは、Veeam v11 で予定されています。

## <a name="before-you-begin"></a>開始する前に

少し前に計画を立てることで、Azure をオフサイト バックアップ ターゲットおよび復旧サイトとして使用して非常に満足しているお客様の仲間入りをすることができます。

### <a name="are-you-new-to-azure"></a>Azure をご利用になるのは初めてですか?

Microsoft では、Azure の使用を開始する際に従うフレームワークを提供しています。 [クラウド導入フレームワーク](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \(CAF\) は、エンタープライズ デジタル変革に向けた詳細なアプローチであり、運用レベルのクラウド導入を計画するための包括的なガイドです。 CAF には、Azure を初めてご利用になる方が迅速かつ安全に運用するのに役立つステップバイステップの [Azure セットアップ ガイド](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/)が含まれています。[Azure portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) では対話型バージョンを見つけることができます。 アプリケーションをデプロイするためのサンプル アーキテクチャおよび具体的なベスト プラクティスと、Azure の専門知識を活用できる無料のトレーニング リソースが記載されています。

### <a name="consider-the-network-between-your-location-and-azure"></a>お客様の場所と Azure の間のネットワークを検討する

クラウド リソースを運用、テストおよび開発を行うために活用する場合でも、バックアップ ターゲットおよび復旧サイトとして利用する場合でも、初期バックアップのシード処理と、毎日の継続的な転送における帯域幅のニーズを理解することが重要です。

ベースライン転送に許容できる時間より長くかかると予測される場合、追加の帯域幅を必要とすることなく、初期バックアップ ベースラインを Azure に転送する手段が Azure Data Box によって提供されます。 ストレージ アカウントを作成するときにデータ転送予測ツールを利用して、初期バックアップの転送に必要な時間を予測することができます。

![Azure Storage データ転送予測ツール](../media/az-storage-transfer.png)

運用アプリケーションに影響を与えることなく、必要な転送時間 (バックアップ時間) 内での毎日のデータ転送をサポートするには、十分なネットワーク容量が必要になることに注意してください。 このセクションでは、ネットワークのニーズを評価するために使用できるツールと手法について概説します。

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>必要となる帯域幅の量を決定するにはどうすればよいですか?

Azure への初期ベースライン転送の変更率とバックアップ セットの合計サイズを決定するために、複数の評価オプションを使用できます。 以下に評価およびレポート ツールの例をいくつか示します。
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>現在のインターネット接続でどの程度のヘッドルームがあるかを把握するにはどうすればよいですか?

日常的に利用可能なヘッドルーム (つまり、通常は未使用の) 帯域幅がどれだけあるかを把握しておくことが重要です。 これにより、最初のアップロード時、オフラインシード処理に Azure Data Box を使用しない場合、および上記の変更率とバックアップ時間に基づいて毎日のバックアップを完了する場合に、目標を達成できるかどうかを適切に評価できます。 Azure へのバックアップが無料で利用できる帯域幅を特定するために使用できる方法を以下に示します。

- 既存の Azure ExpressRoute のお客様ですか? Azure portal で[回線使用量](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics)を確認します。
- ISP に問い合わせることができます。 お客様と共有できる、既存の日単位および月単位の使用率を示すレポートが必要です。
- ルーターまたはスイッチ レベルでネットワーク トラフィックを監視することで、使用率を測定できる次のようなツールがいくつかあります。
  - [Solarwinds Bandwidth Analyzer パック](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco ネットワーク アシスタント](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>適切なストレージ オプションの選択

Azure をバックアップ ターゲットとして使用する場合、お客様は [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) を利用します。 Azure Blob Storage は、Microsoft のオブジェクト ストレージ ソリューションです。 Blob Storage は、いずれのデータ モデルや定義にも準拠していないデータである、非構造化データを大量に格納するために最適化されています。 また、Azure Storage はセキュリティで保護されており、スケーラブルであり、持続性や高可用性を備えています。 Microsoft のプラットフォームでは、内部 SLA を満たすための[回復性レベル](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json)を提供するために、適切なワークロードに適したストレージを柔軟に選択することができます。 Blob Storage は従量課金制のサービスです。 格納データの量、そのデータへのアクセス、およびクールおよびアーカイブ層の場合は最低限必要な保有期間について、[月単位で課金されます](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing)。 バックアップ データに適用できる回復性と階層化のオプションは以下の表にまとめられています。

**Azure Blob Storage の回復性オプション:**

|  |ローカル冗長  |ゾーン冗長  |geo 冗長  |geo ゾーン冗長  |
|---------|---------|---------|---------|---------|
|コピーの有効数     | 3         | 3         | 6         | 6 |
|可用性ゾーンの数     | 1         | 3         | 2         | 4 |
|リージョンの数     | 1         | 1         | 2         | 2 |
|セカンダリ リージョンへの手動フェールオーバー     | NA         | NA         | はい         | はい |

**Azure Blob Storage の層:**

|  | ホット層   |クール層   | アーカイブ層 |
| ----------- | ----------- | -----------  | -----------  |
| 可用性 | 99.9%         | 99%         | オフライン      |
| 利用料金 | より高いストレージ コスト、より低いアクセス、およびトランザクション コスト | より低いストレージ コスト、より高いアクセスおよびトランザクション コスト | 最も低いストレージ コスト、最も高いアクセスおよびトランザクション コスト |
| 最低限必要なデータ保有期間 | NA | 30 日 | 180 日 |
| 待機時間 (最初のバイトまでの時間) | ミリ秒 | ミリ秒 | 時間 |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure へのバックアップ コスト モデルのサンプル

パブリック クラウドを初めてご利用になるお客様には、従量課金制の概念は難しいかもしれません。 使用された容量に対してのみ料金が発生しますが、トランザクション (読み取りまたは書き込み) および [Azure Express Route Direct Local または Express Route の無制限データ プラン](https://azure.microsoft.com/pricing/details/expressroute/)が使用されている場合はオンプレミス環境に読み取られた[データのエグレス](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure からのデータのエグレスを含む) についても料金が発生します。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)で、表示価格または [Azure Storage 予約容量の価格](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)に基づいて what-if 分析を実行することができます。これにより、最大 38% の節約が実現します。 Azure にバックアップする月額料金をモデル化するために行使価格例を以下に示します。これは単なる例であり、***ここに取り込まれていないアクティビティにより価格が異なる場合があります。***


|コスト係数  |毎月のコスト  |
|---------|---------|
|クール ストレージ上の 100 TB のバックアップ データ     |$1556.48         |
|2 TB (1 日あたりの新しいデータの書き込み量) x 30 日     |$72 (トランザクション内)          |
|毎月の推定合計     |$1628.48         |
|---------|---------|
|パブリック インターネット経由でのオンプレミスへの 5 TB の 1 回限りの復元   | $527.26         |

> [!Note]
この見積額は、米国東部の従量課金制価格を使用して Azure 料金計算ツールで生成されたものであり、Veeam の WAN 転送の既定の 256 kb のチャンク サイズに基づいています。 この例はお客様の要件に当てはまらない場合があります。

## <a name="implementation-and-operational-guidance"></a>実装と運用に関するガイダンス

このセクションでは、オンプレミスの Veeam デプロイに Azure Storage を追加するための簡単なガイドを提供します。 詳細なガイダンスと計画に関する考慮事項に関心をお持ちの場合は、[Veeam Cloud Connect Backup ガイド](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)を参照することをお勧めします。

1. Azure portal を開き、"ストレージ アカウント" を検索するか、既定のサービス アイコンをクリックします。

      ![Azure portal](../media/azure-portal.png)

      ![Azure Portal のストレージ アカウント](../media/locate-storage-account.png)

2. アカウントを追加することを選ぶか、リソース グループを選択または作成し、一意の名前を指定してリージョンを選び、[Standard] のパフォーマンスを選択し、アカウントの種類は常に [ストレージ V2] のままとし、SLA を満たすレプリケーション レベルと、バックアップ ソフトウェアで利用する既定の層を選びます。 Azure ストレージ アカウントを使用すると、ホット、クール、およびアーカイブ層を 1 つのアカウント内で使用できるようになります。また、Veeam ポリシーを使用すると、複数の層を利用してデータのライフサイクルを効果的に管理できます。 次の手順に進みます。 
    
      ![ストレージ アカウントの作成](../media/account-create-1.png)

3. ここでは既定のネットワーク オプションを引き続き使用して、[データ保護] に移ります。 ここで、[論理的な削除] を有効にすることを選択できます。これにより、誤って削除されたバックアップ ファイルを定義された保有期間内に回復し、誤削除または悪意のある削除から保護することができます。 
    ![ストレージ アカウントの作成パート 2](../media/account-create-2.png)

4. 次に、Azure へのバックアップ ユース ケースでは [詳細設定] 画面の既定の設定を使用することをお勧めします。

    ![ストレージ アカウントの作成パート 3](../media/account-create-3.png) 

5. タグ付けを利用し、アカウントを作成する場合は、組織のタグを追加します。 これで、数ペタバイトのオンデマンド ストレージを自由にご利用いただけます。

6. これでアカウントを Veeam 環境に追加する前に必要なのは、2 つの簡単な手順だけになりました。 Azure Portal で作成したアカウントに移動し、ポータル ブレードの [Blob Service] メニューで [コンテナー] を選択します。 新しいコンテナーを追加し、わかりやすい名前を選びます。 その後、[設定] の下にある [アクセス キー] 項目に移動し、[ストレージ アカウント名] と 2 つのアクセス キーの 1 つをコピーします。 そのコンテナー名、アカウント名、およびアクセス キーは次の手順で必要になります。

    ![コンテナーの作成](../media/container-b.png)
    
    ![そのアカウント情報を取得する](../media/access-key.png)
    
    > [!Note]
Veeam Backup and Replication により、Azure に接続するための追加オプションが提供されます。 Microsoft Azure Blob Storage をバックアップ ターゲットとして利用するこの記事のユース ケースでは、上記の方法を使用することをお勧めします。

7. ***(省略可能)*** デプロイにセキュリティ層をさらに追加することができます。

     1. ストレージ アカウントに変更を加えることができるユーザーを制限するようにロール ベース アクセスを構成します。 [詳しくはこちらをご覧ください](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. 企業ネットワーク外からアクセスが試行されないように、[ストレージ ファイアウォール](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)を使用して、アカウントへのアクセスを特定のネットワーク セグメントに限定します。

    ![ストレージ ファイアウォール](../media/storage-firewall.png) 

    1. アカウントに[削除ロック](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)を設定し、ストレージ アカウントが誤って削除されないようにします。

    ![リソース ロック](../media/resource-lock.png) 1.)追加の[セキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)を構成します。 
8. Veaam Backup and Replication 管理コンソールで、[Backup Infrastructure]\(バックアップ インフラストラクチャ\) に移動し、概要ウィンドウで右クリックし、[Add Backup Repository]\(バックアップ リポジトリの追加\) を選択して構成ウィザードを開きます。 ダイアログ ボックスで、オブジェクト ストレージ --> [Microsoft Azure Blob Storage] --> [Azure Blob Storage] の順に選択します。
    
    ![Veeam リポジトリ ウィザードの画面 a](../media/veeam-repo-a.png)

    ![Veeam リポジトリ ウィザードの画面 b](../media/veeam-repo-b.png)

    ![Veeam リポジトリ ウィザードの画面 c](../media/veeam-repo-c.png)

9. 次に、新しい Microsoft Azure Blob リポジトリの名前と説明を指定します。
    
    ![Veeam リポジトリ ウィザードの画面 d](../media/veeam-repo-d.png)

10. 次の手順では、Azure Storage アカウントにアクセスするための資格情報を追加します。 クラウド資格情報マネージャーで [Microsoft Azure Storage アカウント] を選択し、ストレージ アカウント名とアクセス キーを入力します。 リージョン セレクターで [Azure グローバル] を選び、該当する場合は任意のゲートウェイ サーバーを選択します。
    
    ![Veeam リポジトリ ウィザードの画面 e](../media/veeam-repo-e.png)

> [!Note]
Veeam ゲートウェイ サーバーを使用しないことにした場合は、すべてのスケールアウト リポジトリ エクステントで直接インターネットにアクセスできることを確認します。

11. コンテナー登録で、ご利用の Azure Storage コンテナーを選び、バックアップを格納するフォルダーを選択するか作成します。 Veeam によって使用される全体的なストレージ容量に対して、ソフト制限を定義することもできます (推奨)。 概要セクションに表示されている情報を確認し、構成ツールを完了します。 その後、バックアップ ジョブの構成で新しいリポジトリを選択できます。

    ![Veeam リポジトリ ウィザードの画面 f](../media/veeam-repo-f.png)
    
    ![Veeam リポジトリ ウィザードの画面 g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Azure のアラートとパフォーマンスの監視

バックアップを格納するために利用するストレージ ターゲットの場合と同じように、Azure リソースと Veeam の機能を利用する場合はこれらの両方を監視することをお勧めします。 Azure Monitor と Veeam の監視機能 (つまり、Veeam 管理コンソールのジョブ ノードの [統計] タブ、または Veeam One Reporter などの詳細オプション) を組み合わせると、ご利用の環境を正常な状態に保つのに役立ちます。

#### <a name="microsoft-azure-portal"></a>Microsoft Azure portal

Microsoft Azure により、[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource) の形で堅牢な監視ソリューションが提供されます。 Azure Storage の容量、トランザクション、可用性、認証などを追跡するように [Azure Monitor を構成する](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration)ことができます。 追跡されるメトリックの完全なリファレンスについては、[こちら](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)を参照してください。 追跡するいくつかの便利なメトリックには、BlobCapacity (最大[ストレージ アカウント容量制限](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account)未満のままになるようにする)、Ingress と Egress (Azure Storage アカウント間で書き込まれ、読み取られるデータの量を追跡する)、および SuccessE2ELatency (Azure Storage と MediaAgent との間の要求のラウンド トリップ時間を追跡する) があります。 

また、[ログ アラートを作成](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)して Azure Storage サービスの正常性を追跡し、いつでも [Azure の状態ダッシュボード](https://status.azure.com/status)を表示することができます。

#### <a name="veeam-reporting"></a>Veeam レポート

[Veeam One レポートの構成](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam Backup and Replication アラーム](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>サポート ケースを開く方法

Azure へのバックアップ ソリューションについてサポートが必要な場合は、Veeam と Azure の両方でケースを開くことをお勧めします。これにより、サポート組織は必要に応じて共同で作業を行うことができます。

#### <a name="how-to-open-a-case-with-veeam"></a>Veeam でケースを開く方法

[Veeam カスタマー サポート ポータル](https://www.veeam.com/support.html)に移動し、サインインして、ケースを開きます。

Veeam によって利用可能なサポート オプションについて理解する必要がある場合は、「[Veeam カスタマー サポート ポリシー](https://www.veeam.com/veeam_software_support_policy_ds.pdf)」を参照してください

また、電話でケースを開くこともできます。

[各国のサポート番号](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Azure サポート チームに連絡してケースを開く方法

[Azure portal](https://portal.azure.com) 内で、ポータルの上部にある検索バーで "サポート" を検索し、[+ 新しいサポート リクエスト] を選びます 
> [!Note]
ケースを開く際には、"Azure Backup" **ではなく**、"Azure Storage" または "Azure ネットワーク" についてサポートが必要であることを明確にしてください。 Azure Backup は Microsoft Azure ネイティブ サービスであり、お客様のケースは正しくルーティングされません。

### <a name="links-to-relevant-veeam-documentation"></a>関連する Veeam ドキュメントへのリンク

詳細については、以下の Veeam ドキュメントを参照してください。

[Veeam ユーザー ガイド](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Veeam アーキテクチャ ガイド](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Marketplace オファリングへのリンク

既知の信頼できる Veeam ソリューションを引き続き使用して、Azure で実行されているワークロードを保護することもできます。 Veeam を使用することで、Azure にソリューションをデプロイし、Azure Virtual Machines やその他の多くの Azure サービスを保護することが簡単になりました。

[Azure Marketplace を介して Veeam B&R をデプロイする](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Azure データシート](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>次のステップ

これらの外部 Web サイト上のその他のリソースを調べ、特殊な使用シナリオに関する情報を取得します。

[Veeam のハウツー ビデオ](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Veeam の技術文書](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam のナレッジ ベースと FAQ](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
