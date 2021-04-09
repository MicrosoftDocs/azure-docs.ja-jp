---
title: FAQ - Avere vFXT for Azure
description: これらのよく寄せられる質問を使用して、Avere vFXT for Azure が自分のニーズに合っているかどうかを判断します。 Avere vFXT for Azure と他の Azure コンポーネントとの連携のしくみについて説明します。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342367"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure の FAQ

この記事では、Avere vFXT for Azure がニーズに適しているかどうかを判断するのに役立つ質問に回答します。 Avere vFXT に関する基本情報を提供し、他の Azure コンポーネントおよび外部のベンダーの製品との連携について説明します。

## <a name="general"></a>全般

### <a name="what-is-avere-vfxt-for-azure"></a>Avere vFXT for Azure とは

Avere vFXT for Azure は、重要なワークロードの効率的な処理のために、Azure コンピューティングのアクティブなデータをキャッシュする高パフォーマンスのファイル システムです。

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT はストレージ ソリューションですか?

いいえ。 Avere vFXT for Azure は、EMC や NetApp の NAS または Azure Blob コンテナーといったストレージ環境に接続するファイル システム *キャッシュ* です。 Avere vFXT は、クライアントからのデータ要求を効率化し、提供するデータをキャッシュすることによって大規模な環境のパフォーマンスを徐々に向上させます。 Avere vFXT 自体にはデータは保存されません。 その背後に保存されているデータの量に関する情報は保持されません。

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT は階層化ソリューションですか?

Avere vFXT for Azure によって、ホット層とクール層の間でデータが自動的に階層化されることはありません。  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>環境が Avere vFXT に適しているかどうかを確認するにはどうすればよいですか?

この質問を検討する最善の方法は、"ワークロードがキャッシュ可能かどうか" を確認することです。 つまり、書き込みに対する読み取りのワークロードの比率が高いかどうかということです。 たとえば、読み取り/書き込みが 80/20 や 70/30 の場合です。

多数の Azure 仮想マシンで実行されるファイル ベースの分析パイプラインがあり、次の条件を 1 つ以上満たしている場合は、Avere vFXT for Azure を検討します。

* 長いファイル アクセス時間のため、全体的なパフォーマンスが遅いか、または一定していない (要件に応じて、十ミリ秒単位から秒単位)。 この待機時間では、カスタマーに受け入れられません。

* 処理に必要なデータが WAN 環境の遠い端にあり、そのデータを永続的に移動することが現実的ではない。 データは、別の Azure リージョンまたは顧客のデータ センターに存在する場合があります。

* 多数のクライアントがデータを要求している (たとえば、ハイ パフォーマンス コンピューティング (HPC) クラスターなど)。 多数の同時要求によって待機時間が長くなる可能性があります。

* 顧客が現在のパイプラインを "そのまま" Azure 仮想マシンで実行することを望んでおり、スケーラビリティのため POSIX ベースの共有ストレージ (またはキャッシュ) ソリューションが必要である。 Avere vFXT for Azure を使用すれば、Azure Blob Storage をネイティブに呼び出すために作業パイプラインを再設計する必要はありません。

* HPC アプリケーションが、NFSv3 クライアントに基づいている。 (場合によっては、SMB 2.1 クライアントを使用できますが、パフォーマンスが制限されます。)

次の図により、この質問に対する回答を得られます。 ワークフローが右上に近いほど、Avere vFXT for Azure のキャッシュ ソリューションが環境に適している可能性が高くなります。

![何千ものクライアントがいる読み取り負荷の高い環境ほど Avere vFXT に適していることを示すグラフの図](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Avere vFXT ソリューションが最も適しているのはどのようなスケールのクライアントですか?

Avere vFXT キャッシュ ソリューションは、数百、数千、または数万のコンピューティング コアを処理するように構築されます。 数台のマシンのみを使用していて、作業が軽量な場合は、Avere vFXT は適切なソリューションではありません。

Avere vFXT の一般的なお客様は、約 1,000 個以上の CPU コアで要求の厳しいワークロードを実行しています。 コアが 50,000 個以上の環境にも対応できます。 Avere vFXT はスケーラブルなので、ワークロードが大きくなって必要なスループットや IOPS が増えたらノードを追加して対応できます。

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Avere vFXT 環境はどれくらいの量のデータを格納できますか?

Avere vFXT for Azure はキャッシュです。 厳密に言えばデータを格納することはありません。 RAM と SSD の組み合わせを使用して、キャッシュされたデータを格納します。 データが永続的に格納されるのはバックエンド ストレージ システム (たとえば、NetApp NAS システムや BLOB コンテナー) です。 その背後に保存されているデータの量に関する情報は、Avere vFXT システムに保持されません。 Avere vFXT は、クライアントが要求するデータのサブセットのみをキャッシュします。  

### <a name="what-regions-are-supported"></a>どのリージョンがサポートされていますか?

Avere vFXT for Azure は、ソブリン リージョン (中国、ドイツ) を除くすべてのリージョンでサポートされています。 使用するリージョンが、Avere vFXT クラスターを作成するために必要な大量のコンピューティング コアと VM インスタンスをサポートできることを確認してください。

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Avere vFXT のヘルプを利用するにはどうすればよいですか?

専門のサポート スタッフのグループが、Avere vFXT for Azure に関するヘルプを提供しています。 [お使いのシステムでヘルプを利用する](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)手順に従って、Azure portal からサポート チケットを開いてください。

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT は高可用性ですか?

はい、Avere vFXT は HA ソリューションとしてのみ実行されます。

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT for Azure では他のクラウド サービスもサポートされていますか?

はい、お客様は Avere vFXT クラスターで複数のクラウド プロバイダーを使用できます。 AWS S3 標準バケットと Google Cloud Services 標準バケットおよび Azure BLOB コンテナーがサポートされています。

> [!NOTE]
> AWS または Google Cloud ストレージでの Avere vFXT の使用にはソフトウェア料金がかかります。 Azure Blob Storage を使用する場合は、追加のソフトウェア料金は発生しません。

## <a name="technical-compute"></a>技術: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Avere vFXT 環境とは "どのようなもの" ですか?

Avere vFXT は、複数の Azure 仮想マシンによって構成されるクラスター化されたアプライアンスです。 クラスターの作成、削除、および変更は、Python ライブラリによって処理されます。 詳しくは、「[Avere vFXT for Azure とは](avere-vfxt-overview.md)」をご覧ください。

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Avere vFXT はどのような種類の Azure 仮想マシン上で実行されますか?  

Avere vFXT for Azure クラスターでは、Microsoft Azure E32s_v3 仮想マシンが使用されます。

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Avere vFXT 環境はスケーリングできますか?

Avere vFXT クラスターは、最小 3 ノードから最大 24 ノードまでの範囲で仮想マシン ノードを構成できます。 9 個より多くのノードがクラスターに必要と思われる場合は、計画に関して Azure のテクニカル サポートにお問い合わせください。 ノードの数が多いほど、大規模なデプロイ アーキテクチャが必要になります。

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Avere vFXT 環境は "自動スケーリング" ですか?

いいえ。 クラスターのサイズをスケールアップ/ダウンできますが、クラスター ノードの追加または削除は手動のステップです。

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Avere vFXT クラスターを仮想マシン スケール セットとして実行できますか?

Avere vFXT では、仮想マシン スケール セットのデプロイはサポートされていません。 組み込まれている可用性サポート メカニズムは、クラスターに参加しているアトミック VM 用にのみ設計されています。  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>優先順位の低い VM で Avere vFXT クラスターを実行できますか?

いいえ、システムは安定した仮想マシンのセットが基になっている必要があります。

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>コンテナー内で Avere vFXT クラスターを実行できますか?

いいえ、Avere vFXT は独立したアプリケーションとして展開する必要があります。

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Avere vFXT の VM は計算クォータに含まれますか?

はい。 クラスターをサポートするのに十分なクォータがリージョンにあることを確認してください。  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Avere vFXT クラスターのマシンを別の可用性ゾーン内で実行できますか?

いいえ。 現在、Avere vFXT で使用されている高可用性モデルでは、Avere vFXT クラスターの個々のメンバーが異なる可用性ゾーンに存在することはサポートされていません。

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Avere vFXT の仮想マシンを複製できますか?

いいえ、Avere vFXT クラスター内のノードを追加または削除するには、サポートされている Python スクリプトを使用する必要があります。 詳しくは、「[Avere vFXT クラスターの管理](avere-vfxt-manage-cluster.md)」をご覧ください。

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>自分のローカル環境で実行できるソフトウェアの "VM" バージョンはありますか?

いいえ、システムはクラスター化されたアプライアンスとして提供され、特定の仮想マシンの種類でテストされています。 この制限により、一般的な Avere vFXT ワークフローの高パフォーマンス要件をサポートできないシステムが作成されるのを防ぐことができます。

## <a name="technical-disks"></a>技術: ディスク

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Azure VM に対してサポートされているディスクの種類は何ですか?

Avere vFXT for Azure では、1 TB または 4 TB の Premium SSD 構成を使用できます。 Premium SSD 構成は、複数のマネージド ディスクとして展開できます。

### <a name="does-the-cluster-support-unmanaged-disks"></a>クラスターではアンマネージド ディスクはサポートされますか?

いいえ、クラスターにはマネージド ディスクが必要です。

### <a name="does-the-system-support-local-attached-ssds"></a>システムではローカルな (アタッチされた) SSD がサポートされますか?

現在、Avere vFXT for Azure ではローカル SSD はサポートされていません。 Avere vFXT に使用されるディスクはシャットダウンして再起動できる必要がありますが、この構成でアタッチされるローカル SSD は終了のみが可能です。

### <a name="does-the-system-support-ultra-ssds"></a>システムでは Ultra SSD はサポートされますか?

いいえ、システムでは Premium SSD 構成のみがサポートされます。

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Premium SSD をデタッチしてから後で再アタッチし、その間にキャッシュの内容を保持することはできますか?

SSD のデタッチと再アタッチはサポートされていません。 その間にソースのメタデータまたはファイルの内容が変更され、データ整合性の問題の原因になる可能性があります。

### <a name="does-the-system-encrypt-the-cache"></a>キャッシュは暗号化されますか?

データはディスク間にストライピングされますが、暗号化はされません。 ただし、ディスク自体を暗号化することはできます。 詳細については、[Azure で仮想マシンをセキュリティで保護し、ポリシーを使用する](../virtual-machines/security-policy.md#encryption)に関するページをご覧ください。

## <a name="technical-networking"></a>技術: ネットワーク

### <a name="what-network-is-recommended"></a>どのようなネットワークが推奨されますか?

Avere vFXT でオンプレミスのストレージを使用する場合は、ストレージとクラスター間に 1 Gbps 以上のネットワーク接続が必要です。 データの量が少なく、ジョブを実行する前にクラウドにデータをコピーしたい場合は、VPN 接続で十分なことがあります。

> [!TIP]
> ネットワーク リンクが遅くなるほど、初期の "コールド" 読み取りが遅くなります。 読み取りが遅いと、作業パイプラインの待機時間が増加します。

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>コンピューティング クラスターとは別の仮想ネットワークで Avere vFXT を実行できますか?

はい、別の仮想ネットワークに Avere vFXT システムを作成できます。 詳しくは、「[Avere vFXT システムの計画](avere-vfxt-deploy-plan.md)」をご覧ください。

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT には専用のサブネットが必要ですか?

はい。 Avere vFXT は高可用性 (HA) クラスターとして厳密に実行され、動作するには複数の IP アドレスが必要です。 クラスターが専用のサブネットに存在する場合は、インストールと通常の動作の問題となる可能性がある IP アドレスの競合のリスクを回避できます。 IP アドレスの重複がない限り、クラスターのサブネットは他のリソースによって使用される仮想ネットワーク内でもかまいません。

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>InfiniBand 上で Avere vFXT を実行できますか?

いいえ、Avere vFXT はイーサネット/IP のみを使用します。

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Avere vFXT からオンプレミスの NAS 環境にアクセスするにはどうすればよいですか?

Avere vFXT 環境は、ネットワーク ゲートウェイまたは VPN 経由でお客様のデータ センターにアクセスがルーティングされる (そして返される) 必要があるという点で他の Azure VM に似ています。 お使いの環境で使用できる場合は、Azure ExpressRoute 接続を使用することを検討してください。

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Avere vFXT の帯域幅要件はどれくらいですか?

全体的な帯域幅の要件は、2 つの要因に依存します。

* ソースから要求されるデータの量
* 初期データ読み込み時にクライアント システムが許容できる待機時間  

待機時間が重要な環境では、リンク速度が 1 Gbps 以上のファイバー ソリューションを使用する必要があります。 利用できる場合は、ExpressRoute を使用します。  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>パブリック IP アドレスを使用して Avere vFXT を実行できますか?

いいえ、Avere vFXT はベスト プラクティスを通じてセキュリティ保護されたネットワーク環境内で動作することが意図されています。

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>クラスターの仮想ネットワークからのインターネット アクセスを制限できますか?

一般的に、必要に応じて自分の仮想ネットワークで追加のセキュリティを構成できます。ただし、一部の制限がクラスターの操作に干渉することがあります。

たとえば、仮想ネットワークからのアウトバウンドのインターネット アクセスを制限すると、AzureCloud へのアクセスを明示的に許可するルールも追加しない限り、クラスターに問題が発生します。 この状況に関する説明は [GitHub の補足ドキュメント](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)にあります。

セキュリティのカスタマイズでヘルプが必要な場合、「[お使いのシステムでサポートを受ける](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)」に記載されているサポートにお問い合わせください。

## <a name="technical-back-end-storage-core-filers"></a>技術: バックエンド ストレージ (コア ファイラー)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>1 つの Avere vFXT 環境でいくつのコア ファイラーをサポートできますか?

Avere vFXT クラスターでは、最大 20 個のコア ファイラーがサポートされます。

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Avere vFXT 環境ではどのようにデータが保存されますか?

Avere vFXT はストレージではありません。 Avere vFXT は、コア ファイラーと呼ばれる複数のターゲット ストレージのデータを読み書きするキャッシュです。 Avere vFXT の Premium SSD ディスクに格納されるデータは一時的であり、最終的にはバックエンドのコア ファイラー ストレージにフラッシュされます。

### <a name="which-core-filers-does-avere-vfxt-support"></a>Avere vFXT ではどのようなコア ファイラーがサポートされますか?

一般に、Avere vFXT for Azure ではコア ファイラーとして次のシステムがサポートされています。

* Dell EMC Isilon (OneFS 7.1、7.2、8.0、8.1) 
* NetApp ONTAP (Clustered Mode 9.4、9.3、9.2、9.1P1、8.0 から 8.3) および (7-Mode 7.*、8.0 から 8.3)

* Azure Blob コンテナー (ローカル冗長ストレージのみ)
* AWS S3 バケット
* Google Cloud バケット

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Avere vFXT ですべての NFS ファイラーがサポートされていないのはなぜですか?

すべての NFS プラットフォームが同じ IETF 標準を満たしていますが、実際には実装ごとに独自の特異性があります。 これらの細部が、Avere vFXT とストレージ システムのやり取りの方法に影響します。 サポートされているシステムは、市場で最も広く使用されているプラットフォームです。

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT では (SwiftStack のような) プライベート オブジェクト ストレージはサポートされますか?

Avere vFXT では、プライベート オブジェクト ストレージはサポートされていません。

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>特定のストレージ製品をサポートしてもらうにはどうすればよいですか?

サポートは、現場での需要の量に基づきます。 NAS ソリューションをサポートするのに十分な収益ベースの要求がある場合は、サポートが検討されます。 Azure サポートにリクエストしてください。

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>コア ファイラーとして Azure Blob Storage を使用できますか?

はい、Avere vFXT for Azure では、ブロック BLOB コンテナーをクラウド コア ファイラーとして使用できます。

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>BLOB コア ファイラーにはどのようなストレージ アカウント要件がありますか?

ストレージ アカウントは、汎用 v2 (GPv2) アカウントであり、ローカル冗長ストレージ専用に構成されている必要があります。 Geo 冗長ストレージとゾーン冗長ストレージはサポートされていません。

ストレージ アカウントの要件については、「[Azure Blob Storage クラウド コア フィルター](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer)」を参照してください。

### <a name="can-i-use-archive-blob-storage"></a>アーカイブ BLOB ストレージを使用できますか?

いいえ。 アーカイブ ストレージのサービス レベル アグリーメント (SLA) は、Avere vFXT システムで必要なリアルタイムのディレクトリおよびファイル アクセスと互換性がありません。

### <a name="can-i-use-cool-blob-storage"></a>クール BLOB ストレージを使用できますか?

通常、クール層の Blob ストレージは、Avere vFXT for Azure コア ファイラーには推奨されません。 クール層では、ストレージコストはより低くなりますが、運用コストはより高くなります。 (詳細については、「[ブロック BLOB の価格](<https://azure.microsoft.com/pricing/details/storage/blobs/>)」を参照してください。)頻繁にデータにアクセスし、変更または削除する場合は、ホット層の使用を検討してください。

「[アクセス層](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier)」では、vFXT コア ファイラーとしてクール層ストレージを使用すると便利な場合の詳細情報を提供します。

### <a name="how-do-i-encrypt-the-blob-container"></a>BLOB コンテナーはどのようにして暗号化すればよいですか?

BLOB 暗号化は Azure で構成するか (推奨)、または vFXT コア ファイラー レベルで構成できます。  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>BLOB コア ファイラーに対して独自の暗号化キーを使用できますか?

既定では、データは Azure Blob Storage、Table Storage、および Queue Storage、および Azure Files 用の Microsoft 管理キーを通じて暗号化されます。 BLOB ストレージや Azure Files の暗号化に独自のキーを使用することもできます。 Avere vFXT 暗号化を使用する場合は、Avere によって生成されたキーを使用し、ローカルに保存する必要があります。

## <a name="purchasing"></a>購入

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Avere vFXT for Azure のライセンスはどのようにして入手できますか?

Avere vFXT for Azure のライセンスは Azure Marketplace で簡単に入手できます。 Azure アカウントにサインアップした後、「[Avere vFXT クラスターのデプロイ](avere-vfxt-deploy.md)」の説明に従って Avere vFXT クラスターを作成します。

### <a name="how-much-does-avere-vfxt-cost"></a>Avere vFXT のコストはどれくらいですか?

Azure では、Avere vFXT クラスターを使用するための追加ライセンス料金はありません。 お客様は、ストレージと他の Azure の消費量の料金を負担します。

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT VM は、低優先度で実行できますか?

いいえ、Avere vFXT クラスターには "Always On" サービスが必要です。 クラスターは必要ないときはオフにできます。

## <a name="next-steps"></a>次のステップ

Avere vFXT for Azure を使い始めるには、以下の記事を参照して独自のシステムを計画して展開する方法を理解してください。

* [Avere vFXT システムの計画](avere-vfxt-deploy-plan.md)
* [デプロイの概要](avere-vfxt-deploy-overview.md)
* [Avere vFXT クラスターの作成を準備する](avere-vfxt-prereqs.md)
* [Avere vFXT クラスターをデプロイする](avere-vfxt-deploy.md)

Avere vFXT の機能とユース ケースの詳細については、「[Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/)」をご覧ください。