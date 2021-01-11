---
title: よく寄せられる質問
description: Azure VMware Solution についてよく寄せられる質問にお答えします。
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: cffa31bb66adfde2af24ab2542322479639ed9dd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752183"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Azure VMware Solution プレビューについてよく寄せられる質問

Azure VMware Solution についてよく寄せられる質問にお答えします。

## <a name="general"></a>全般

**Azure VMware Solution とは**

企業によるビジネスの機敏性の向上、コスト削減、イノベーションの促進のための IT 近代化戦略の追及を受けて、ハイブリッド クラウド プラットフォームが顧客のデジタル変革の主要な手段として登場しました。 Azure VMware Solution は、VMware の Software Defined Data Center (SDDC) ソフトウェアと Microsoft Azure グローバル クラウド サービス エコシステムを組み合わせたものです。 Azure VMware Solution は、パフォーマンス、可用性、セキュリティ、およびコンプライアンスの要件を満たすために管理されます。

## <a name="azure-vmware-solution-service"></a>Azure VMware Solution サービス

**現在、Azure VMware Solution はどこで利用できますか?**

このサービスは新しいリージョンに継続的に追加されているので、詳細については、[最新のサービスの可用性情報](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)に関するページを参照してください。 

**Azure VMware Solution インスタンスで実行されるワークロードで Azure サービスを使用できますか? また、このワークロードを Azure サービスと統合できますか?**

Azure VMware Solution のお客様はすべての Azure サービスを利用できるようになります。 特定のサービスのパフォーマンスと可用性の制限については、ケースバイケースで対処する必要があります。

**プライベート クラウドのリソースを管理するために現在使用しているのと同じツールを使用しますか?**

はい。 デプロイと多くの管理操作のために Azure portal を使用します。 vSphere と NSX-T のリソースを管理するために vCenter と NSX Manager を使用します。

**オンプレミスの vCenter を使用してプライベート クラウドを管理できますか?**

初期の Azure VMware Solution は、オンプレミス環境とプライベート クラウド環境全体に対して単一の管理エクスペリエンスをサポートしていません。 プライベート クラウド クラスターは、プライベート クラウドに対してローカルな vCenter および NSX Manager を使用して管理します。

**オンプレミスで実行される vRealize スイートを使用できますか?** 

特定の統合およびユース ケースは、ケースバイケースで評価されます。

**オンプレミス環境から Azure VMware Solution のプライベート クラウドに vSphere VM を移行できますか?**

はい。 標準クロス vCenter [vMotion の要件](https://kb.vmware.com/s/article/210695)が満たされる場合、VM 移行と vMotion を使用して VM をプライベート クラウドに移動できます。

**オンプレミス環境では、特定のバージョンの vSphere が必要ですか?**

どのクラウド環境にも HCX が付属しているため、vMotion のオンプレミス環境では vSphere 5.5 以降が必要です。

**変更制御プロセスはどのようなものですか?**

サービス自体に加えられた更新は、Microsoft Azure の標準的な変更管理プロセスに従います。 お客様は、ワークロード管理タスクと関連する変更管理プロセスについて責任を持ちます。

**これは Azure VMware Solution by CloudSimple とはどのように違いますか?**

新しい Azure VMware Solution では、Microsoft と VMware の間に直接クラウド プロバイダー パートナーシップがあります。 新しいソリューションは、Microsoft によって完全に設計、構築、サポートされ、VMware によって保証されます。 アーキテクチャ上、ソリューションは Azure 専用インフラストラクチャで実行されている VMware テクノロジ スタックと一貫しています。

**既存の Azure VMware Solution の顧客に対して、このプレビューにはどのような意味がありますか?**

既存の Azure VMware Solution by CloudSimple には変更はありません。 ソリューションは引き続き Azure 上でサポートされます。 Azure VMware Solution は、Microsoft のサービス レベル アグリーメント [(SLA)](https://aka.ms/CSVMwareSLA) によって支えられています。 お客様は、運用環境のワークロードに対してサービスを引き続き使用する必要があります。これは、[Microsoft のサービス使用条件](https://azure.microsoft.com/support/legal/)によって管理される、利用可能なソリューションです。

**Azure VMware Solution by CloudSimple からこの新しいソリューションに移行できますか?**

はい。Azure VMware Solution では、HCX などの使い慣れた VMware ツールを使用した移行がサポートされています。 新しいソリューションへの移行に関心があるお客様は、Microsoft のアカウント チームと協力して、オプションと利用可能なサポートを確認してください。



## <a name="compute-network-and-storage"></a>コンピューティング、ネットワーク、およびストレージ

**使用できるホストの種類は複数ありますか?**

使用できるホストの種類は 1 つだけです。

**各種ホストの CPU の仕様はどのようになっていますか?**

サーバーには、デュアル 18 コア 2.3 GHz Intel CPU が搭載されています。

**各ホストにはどのくらいのメモリがありますか?**

サーバーには 576 GB の RAM が搭載されています。

**各ホストのストレージ容量はどのくらいですか?**

各 ESXi ホストには、2 つの vSAN ディスクグループがあり、15.2 TB の容量階層と 3.2 TB の NVMe キャッシュ階層 (各ディスクグループで 1.6 TB) があります。

**各 ESXi ホストで使用できるネットワーク帯域幅はどれくらいですか?**

各 ESXi ホストは、4 つの 25 Gbps NIC で構成された Azure VMware Solution です。ESXi のシステム トラフィック用に 2 つの NIC がプロビジョニングされ、ワークロードのトラフィック用に 2 つの NIC がプロビジョニングされます。 

**vSAN データストアに保存されるデータは保存時に暗号化されますか?**

はい。すべての vSAN データが、Azure Key Vault に保存されているキーを使用して既定で暗号化されます。

## <a name="hosts-clusters-and-private-clouds"></a>ホスト、クラスター、およびプライベート クラウド

**基盤となるインフラストラクチャは共有されていますか?**

いいえ。プライベート クラウド ホストとクラスターは専用であり、使用前と使用後に安全に消去されます。

**クラスターあたりのホストの最小数と最大数はどれくらいですか?**

クラスターは、3 台から 16 台の ESXi ホストにスケーリングできます。 試用版クラスターは、3 台のホストに制限されます。

**プライベート クラウド クラスターはスケーリングできますか?**

はい。クラスターは、ESXi ホストの最小数と最大数の間でスケーリングできます。 試用版クラスターは、3 台のホストに制限されます。

**試用版クラスターとはどのようなものですか?**

試用版クラスターは、Azure VMware Solution プライベート クラウドの 1 か月間の評価に使用される 3 台のホストのクラスターです。

**試用版クラスターでハイエンド ホストを使用できますか?**

いいえ。 ハイエンド ESXi ホストは、運用クラスターで使用するために予約されています。

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware Solution と VMware ソフトウェア

**プライベート クラウドで使用される VMware ソフトウェアのバージョンは何ですか?**

プライベート クラウドでは、vSphere 6.7、vSAN 6.7、HCX、および NSX-T バージョン 2.5 が使用されます。  

**プライベート クラウドでは VMware NSX は使用されますか?**

はい。Azure VMware Solution のプライベート クラウドでは、ソフトウェア定義のネットワークのために NSX-T 2.5 が使用されます。

**プライベート クラウドで VMware NSX-V は使用できますか?**

いいえ。 NSX-T が NSX のサポートされている唯一のバージョンです。

**オンプレミス環境またはプライベート クラウドに接続するネットワークでは NSX が必要ですか?**

いいえ。オンプレミスでは NSX を使用する必要はありません。

**プライベート クラウドでの VMware ソフトウェアのアップグレードと更新のスケジュールはどのようになりますか?**

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持するために行われます。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。

**プライベート クラウド ソフトウェア スタックはどのくらいの頻度で更新されますか?**

プライベート クラウド ソフトウェアは、VMware からのソフトウェア バンドルのリリースに合わせたスケジュールでアップグレードされます。 プライベート クラウドでは、アップグレードのためのダウンタイムは必要ありません。

## <a name="connectivity"></a>接続

**オンプレミス環境にプライベート クラウドを組み込むには、どのようなネットワーク IP アドレスを計画する必要がありますか?**

Azure VMware Solution のプライベート クラウドをデプロイするには、/22 プライベート ネットワーク アドレス空間が 1 つ必要です。 このプライベート アドレス空間は、サブスクリプションの他の仮想ネットワーク、またはオンプレミス ネットワークと重複しないようにする必要があります。
 
**オンプレミス環境から Azure VMware Solution のプライベート クラウドにはどのように接続しますか?**

次の 2 つの方法のいずれかでサービスに接続できます。 

- ExpressRoute 経由でプライベート クラウドにピアリングされる、Azure 仮想ネットワークにデプロイされた VM またはアプリケーション ゲートウェイを使用する。
- オンプレミスのデータセンターから Azure ExpressRoute 回線への ExpressRoute Global Reach を使用する。

**ワークロード VM をインターネットまたは Azure サービス エンドポイントに接続するにはどうすればよいですか?**

Azure portal で、プライベート クラウドのインターネット接続を有効にします。 NSX-T Manager を使用して、NSX-T T1 ルーターと論理スイッチを作成します。 次に、vCenter を使用して、論理スイッチによって定義されたネットワーク セグメントに VM をデプロイします。 この VM は、インターネットと Azure サービスにネットワーク アクセスできます。

**インターネットからプライベート クラウド内の論理ネットワーク上の VM へのアクセスを制限する必要はありますか?**

いいえ。 インターネットからプライベート クラウドへの直接的なインバウンド ネットワーク トラフィックは許可されません。

**論理ネットワーク上の VM からインターネットへのインターネット アクセスを制限する必要はありますか?**

はい。 NSX-T Manager を使用して、インターネットへの VM のアクセスを制限するファイアウォールを作成する必要があります。

## <a name="accounts-and-privileges"></a>アカウントと特権

**新しい Azure VMware Solution プライベート クラウドでは、どのようなアカウントと特権が取得されますか?**

vCenter の cloudadmin ユーザーの資格情報と、NSX-T Manager の管理者アクセス権が提供されます。 また、Azure Active Directory を組み込むために使用できる CloudAdmin グループもあります。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

**ESXi ホストに管理者アクセスできますか?**

いいえ。ESXi への管理者アクセスは、ソリューションのセキュリティ要件を満たすために制限されています。

**vCenter にはどのような特権とアクセス許可がありますか?**

CloudAdmin グループの特権が付与されます。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

**NSX-T Manager にはどのような特権とアクセス許可がありますか?**

NSX-T に対する完全な管理者特権があり、オンプレミスの NSX-T データセンターと同様に、ロールベースのアクセス制御を管理できます。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

> [!NOTE]
> プライベート クラウドのデプロイの一部として T0 ルーターが作成され構成されます。 この論理ルーターまたは NSX-T エッジ ノード VM を変更すると、プライベート クラウドへの接続に影響する可能性があります。

## <a name="billing-and-support"></a>課金とサポート

**Azure VMware Solution のプレビュー期間中はどのように課金されますか?**

プレビュー期間中の Azure VMware Solution の課金は、1 か月ごとの従量課金制です。 一般提供時に追加のオプションが提供される予定です。

**Azure VMware Solution のプレビュー期間中の価格体系はどのようなものですか?**

価格に関する一般的な質問については、Azure VMware ソリューションの[価格](https://azure.microsoft.com/pricing/details/azure-vmware)に関するページを参照してください。 要請に応じてプレビュー価格をご利用いただけます。アカウント チームにお問い合わせいただくか、価格ページのリンクに従って営業担当者にお問い合わせください。

**Azure VMware Solution のサポート提供元はどこですか?**

Azure VMware Solution のサポートは、Microsoft が提供します。 プレビューのガイドラインにより、サポートが提供されるのは、PST の営業時間で、月曜日から金曜日の午前 9 時から午後 5 時までです。 サポート チケットは[こちらのリンク](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)から送信できます

**Azure VMware Solution のプライベート クラウドを作成するにはどのアカウントが必要ですか?**

Azure サブスクリプションの Azure アカウントが必要です。

<a name="how-to-request-a-quota-increase-for-avs"></a>**Azure VMware Solution に対するホスト クォータの増量はどのようにリクエストすればよいですか?**

クォータの増量は、[サポート リクエストを送信する](..\azure-portal\supportability\how-to-create-azure-support-request.md)ことで要求できます。 クォータ管理チームにより、3 営業日以内に要求が評価されて承認されます。  

> [!IMPORTANT]
> クォータの増量をリクエストする前に、Azure portal で **Microsoft.AVS** リソース プロバイダーを登録してください。  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

1. Azure portal の **[ヘルプとサポート]** で**新しいサポート リクエスト**を作成し、次の情報をチケットに提供します。
   - **問題の種類:** 技術
   - **サブスクリプション:** サブスクリプション ID
   - **サービス:** Azure VMware Solution 
   - **概要:** クォータの増加
   - **問題の種類:** 容量管理に関する問題
   - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの [詳細] タブの [説明] で、次のことを指定します。
   - 追加するノードの数   
   - ノードの SKU
   - リージョン

   > [!NOTE] 
   > 既定では、最低 4 ノードが許可されます。

1. **[確認および作成]** をクリックして要求を送信します。

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
