---
title: FAQ - CloudSimple による VMware ソリューション
description: CloudSimple による Azure VMware ソリューションのよく寄せられる質問
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595342"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple による VMware ソリューションについてよく寄せられる質問

サービスとその使用方法を理解するために役立つ、CloudSimple による Azure VMware ソリューションについてよく寄せられる質問と回答。 これらの質問と回答は、次のカテゴリに分類されます。

* CloudSimple サービス
* 接続
* ネットワーク
* セキュリティ
* Compute
* Storage
* VMware
* Azure の統合
 
## <a name="cloudsimple-service"></a>CloudSimple サービス

**CloudSimple による Azure VMware ソリューションとは何ですか?**

CloudSimple による Azure VMware ソリューションは、数分で VMware ワークロードを変換して Azure 上の専用のプライベート クラウドに拡張します。 このソリューションは、インフラストラクチャをプロビジョニングして管理し、オンプレミスと Azure の間でワークロードを調整します。 アプリはオンプレミスと Azure でまったく同じように実行されるため、アプリの複雑な再設計をしなくても、クラウドの弾力性とサービスのベネフィットを受けられます。 CloudSimple は、オンデマンド プロビジョニング、成長に応じた課金、容量最適化を提供するクラウド使用モデルによって、総保有コストを削減します。 機能、利点、およびシナリオについては、[CloudSimple による Azure VMware ソリューションの概要](cloudsimple-vmware-solutions-overview.md)に関するページを参照してください。

**CloudSimple プライベート クラウドとは何ですか?**

Microsoft Azure インフラストラクチャ (ハードウェアとデータセンターの占有領域) にデプロイされた高パフォーマンスのコンピューティング、ストレージ、およびネットワーク環境で構成される専用のプライベート クラウドを Azure の場所にプロビジョニングします。 このプライベート クラウドは、ネイティブな VMware プラットフォームをサービスとして提供します。 VMware の用語では、各プライベート クラウドには vCenter Server の正確に 1 つのインスタンスが含まれます。 vCenter Server は、1 つ以上の vSphere クラスターに含まれている複数の ESXi ノードを、対応する vSAN ストレージと共に管理します。 CloudSimple サービスには、Azure サブスクリプション内の複数のプライベート クラウドを含めることができます。 プライベート クラウドの詳細については、[プライベート クラウドの概要](cloudsimple-private-cloud.md)に関するページを参照してください。

**CloudSimple サービスはどこで使用できますか?**

CloudSimple は米国東部および米国西部リージョンで利用できます。

**CloudSimple のサブスクリプションを有効にする方法は?**

CloudSimple サービス用にサブスクリプションを有効にするには、Microsoft アカウント担当者 ([azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)) に連絡してください。 CloudSimple サービスを有効にするサブスクリプション ID を電子メールに記入してください。 

**CloudSimple ポータルにアクセスする方法は?**

CloudSimple ポータルには、Azure portal からアクセスします。 CloudSimple ポータルにアクセスする方法については、[Azure portal から CloudSimple による VMware ソリューション ポータルへのアクセス](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)に関するページを参照してください。

**プライベート クラウドの容量を増やすにはどうすればよいですか?**

Azure portal からノードをプロビジョニングし、CloudSimple ポータルからプライベート クラウドを拡張します。 プライベート クラウドは、既存の vSphere クラスターにノードを追加するか、または新しい vSphere クラスターを作成することによって拡張できます。 手順については、「[CloudSimple プライベート クラウドの拡張](https://docs.azure.cloudsimple.com/expand-private-cloud)」を参照してください。

**メンテナンス中にプライベート クラウドはどうなりますか?**

CloudSimple は、スケジュールされたメンテナンスの数日前に定期的な通知を提供します。 プライベート クラウドの可用性を確保するために、メンテナンスは中断のない方法で実行されます。 メンテナンスは次のいずれかの種類になります。

- **CloudSimple インフラストラクチャ**: CloudSimple インフラストラクチャは、高可用性になるように設計されています。 メンテナンス中に、冗長なコンポーネントを影響が出ないように 1 つずつ更新することによって、プライベート クラウドの接続と可用性が確保されます。 プライベート クラウド vCenter、すべての仮想マシン、プライベート クラウドからのインターネット接続、およびオンプレミスまたは Azure への接続にアクセスできます。
- **CloudSimple ポータル**: メンテナンス中に、CloudSimple ポータル上の一部の機能にアクセスできなくなるか、または無効になることがあります。 メンテナンス通知には、ポータル上で何を実行できるかに関する情報が含まれます。

## <a name="connectivity"></a>接続

**CloudSimple リージョン ネットワークへの接続オプションにはどのようなものがありますか?**

CloudSimple では、CloudSimple リージョン ネットワークに接続するための 3 種類の接続オプションを提供しています。 これらの 3 つのオプションをすべて一緒に使用できます。

- オンプレミスのデータセンターから CloudSimple リージョン ネットワークへの Azure ExpressRoute 接続: Global Reach を使用してオンプレミスの ExpressRoute 回線と CloudSimple ExpressRoute 回線をブリッジする、高速で待機時間の短い、セキュリティで保護されたプライベート接続。 接続を設定するには、[ExpressRoute を使用したオンプレミスから CloudSimple への接続](https://docs.azure.cloudsimple.com/on-premises-connection)に関するページを参照してください。
- Azure 仮想ネットワークから CloudSimple リージョン ネットワークへの ExpressRoute 接続: 仮想ネットワーク ゲートウェイを使用して Azure 上の仮想ネットワークと CloudSimple ExpressRoute 回線をブリッジする、高速で待機時間の短い、セキュリティで保護されたプライベート接続。 接続を設定するには、[ExpressRoute を使用した CloudSimple プライベート クラウド環境から Azure 仮想ネットワークへの接続](https://docs.azure.cloudsimple.com/azure-expressroute-connection)に関するページを参照してください。
- オンプレミスのデータセンターから CloudSimple リージョン ネットワークへのサイト間 VPN 接続: オンプレミスの VPN デバイスから CloudSimple プライベート クラウド リージョンへのセキュリティで保護された仮想プライベート ネットワーク。 接続を設定するには、[オンプレミス ネットワークと CloudSimple プライベート クラウドの間の VPN 接続の設定](https://docs.azure.cloudsimple.com/set-up-vpn)に関するページを参照してください。

**プライベート クラウドに接続するにはどうすればよいですか?**

CloudSimple ポータルでは、プライベート クラウドの詳細を表示できます。 プライベート クラウドに対応する vCenter に接続するには、ネットワーク接続がサイト間、ポイント対サイト、または ExpressRoute を使用して確立されていることを確認します。 次に、Azure portal から CloudSimple ポータルを起動します。 ホーム ページまたはプライベート クラウドの詳細ページで **[vSphere Client を起動する]** を選択します。

**ExpressRoute 回線の利点は何ですか?**

Azure ExpressRoute 回線は、高速で待機時間の短い、セキュリティで保護された接続を提供します。 CloudSimple では、顧客ごと、リージョンごとに専用の ExpressRoute 回線が提供されます。 この回線を使用して、オンプレミスおよび Azure サブスクリプションからセキュリティで保護された接続を確立できます。

**CloudSimple へと CloudSimple からの接続のネットワーク コストはどれくらいですか? CloudSimple へと CloudSimple から Azure へのエグレス料金は発生しますか? リージョンにまたがるエグレス料金は発生しますか?**

ネットワーク エグレスに対して料金は発生しません。 仮想ネットワークまたはオンプレミスの ExpressRoute 回線からのすべてのエグレス トラフィックには Azure の標準料金が適用されます。

## <a name="networking"></a>ネットワーク

**プライベート クラウドにはどのようなネットワーク機能を使用できますか?**

VLAN とそのサブネットおよびファイアウォール テーブルをプロビジョニングできます。 パブリック IP アドレスを割り当て、プライベート クラウドで実行される仮想マシンにマップできます。 詳細については、「[VLAN とサブネットの概要](cloudsimple-vlans-subnets.md)」、「[ファイアウォール テーブルの概要](cloudsimple-firewall-tables.md)」、および[パブリック IP アドレスの概要](cloudsimple-public-ip-address.md)に関するページを参照してください。

**プライベート クラウド内のアプリケーションに対して異なるサブネットを設定するにはどうすればよいですか?**

CloudSimple ポータルから、プライベート クラウド上に VLAN を作成できます。 VLAN を作成したら、その VLAN を使用してプライベート クラウド vCenter 上に分散ポート グループを作成し、その分散ポート グループに接続された仮想マシンを作成できます。 VLAN またはサブネットのファイアウォール テーブルを有効にし、ネットワーク トラフィックをセキュリティで保護するためのファイアウォール規則を定義できます。

**プライベート クラウドにはどのようなファイアウォール設定を使用できますか?**

南北および東西のトラフィックのための規則を構成できます。 規則はファイアウォール テーブルで定義されます。 プライベート クラウド上の VLAN にファイアウォール テーブルを接続できます。 セットアップ手順については、[プライベート クラウドのファイアウォール テーブルと規則の設定](https://docs.azure.cloudsimple.com/firewall)に関するページを参照してください。

**プライベート クラウド環境内の VM にパブリック IP アドレスを割り当てることはできますか?**

CloudSimple ポータルでは、新しいパブリック IP アドレスを割り当て、それを仮想マシンまたはアプライアンスのプライベート IP アドレスに関連付ける簡単にできます。 また、新しいファイアウォール規則を作成するか、または既存のファイアウォール規則を適用して、ポータルで特定のポートや IP アドレスの特定のセットからのトラフィックを許可することもできます。 セットアップ手順については、[プライベート クラウド環境へのパブリック IP アドレスの割り当て](https://docs.azure.cloudsimple.com/public-ips)に関するページを参照してください。

## <a name="security"></a>セキュリティ

**CloudSimple にはどのようなセキュリティ オプションがありますか?**

CloudSimple プライベート クラウドは、プライベート クラウド環境をセキュリティで保護するために次のセキュリティ機能を提供します。

- **保存データの暗号化:** プライベート クラウド内の vSAN ストレージ上に存在する保存データを暗号化できます。 vSAN は、Azure 仮想ネットワークまたはオンプレミスの環境にデプロイできる外部のキー管理サーバーをサポートします。 詳細については、[CloudSimple プライベート クラウドのための vSAN 暗号化の構成](https://docs.azure.cloudsimple.com/vsan-encryption)に関するページを参照してください。
- **ネットワークのセキュリティ:** プライベート クラウドからの、インターネットからプライベート クラウドへの、オンプレミスでの、さらにプライベート クラウドのサブネット内でのネットワーク トラフィック フローは、ファイアウォール規則を使用して制御します。
- **セキュリティで保護されたプライベート接続:** オンプレミス ネットワークと Azure サブスクリプションの間のセキュリティで保護されたプライベート接続。

## <a name="compute"></a>Compute

**どのような種類のホストが使用可能ですか?**

CloudSimple は 2 種類のホストを提供します。

* **CS28 ノード**: CPU:2x 2.2 GHz、合計 28 コア、48 HT。 RAM: 256 GB。 ストレージ:1600 GB NVMe キャッシュ、5760 GB データ (オール フラッシュ)。 ネットワーク: 2x25Gbe NIC。
* **CS36 ノード**: CPU 2x 2.3 GHz、合計 36 コア、72 HT。 RAM: 512 GB。 ストレージ:3200 GB NVMe キャッシュ、11,520 GB データ (オール フラッシュ)。 ネットワーク: 2x25Gbe NIC。

**ハードウェア障害はどのように処理されますか?**

すべての CloudSimple インフラストラクチャが、CloudSimple プラットフォームとそのサービス運用チームによって継続的に監視されます。 ハードウェア障害が検出された場合は、新しいノードがプライベート クラウドに追加されます。 プライベート クラウドの高可用性を確保するために、障害が発生したノードは削除されます。

## <a name="storage"></a>Storage

**プライベート クラウドではどのような種類のストレージがサポートされますか?**

CloudSimple は、すべてのプライベート クラウドに**オール フラッシュ VMware vSAN ストレージ**を提供します。 個々の vSphere は、その独自の vSAN データ ストアを使用して作成されます。 詳細については、[プライベート クラウド VMware コンポーネント - vSAN ストレージ](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)に関するページを参照してください。

**データの暗号化はサポートされていますか?**
はい。 プライベート クラウド上の vSAN ストレージを設定して、vSAN 上に格納されているデータの暗号化のためにオンプレミスまたは Azure にデプロイされたキー管理サーバー (KMS) を使用できます。

**障害が発生したディスクはどのように処理されますか?**

CloudSimple の監視では、プライベート クラウドのすべてのハードウェア コンポーネントを継続的に監視します。 ディスク障害が検出されたか、またはヒューリスティックに基づいてディスクが障害発生中として識別された場合は、新しいノードがプライベート クラウドに自動的に追加されます。 障害が発生したか、または障害発生中のディスクを含むノードは、プライベート クラウドから削除されます。

## <a name="vmware"></a>VMware

**アプリケーションおよびデータのオンプレミスからの大規模なアップロードや移行を実行するにはどうすればよいですか?**

CloudSimple は、ネイティブな VMware vSphere ソリューションを提供します。 一括データ移行に使用されるツールはすべて、CloudSimple プライベート クラウドで使用できます。 使用できるオプションには、次のものがあります。

- データの一括移行用の VMware HCX。
- オンプレミスから CloudSimple への Storage vMotion を使用したデータのコールド移行。

**インストールできる VMware ツールはありますか?**

CloudSimple は、ネイティブな VMware vSphere ソリューションを提供します。 オンプレミスの vSphere 環境を管理するために使用されるツールはすべて、CloudSimple で使用できます。 CloudSimple では、VMware ツールをインストールするためのライセンス持ち込み (BYOL) モデルがサポートされます。

**更新とアップグレードはどのように管理されますか?**

CloudSimple は、プライベート クラウドのすべてのインフラストラクチャ コンポーネントをシームレスで、かつ中断のない方法で管理および更新します。 VMware またはインフラストラクチャ ベンダーによってリリースされた更新プログラムまたはセキュリティ修正プログラムはすべて、CloudSimple によって認定されるとすぐに更新のためにスケジュールされます。

CloudSimple は、プライベート クラウドにインストールされているアプリケーションのアップグレードや更新を実行しません。

## <a name="azure-integration"></a>Azure の統合

**どのような Azure サービスがサポートされていますか?**

CloudSimple は、Azure 上のサブスクリプションへの Azure ExpressRoute 接続を提供します。 サブスクリプションで実行されるサービスはすべて、プライベート クラウドへのネットワーク接続を備え、プライベート クラウドに接続できます。 次に例を示します。

- **Azure Active Directory**: CloudSimple vCenter の ID ソースとして Azure Active Directory を使用します。
- **Azure Storage**:プライベート クラウドからのバックアップ、イメージ、その他のデータを格納するために Storage を使用します。
- **ハイブリッド アプリケーション**: パブリックおよびプライベート クラウドにまたがるアプリケーション アーキテクチャを作成できます。 たとえば、CloudSimple プライベート クラウド上のアプリケーションおよびデータベース サーバーにアクセスする Azure の Web サーバーを作成できます。
- **Azure Monitor** および **Azure Security Center**: VMware 上で実行されるワークロードは、ログ記録、パフォーマンス メトリック、セキュリティ管理のために Monitor および Security Center を使用できます。

**VMware テナントを Azure にマップする方法は?**

CloudSimple は、Azure portal からプライベート クラウド上の VMware VM を管理するための固有の機能を提供します。 必要なリソース制約が構成された vCenter リソース プールは、グローバル管理者によってサブスクリプションにマップできます。 

**Azure ではどのようなライセンス特典を受けられますか?**

CloudSimple では、Azure ハイブリッド特典を利用してライセンスに関して最大 90% を節約することにより、Microsoft ライセンスへの投資を維持し、他のクラウドと比較して総保有コストを削減できます。 また、Windows Server 2008 および Microsoft SQL Server 2008 用の延長されたセキュリティ修正プログラムも入手できます。 Veeam や Zerto などの一般的なアプリのためのクラウドへの BYOL によって低コストを維持してください。 
