---
title: FAQ - Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple についてよく寄せられる質問には、接続性、ネットワーク、セキュリティ、ストレージ、Azure 統合などがあります。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895565"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>VMware Solution by CloudSimple についてよく寄せられる質問

## <a name="cloudsimple-service"></a>CloudSimple サービス

**Azure VMware Solution by CloudSimple とは何ですか?**

Azure VMware Solution by CloudSimple は、数分で VMware ワークロードを変換して Azure 上の専用のプライベート クラウドに拡張します。 CloudSimple では、プロビジョニング、インフラストラクチャの管理、そしてオンプレミスと Azure の間でのワークロードの調整を任せることができます。 アプリはオンプレミスと Azure でまったく同じように実行されるため、アプリの複雑な再設計をしなくても、クラウドの弾力性とサービスのベネフィットを受けられます。 CloudSimple は、オンデマンド プロビジョニング、成長に応じた課金、容量最適化を提供するクラウド使用モデルによって、総保有コストを削減します。  機能、利点、シナリオについては、「[CloudSimple による Azure VMware ソリューションとは](cloudsimple-vmware-solutions-overview.md)」を参照してください。

**CloudSimple プライベート クラウドとは?**

CloudSimple プライベート クラウドは個人の専用クラウドであり、Azure の場所にある Microsoft Azure インフラストラクチャ (ハードウェアとデータセンターの占有領域) にデプロイされた高パフォーマンスのコンピューティング、ストレージ、ネットワーク環境で構成されます。  プライベート クラウドは、ネイティブ VMware "サービスとしてのプラットフォーム" を提供します。 VMware の用語では、各プライベート クラウドには vCenter Server のインスタンスが 1 つだけ含まれます。 vCenter Server は、1 つ以上の vSphere クラスターに含まれる複数の ESXi ノードを、対応する仮想 SAN (vSAN) ストレージと共に管理します。 CloudSimple サービスには、Azure サブスクリプション内の複数のプライベート クラウドを含めることができます。  詳細については、[プライベート クラウドの概要](cloudsimple-private-cloud.md)に関するページを参照してください。

**CloudSimple サービスが使用可能な場所は?**

CloudSimple は米国東部、米国西部、西ヨーロッパ リージョンで利用できます。近日中にリージョンが追加される予定です。

**CloudSimple のサブスクリプションを有効にする方法は?**

Microsoft アカウント担当者 ([azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)) に連絡すれば、CloudSimple サービス用にサブスクリプションを有効にできます。 CloudSimple サービスを有効にするサブスクリプション ID を電子メールに記入してください。  

**CloudSimple ポータルにアクセスする方法は?**

CloudSimple ポータルには、Azure portal からアクセスします。  詳細については、「[Azure portal から VMware Solution by CloudSimple にアクセスする](access-cloudsimple-portal.md)」を参照してください。

**プライベート クラウドの容量を増やす方法は?**

容量を増やすには、Azure portal から追加ノードを購入し、そのノードを使用して CloudSimple ポータルからプライベート クラウドを拡張します。  既存の vSphere クラスターか新しい vSphere クラスターにノードを追加できます。  詳細については、「[CloudSimple プライベート クラウドの拡張](expand-private-cloud.md)」を参照してください。

**メンテナンス中、プライベート クラウドはどうなりますか?**

CloudSimple からは、予定メンテナンス期間の数日前に通知が届きます。  プライベート クラウドの可用性を確保するために、メンテナンスは中断を伴わない方法で行われます。  メンテナンスは次のいずれかの種類になります。

* **CloudSimple インフラストラクチャ**。  CloudSimple インフラストラクチャは、高可用性となるように設計されています。  この種類のメンテナンス期間中、サービスの中断を回避するために、冗長コンポーネントが一度に 1 つずつ更新されます。 プライベート クラウド vCenter、すべての仮想マシン、プライベート クラウドからのインターネット接続、オンプレミスまたは Azure へのアクセスを管理できます。
* **CloudSimple ポータル**。 この種類のメンテナンス期間中、CloudSimple ポータルの一部の機能が無効になっているか、アクセスできない可能性があります。  メンテナンス期間に先立つ通知には、メンテナンス中の機能制限についての詳細が含まれます。

## <a name="connectivity"></a>接続

**CloudSimple リージョン ネットワークへの接続オプションにはどのようなものがありますか。**

CloudSimple では、CloudSimple リージョン ネットワークに接続するため次の接続オプションを提供しています。 複数のオプションを同時に使用できます。

* **オンプレミスのデータセンターから CloudSimple リージョン ネットワークへの ExpressRoute 接続**。 これは Global Reach を使用してオンプレミスの ExpressRoute 回線と CloudSimple ExpressRoute 回線をブリッジする、高速で待機時間の短い、セキュリティで保護されたプライベート接続です。 接続の設定については、[ExpressRoute を使用してオンプレミスから CloudSimple に接続する](on-premises-connection.md)方法に関するページを参照してください。
* **Azure 仮想ネットワークから CloudSimple リージョン ネットワークへの ExpressRoute 接続**。 これは仮想ネットワーク ゲートウェイを使用して Azure 上の仮想ネットワークと CloudSimple ExpressRoute 回線をブリッジする、高速で待機時間の短い、セキュリティで保護されたプライベート接続です。 接続の設定については、[ExpressRoute を使用して CloudSimple プライベート クラウド環境を Azure 仮想ネットワークに接続する](azure-expressroute-connection.md)方法に関するページを参照してください。
* **オンプレミスのデータセンターから CloudSimple リージョン ネットワークへのサイト間 VPN 接続**。 これはオンプレミスの VPN デバイスから CloudSimple プライベート クラウド リージョンへのセキュリティで保護された仮想プライベート ネットワークです。  詳細については、「[CloudSimple ネットワーク上の VPN ゲートウェイを設定する](vpn-gateway.md)」を参照してください。

**プライベート クラウドに接続する方法は?**

CloudSimple ポータルでは、プライベート クラウドの詳細を表示できます。 プライベート クラウドに対応する vCenter に接続するには、ネットワーク接続がサイト間 VPN、ポイント対サイト VPN、または ExpressRoute を使用して確立されていることをまず確認します。 次に、Azure portal から CloudSimple ポータルを起動し、ホーム ページまたはプライベート クラウドの詳細ページで **[vSphere Client を起動する]** をクリックします。

**ExpressRoute 回線の利点は何ですか?**

Azure ExpressRoute 回線は、高速で待機時間の短い、セキュリティで保護された接続です。  CloudSimple では、顧客ごと、リージョンごとに専用の ExpressRoute 回線が提供されます。  この回線を使用して、セキュリティで保護された接続をオンプレミスまたは Azure サブスクリプションから確立できます。

**CloudSimple へのネットワーク接続のコストはどのくらいですか。CloudSimple と Azure の間、またはリージョン間ではエグレス料金が適用されますか。**

CloudSimple のネットワーク エグレスは課金されません。  仮想ネットワークまたはオンプレミスの ExpressRoute 回線からのすべてのエグレス トラフィックには Azure の標準料金が適用されます。

## <a name="networking"></a>ネットワーク

**プライベート クラウドのためにどのようなネットワーク機能が利用できますか?**

VLAN (とそのサブネット) とファイアウォール テーブルをプロビジョニングし、プライベート クラウドで稼働している仮想マシンにマップされるパブリック IP アドレスを割り当てることができます。 ネットワーク機能の詳細については、「[VLAN とサブネットの概要](cloudsimple-vlans-subnets.md)」、「[ファイアウォール テーブルの概要](cloudsimple-firewall-tables.md)」、[パブリック IP アドレスの概要](cloudsimple-public-ip-address.md)に関するページを参照してください。

**プライベート クラウド内のアプリケーションに対して、異なるサブネットをセットアップする方法は?**

CloudSimple ポータルから、プライベート クラウド上に VLAN を作成します。  VLAN を作成したら、その VLAN を使用してプライベート クラウド vCenter 上に分散ポート グループを作成し、その分散ポート グループに接続された仮想マシンを作成できます。  VLAN/サブネットのファイアウォール テーブルを有効にして、ネットワーク トラフィックをセキュリティで保護するためのファイアウォール規則を定義できます。

**プライベート クラウドに対してどのようなファイアウォール設定が利用できますか?**

南北および東西のトラフィックのための規則を構成できます。  規則はファイアウォール テーブルで定義されます。  プライベート クラウド上の VLAN にファイアウォール テーブルを接続できます。  詳細については、「[プライベート クラウドのファイアウォールのテーブルとルールの設定](firewall.md)」を参照してください。

**プライベート クラウド環境内の VM にパブリック IP アドレスを割り当てることはできますか?**

CloudSimple ポータルでは、新しいパブリック IP アドレスを割り当て、それを仮想マシンまたはアプライアンスのプライベート IP アドレスに関連付けることができます。  ポータルで、新しいファイアウォール規則を作成または既存のファイアウォール規則を適用して、特定のポートや IP アドレスからのトラフィックを許可することもできます。 詳細については、「[プライベート クラウド環境のためにパブリック IP アドレスを割り当てる](public-ips.md)」を参照してください。

## <a name="security"></a>Security

**CloudSimple にはどのようなセキュリティ オプションがありますか?**

CloudSimple は、プライベート クラウド環境をセキュリティで保護するために、次のセキュリティ機能を提供します。

* **保存データの暗号化**。 プライベート クラウド内の vSAN ストレージ上にある保存データを暗号化できます。 vSAN は、Azure vNet またはオンプレミス環境にデプロイできる外部のキー管理サーバーをサポートします。  詳細については、[CloudSimple プライベート クラウドでの vSAN 暗号化の構成](vsan-encryption.md)に関する記事を参照してください。
* **ネットワークのセキュリティ**。 プライベート クラウドとインターネットの間、プライベート クラウドとオンプレミス環境の間、あるいはプライベート クラウドのサブネット内で適用されるファイアウォール規則でネットワーク トラフィックの流れを制御します。
* **セキュリティで保護されたプライベート接続**。 セキュリティで保護されたプライベート接続がオンプレミス ネットワークと Azure サブスクリプションの間で確立されます。

## <a name="compute"></a>Compute

**どのような種類のホストが使用可能ですか?**

CloudSimple は次の種類のホストを提供します。

* **CS28 ノード**:CPU:2x 2.2 GHz、合計 28 コア、48 HT。  RAM: 256 GB。  ストレージ:1600 GB NVMe キャッシュ、5760 GB データ (オールフラッシュ)。 [ネットワーク]\: 4x25Gbe NIC
* **CS36 ノード:** CPU 2x 2.3 GHz、合計 36 コア、72 HT。  RAM: 512 GB。  ストレージ:3200 GB NVMe キャッシュ、11520 GB データ (オール フラッシュ)。  [ネットワーク]\: 4x25Gbe NIC
* **CS36m ノード:** CPU 2x 2.3 GHz、合計 36 コア、72 HT。  RAM: 576 GB。  ストレージ:3200 GB NVMe キャッシュ、13360 GB データ (オール フラッシュ)。  [ネットワーク]\: 4x25Gbe NIC

**ハードウェア障害はどのように処理されますか?**

すべての CloudSimple インフラストラクチャは、CloudSimple プラットフォームと、当社のサービス運用チームによって継続的に監視されています。  ハードウェア障害が検出された場合、新しいノードがプライベート クラウドに追加され、問題のあるノードが削除されます。

## <a name="storage"></a>ストレージ

**プライベート クラウドではどのような種類のストレージがサポートされますか?**

CloudSimple は、すべてのプライベート クラウドにオール フラッシュ VMware vSAN ストレージを提供します。  個々の vSphere は、その独自の vSAN データ ストアを使用して作成されます。  詳細については、[プライベート クラウド VMware コンポーネント - vSAN ストレージ](vmware-components.md#vsan-storage)に関するページを参照してください。

**データの暗号化はサポートされていますか?**
はい。  プライベート クラウド上の vSAN ストレージを設定して、vSAN 上に格納されているデータの暗号化のためにオンプレミスまたは Azure にデプロイされたキー管理サーバー (KMS) を使用できます。

**障害が発生したディスクはどのように処理されますか?**

CloudSimple では、プライベート クラウドのすべてのハードウェア コンポーネントを継続的に監視します。  ディスク障害が検出されたか、またはヒューリスティックに基づいてディスクが障害発生中として識別された場合は、新しいノードがプライベート クラウドに自動的に追加されます。  障害が発生したか、障害発生中のディスクを含むノードは、プライベート クラウドから削除されます。

## <a name="vmware"></a>VMware

**オンプレミスから大量のアプリケーションやデータをアップロードしたり、移行したりするにはどうすればよいですか?**

CloudSimple は、ネイティブな VMware vSphere ソリューションを提供します。  一括データ移行用の VMWare ツールはすべて、プライベート クラウドで使用できます。  次のオプションがあります。

* データの一括移行用の VMware HCX。
* Storage vMotion を使用して、オンプレミスから CloudSimple にデータをコールド移行します。

**インストールできる VMware ツールはありますか?**

CloudSimple は、ネイティブな VMware vSphere ソリューションを提供します。  オンプレミス vSphere 環境を管理するために使用される VMware ツールはすべて CloudSimple で使用できます。  CloudSimple では、VMware ツールをインストールするためのライセンス持ち込み (BYOL) モデルがサポートされます。

**更新とアップグレードはどのように管理されますか?**

CloudSimple は、プライベート クラウドのすべてのインフラストラクチャ コンポーネントを、シームレスで中断のない方法で管理および更新します。  VMware またはインフラストラクチャ ベンダーによってリリースされた更新プログラムとセキュリティ パッチはすべて、CloudSimple による認定を受け次第、更新がスケジュールされます。

プライベート クラウドにインストールされているアプリケーションのアップグレードまたは更新を CloudSimple が実行することはありません。

## <a name="azure-integration"></a>Azure の統合

**どのような Azure サービスがサポートされていますか?**

CloudSimple は、Azure 上のサブスクリプションへの Azure ExpressRoute 接続を提供します。  サブスクリプションで実行されているサービスはすべて、プライベート クラウドに接続できます。  たとえば、次のようになります。

* CloudSimple vCenter の ID ソースとしての **Azure Active Directory**。
* プライベート クラウドのバックアップ、イメージ、その他データを保存するための **Azure Storage**。
* パブリック クラウドとプライベート クラウドにまたがるアプリケーション アーキテクチャを持つ **ハイブリッド アプリケーション**。  たとえば、プライベート クラウド上のアプリケーション サーバーやデータベース サーバーにアクセスする Web サーバーを Azure に作成できます。
* VMware 上で実行されているワークロードを対象とする **Azure Monitor** と **Azure Security Center**。ログ記録、パフォーマンス メトリック、セキュリティ管理を支援します。

**VMware テナントを Azure にマップする方法は?**

CloudSimple は、Azure portal からプライベート クラウド上の VMware VM を管理するための固有の機能を提供します。  必要なリソース制約を構成した vCenter リソース プールは、グローバル管理者がサブスクリプションにマップできます。  

**Azure ではどのようなライセンス特典を受けられますか?**

CloudSimple を利用するとき、Azure ハイブリッド使用特典を活用し、ライセンスで最大 90% 節約できます。 この特典は Microsoft ライセンスへの投資を守り、他のクラウド ソリューションに比べて TCO を下げるものです。 また、Windows Server 2008 および Microsoft SQL Server 2008 用の延長されたセキュリティ修正プログラムも入手できます。  ライセンス持ち込み (BYOL) モデルを使用すると、Veeam や Zerto などの一般的なアプリのコストを抑えることができます。  
