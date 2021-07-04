---
title: Veeam を使用して Azure にデータをバックアップする
titleSuffix: Azure Storage
description: 検討すべき要素の概要と、Veeam Backup and Recovery のストレージ ターゲットおよび回復場所として Azure を使用するために従う手順が示されています
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589907"
---
# <a name="backup-to-azure-with-veeam"></a>Veeam を使用した Azure へのバックアップ

この記事では、Veeam インフラストラクチャと Azure Blob Storage を統合する方法について説明します。 これには前提条件、考慮事項、実装、および運用に関するガイダンスが含まれます。 この記事では、プライマリ サイト内での通常の運用を妨げる障害発生時に、Azure をオフサイト バックアップ ターゲットおよび復元サイトとして使用する方法について説明します。

> [!NOTE]
> Veeam は、目標復旧時間 (RTO) を短縮するソリューションである Veeam レプリケーションも提供します。 このソリューションでは、Azure 運用環境で障害が発生した場合により迅速に復旧するのに役立つスタンバイ VM を使用できます。 Veeam には、Azure および Office 365 リソースをバックアップするための専用のツールもあります。 これらの機能は、このドキュメントの範囲外です。

## <a name="reference-architecture"></a>参照アーキテクチャ

次の図は、オンプレミスから Azure および Azure 内のデプロイに関する参照アーキテクチャを示しています。

![Veeam から Azure の参照アーキテクチャ図。](../media/veeam-architecture.png)

既存の Veeam デプロイは、クラウド バックアップ リポジトリとして Azure Storage アカウントまたは複数のアカウントを追加することで、Azure と簡単に統合できます。 また、Veeam では、オンプレミスから Azure 内にバックアップを回復することもできます。これにより、Azure のオンデマンド復旧サイトが提供されます。

## <a name="veeam-interoperability-matrix"></a>Veeam の相互運用性マトリックス

| ワークロード | GPv2 と Blob Storage | クール層のサポート | アーカイブ層のサポート | Data Box ファミリのサポート |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| オンプレミスの VM またはデータ | v10a | v10a | 該当なし | 10a<sup>*</sup> |
| Azure VM | v10a | v10a | 該当なし | 10a<sup>*</sup> |
| Azure BLOB | v10a | v10a | 該当なし | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | 該当なし | 10a<sup>*</sup> |

<sup>*</sup>Veeam Backup and Replication では、Azure Data Box に対してのみ REST API がサポートされます。 そのため、Azure Data Box Disk はサポートされません。

## <a name="before-you-begin"></a>開始する前に

少し前に計画を立てることで、Azure をオフサイト バックアップ ターゲットおよび復旧サイトとして使用できます。

### <a name="get-started-with-azure"></a>Azure を使用して作業を開始する

Microsoft では、Azure の使用を開始する際に従うフレームワークを提供しています。 [クラウド導入フレームワーク](/azure/architecture/cloud-adoption/) (CAF) は、エンタープライズ デジタル変革に向けた詳細なアプローチであり、運用レベルのクラウド導入を計画するための包括的なガイドです。 CAF には、迅速かつ安全に運用するのに役立つステップバイステップの [Azure セットアップガイド](/azure/cloud-adoption-framework/ready/azure-setup-guide/)が含まれています。 対話型バージョンは [Azure portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)で見つけることができます。 アプリケーションをデプロイするためのサンプル アーキテクチャ、具体的なベスト プラクティス、および Azure の専門知識を活用できる無料のトレーニング リソースが記載されています。

### <a name="consider-the-network-between-your-location-and-azure"></a>お客様の場所と Azure の間のネットワークを検討する

クラウド リソースを運用、テストおよび開発を行うために使用する場合でも、バックアップ ターゲットおよび復旧サイトとして利用する場合でも、初期バックアップのシード処理と、毎日の継続的な転送における帯域幅のニーズを理解することが重要です。

Azure Data Box によって、追加の帯域幅を必要とすることなく、初期バックアップ ベースラインを Azure に転送する手段が提供されます。 これは、ベースライン転送にかかる時間が許容できる時間よりも長いと推定される場合に役に立ちます。 ストレージ アカウントを作成するときにデータ転送予測ツールを使用して、初期バックアップの転送に必要な時間を予測することができます。

![ポータル内の Azure Storage データ転送予測ツールを示します。](../media/az-storage-transfer.png)

運用アプリケーションに影響を与えることなく、必要な転送時間 (バックアップ時間) 内での毎日のデータ転送をサポートするには、十分なネットワーク容量が必要になることに注意してください。 このセクションでは、ネットワークのニーズを評価するために使用できるツールと手法について概説します。

#### <a name="determine-how-much-bandwidth-youll-need"></a>必要な帯域幅の量を決定する

Azure への初期ベースライン転送の変更率とバックアップ セットの合計サイズを決定するために、複数の評価オプションを使用できます。 以下に評価およびレポート ツールの例をいくつか示します。

- [MiTrend](https://mitrend.com/)
- [Apt are](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>未使用のインターネット帯域幅を確認する

日常的に利用可能な通常は未使用の帯域幅 (つまり *ヘッドルーム*) がどれだけあるかを把握しておくことが重要です。 これは、以下に関する目標を達成できるかどうかを評価するのに役立ちます。

- オフライン シード処理に Azure Data Box を使用していない場合の最初のアップロード時間
- 上記の変更率とバックアップ時間に基づいた毎日のバックアップの完了

以下の方法を使用して、Azure へのバックアップが無料で利用できる帯域幅を特定します。

- 既存の Azure ExpressRoute を使用している場合は、Azure portal で[回線使用量](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)を確認します。
- ISP に連絡してください。 既存の日単位および月単位の使用状況を示すレポートを共有できるようにする必要があります。
- ルーターまたはスイッチ レベルでネットワーク トラフィックを監視することで、使用率を測定できるツールがいくつかあります。 これには以下が含まれます。

  - [Solarwinds Bandwidth Analyzer パック](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco ネットワーク アシスタント](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>適切なストレージ オプションを選択する

Azure をバックアップ ターゲットとして使用する場合は、[Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md)を使用します。 Blob Storage は、Microsoft のオブジェクト ストレージ ソリューションです。 Blob Storage は、いずれのデータ モデルや定義にも準拠していないデータである、非構造化データを大量に格納するために最適化されています。 また、Azure Storage はセキュリティで保護されており、スケーラブルであり、持続性や高可用性を備えています。 ワークロードに適したストレージを選択して、内部 SLA を満たすための[回復性レベル](../../../../common/storage-redundancy.md)を提供できます。 Blob Storage は従量課金制のサービスです。 格納データの量、そのデータへのアクセス、およびクールおよびアーカイブ層の場合は最低限必要な保有期間について、[月単位で課金されます](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing)。 バックアップ データに適用できる回復性と階層化のオプションは以下の表にまとめられています。

**Blob Storage の回復性オプション:**

|  |ローカル冗長  |ゾーン冗長  |geo 冗長  |geo ゾーン冗長  |
|---------|---------|---------|---------|---------|
|**コピーの有効数**     | 3         | 3         | 6         | 6 |
|**可用性ゾーンの数**     | 1         | 3         | 2         | 4 |
|**リージョンの数**     | 1         | 1         | 2         | 2 |
|**セカンダリ リージョンへの手動フェールオーバー**     | 該当なし         | 該当なし         | はい         | はい |

**Blob Storage の層:**

|  | ホット層   |クール層   | アーカイブ層 |
| ----------- | ----------- | -----------  | -----------  |
| **可用性** | 99.9%         | 99%         | オフライン      |
| **利用料金** | より高いストレージ コスト、より低いアクセス、およびトランザクション コスト | より低いストレージ コスト、より高いアクセスおよびトランザクション コスト | 最も低いストレージ コスト、最も高いアクセスおよびトランザクション コスト |
| **最低限必要なデータ保有期間** | NA | 30 日 | 180 日 |
| **待機時間 (最初のバイトまでの時間)** | ミリ秒 | ミリ秒 | 時間 |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure へのバックアップ コスト モデルのサンプル

クラウドを初めてご利用になるお客様には、従量課金制の利用は難しいかもしれません。 使用された容量に対してのみ料金が発生しますが、トランザクション (読み取りまたは書き込み) および [Azure Express Route Direct Local または Express Route の無制限データ プラン](https://azure.microsoft.com/pricing/details/expressroute/)が使用されている場合はオンプレミス環境に読み取られた[データのエグレス](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure からのデータのエグレスを含む) についても料金が発生します。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して、"what-if" 分析を実行できます。 表示価格または [Azure Storage 予約容量の価格](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)に基づいて分析を行うことができます。これにより、最大 38% の節約が実現します。 Azure にバックアップする月額料金をモデル化するための行使価格例を以下に示します。 これは一例です。 *ここに取り込まれていないアクティビティにより料金が異なる場合があります。*

|コスト係数  |毎月のコスト  |
|---------|---------|
|クール ストレージ上の 100 TB のバックアップ データ     |$1556.48         |
|2 TB (1 日あたりの新しいデータの書き込み量) x 30 日     |$72 (トランザクション内)          |
|毎月の推定合計     |$1628.48         |
|---------|---------|
|パブリック インターネット経由でのオンプレミスへの 5 TB の 1 回限りの復元   | $527.26         |

> [!Note]
> この見積額は、米国東部の従量課金制価格を使用して Azure 料金計算ツールで生成されたものであり、Veeam の WAN 転送の既定の 256 kb のチャンク サイズに基づいています。 この例はお客様の要件に当てはまらない場合があります。

## <a name="implementation-guidance"></a>実装ガイダンス

このセクションでは、オンプレミスの Veeam デプロイに Azure Storage を追加する方法についての簡単なガイドを提供します。 詳細なガイダンスと計画に関する考慮事項については、「[Veeam Cloud Connect Backup ガイド](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)」を参照してください。

1. Azure portal を開き、 **[ストレージ アカウント]** を検索します。 既定のサービス アイコンをクリックすることもできます。

      ![Azure portal でのストレージ アカウントの追加を示します。](../media/azure-portal.png)

      ![Azure portal の検索ボックスに「ストレージ」と入力した状態を示します。](../media/locate-storage-account.png)

2. **[作成]** を選択してアカウントを追加します。 リソース グループを選択または作成し、一意の名前を指定してリージョンを選び、 **[標準]** のパフォーマンスを選択し、アカウントの種類は常に **[ストレージ V2]** のままとし、SLA を満たすレプリケーション レベルと、バックアップ ソフトウェアで適用する既定の層を選びます。 Azure ストレージ アカウントを使用すると、ホット、クール、およびアーカイブ層を 1 つのアカウント内で使用できるようになります。また、Veeam ポリシーを使用すると、複数の層を使用してデータのライフサイクルを効果的に管理できます。

    ![Azure portal 内のストレージ アカウント設定を示します](../media/account-create-1.png)

3. ここでは既定のネットワーク オプションを引き続き使用して、 **[データ保護]** に移ります。 ここで、[論理的な削除] を有効にすることを選択できます。これにより、誤って削除されたバックアップ ファイルを定義された保有期間内に回復し、誤削除または悪意のある削除から保護することができます。

    ![ポータルでのデータ保護設定を示します。](../media/account-create-2.png)

4. 次に、Azure へのバックアップ ユース ケースでは **[詳細設定]** 画面の既定の設定を使用することをお勧めします。

    ![ポータルの [詳細設定] タブを示します。](../media/account-create-3.png)

5. タグ付けを使用し、アカウントを作成する場合は、組織のタグを追加します。

6. これでアカウントを Veeam 環境に追加する前に必要なのは、2 つの簡単な手順だけになりました。 Azure portal で作成したアカウントに移動し、 **[Blob service]** メニューで **[コンテナー]** を選択します。 コンテナーを追加し、わかりやすい名前を選びます。 その後、 **[設定]** の下にある **[アクセス キー]** 項目に移動し、 **[ストレージ アカウント名]** と 2 つのアクセス キーの 1 つをコピーします。 そのコンテナー名、アカウント名、およびアクセス キーは次の手順で必要になります。

    ![ポータルでのコンテナーの作成を示します。](../media/container-b.png)

    ![ポータルでのアクセス キーの設定を示します。](../media/access-key.png)

    > [!Note]
    > Veeam Backup and Replication により、Azure に接続するための追加オプションが提供されます。 Microsoft Azure Blob Storage をバックアップ ターゲットとして使用するこの記事のユース ケースでは、上記の方法を使用することをお勧めします。

7. *(省略可能)* デプロイにセキュリティ層をさらに追加することができます。

     1. ストレージ アカウントに変更を加えることができるユーザーを制限するようにロール ベース アクセスを構成します。 詳細については、「[管理操作のための組み込みロール](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)」を参照してください。

     1. 企業ネットワーク外からアクセスが試行されないように、[ストレージ ファイアウォール設定](../../../../common/storage-network-security.md)を使用して、アカウントへのアクセスを特定のネットワーク セグメントに限定します。

        ![ポータル内のストレージ ファイアウォールの設定を示します。](../media/storage-firewall.png)

     1. アカウントに[削除ロック](../../../../../azure-resource-manager/management/lock-resources.md)を設定し、ストレージ アカウントが誤って削除されないようにします。

        ![リソース ロック](../media/resource-lock.png)

     1. 追加の[セキュリティに関するベスト プラクティス](../../../../../storage/blobs/security-recommendations.md)を構成します。

8. Veaam Backup and Replication 管理コンソールで、 **[Backup Infrastructure]\(バックアップ インフラストラクチャ\)** に移動し、概要ウィンドウで右クリックし、 **[Add Backup Repository]\(バックアップ リポジトリの追加\)** を選択して構成ウィザードを開きます。 ダイアログ ボックスで、**[Object storage]** ->  **[Microsoft Azure Blob Storage]**  ->  **[Azure Blob Storage]** の順に選択します。

    ![Veeam リポジトリ ウィザードでの Object storage の選択を示します。](../media/veeam-repo-a.png)

    ![Veeam リポジトリ ウィザードでの Microsoft Azure Blob Storage の選択を示します。](../media/veeam-repo-b.png)

    ![Veeam リポジトリ ウィザードでの Azure Blob Storage の選択を示します。](../media/veeam-repo-c.png)

9. 次に、新しい Blob Storage リポジトリの名前と説明を指定します。

    ![Veeam リポジトリ ウィザードでのリポジトリの名前の入力を示します。](../media/veeam-repo-d.png)

10. 次の手順では、Azure Storage アカウントにアクセスするための資格情報を追加します。 クラウド資格情報マネージャーで **[Microsoft Azure Storage アカウント]** を選択し、ストレージ アカウント名とアクセス キーを入力します。 リージョン セレクターで **[Azure Global]** を選び、該当する場合は任意のゲートウェイ サーバーを選択します。

    ![Veeam リポジトリ ウィザードでのアカウントの指定を示します。](../media/veeam-repo-e.png)

    > [!Note]
    > Veeam ゲートウェイ サーバーを使用しないことにした場合は、すべてのスケールアウト リポジトリ エクステントで直接インターネットにアクセスできることを確認します。

11. コンテナー登録で、ご利用の Azure Storage コンテナーを選び、バックアップを格納するフォルダーを選択するか作成します。 Veeam によって使用される全体的なストレージ容量に対して、ソフト制限を定義することもできます (推奨)。 概要セクションに表示されている情報を確認し、構成ツールを完了します。 バックアップ ジョブの構成で新しいリポジトリを選択できるようになりました。

    ![Veeam リポジトリ ウィザードでのコンテナーの指定を示します。](../media/veeam-repo-f.png)

    ![Veeam リポジトリ ウィザードでのフォルダーの作成を示します。](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>操作ガイダンス

### <a name="azure-alerts-and-performance-monitoring"></a>Azure のアラートとパフォーマンスの監視

バックアップを格納するために利用するストレージ ターゲットの場合と同じように、Azure リソースと Veeam の機能を利用する場合はこれらの両方を監視することをお勧めします。 Azure Monitor と Veeam の監視機能 (Veeam 管理コンソールの **[ジョブ]** ノードの **[統計]** タブ、または Veeam One Reporter などの詳細オプション) を組み合わせると、ご利用の環境を正常な状態に保つのに役立ちます。

#### <a name="azure-portal"></a>Azure portal

Azure により、[Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) の形で堅牢な監視ソリューションが提供されます。 Azure Storage の容量、トランザクション、可用性、認証などを追跡するように [Azure Monitor を構成する](../../../../blobs/monitor-blob-storage.md)ことができます。 追跡されるメトリックの完全なリファレンスについては、[こちら](../../../../blobs/monitor-blob-storage-reference.md)を参照してください。 追跡するいくつかの便利なメトリックには、BlobCapacity (最大[ストレージ アカウント容量制限](../../../../common/scalability-targets-standard-account.md)未満のままになるようにする)、Ingress と Egress (Azure Storage アカウント間で書き込まれ、読み取られるデータの量を追跡する)、および SuccessE2ELatency (Azure Storage と MediaAgent との間の要求のラウンド トリップ時間を追跡する) があります。

また、[ログ アラートを作成](../../../../../service-health/alerts-activity-log-service-notifications-portal.md)して Azure Storage サービスの正常性を追跡し、いつでも [Azure の状態ダッシュボード](https://status.azure.com/status)を表示することができます。

#### <a name="veeam-reporting"></a>Veeam レポート

- [Veeam One レポートの構成](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam Backup and Replication アラーム](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>サポート ケースを開く方法

Azure ソリューションへのバックアップについてサポートが必要な場合は、Veeam と Azure の両方でケースを開く必要があります。 これにより、サポート組織は必要に応じて共同作業を行うことができます。

#### <a name="to-open-a-case-with-veeam"></a>Veeam でケースを開くには

[Veeam カスタマー サポート サイト](https://www.veeam.com/support.html)でサインインし、ケースを開きます。

Veeam によって利用可能なサポート オプションについて理解するには、「[Veeam カスタマー サポート ポリシー](https://www.veeam.com/veeam_software_support_policy_ds.pdf)」を参照してください。

電話でケースを開くこともできます: [各国のサポート番号](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Azure でケースを開くには

[Azure portal](https://portal.azure.com) で、上部にある検索バーで「**サポート**」を検索します。 **[ヘルプとサポート]**  ->  **[新しいサポート要求]** の順に選択します。

> [!NOTE]
> ケースを開く際には、Azure Storage または Azure ネットワークについてサポートが必要であることを明確にしてください。 Azure Backup を指定しないでください。 Azure Backup は Azure サービスの名前であり、ケースは正しくルーティングされません。

### <a name="links-to-relevant-veeam-documentation"></a>関連する Veeam ドキュメントへのリンク

詳細については、以下の Veeam のドキュメントを参照してください。

- [Veeam ユーザー ガイド](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Veeam アーキテクチャ ガイド](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Marketplace のオファリング

既知の信頼できる Veeam ソリューションを引き続き使用して、Azure で実行されているワークロードを保護することができます。 Veeam を使用することで、Azure にソリューションをデプロイし、Azure Virtual Machines やその他の多くの Azure サービスを保護することが簡単になりました。

- [Azure Marketplace を介して Veeam Backup & Replication をデプロイする](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Veeam の Azure のバックアップと復旧に関する Web サイト](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>次のステップ

特殊な使用シナリオの詳細については、Veeam Web サイトにある以下のリソースを参照してください。

- [Veeam のハウツー ビデオ](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeam の技術文書](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam のナレッジ ベースと FAQ](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
