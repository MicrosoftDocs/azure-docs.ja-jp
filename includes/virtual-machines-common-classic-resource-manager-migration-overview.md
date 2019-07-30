---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: de2e33ceb182383d9529bfe41afffbbf28e1e493
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671293"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行
この記事では、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイ モデルから Resource Manager デプロイ モデルに移行する方法と、サブスクライブ内で共存する 2 つのデプロイ モデルから仮想ネットワークのサイト間ゲートウェイを使用してリソースに接続する方法の詳細を説明します。 [Azure Resource Manager の機能と利点](../articles/azure-resource-manager/resource-group-overview.md)の詳細を参照してください。 

## <a name="goal-for-migration"></a>移行の目的
Resource Manager では、テンプレートを使用して複雑なアプリケーションをデプロイできます。また、VM の拡張機能を使用して仮想マシンを構成し、アクセス管理とタグ付けを統合します。 Azure Resource Manager には、仮想マシンの可用性セットへのスケーラブルな並列デプロイも含まれます。 さらに、新しいモデルでは、計算、ネットワーク、ストレージの個別のライフサイクル管理が提供されます。 最後に、仮想ネットワークでの仮想マシンの実行によって、セキュリティが既定で有効になることが重要視されています。

機能面について言うと、クラシック デプロイ モデルの計算、ネットワーク、およびストレージ機能のほとんどが、Azure Resource Manager でもサポートされています。 Azure Resource Manager の新機能を活用するには、クラシック デプロイ モデルから既存のデプロイを移行します。

## <a name="supported-resources-for-migration"></a>移行がサポートされているリソース
次のクラシック IaaS リソースは、移行中にサポートされます。

* Virtual Machines
* 可用性セット
* Cloud Services と Virtual Machines
* ストレージ アカウント
* 仮想ネットワーク
* VPN ゲートウェイ
* ExpressRoute ゲートウェイ _(仮想ネットワークと同じサブスクリプション内にある場合のみ)_
* ネットワーク セキュリティ グループ
* ルート テーブル
* 予約済み IP

## <a name="supported-scopes-of-migration"></a>移行のサポート対象範囲
コンピューティング リソース、ネットワーク リソース、ストレージ リソースの移行を完了するには、4 つの方法があります。

* [仮想マシンの移行 (仮想ネットワーク外)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [仮想マシンの移行 (仮想ネットワーク内)](#migration-of-virtual-machines-in-a-virtual-network)
* [ストレージ アカウントの移行](#migration-of-storage-accounts)
* [接続されていないリソースの移行](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>仮想マシンの移行 (仮想ネットワーク外)
Resource Manager デプロイ モデルでは、既定でアプリケーションのセキュリティが適用されます。 すべての VM が Resource Manager モデルの仮想ネットワーク内にある必要があります。 Azure Platform は移行の一環として、仮想マシンを再起動 (`Stop`、`Deallocate`、および `Start`) します。 仮想マシンの移行先となる仮想ネットワークには、2 つのオプションがあります。

* まず、新しい仮想ネットワークを作成し、仮想マシンをその新しい仮想ネットワークに移行するようにプラットフォームに要求できます。
* また、Resource Manager の既存の仮想ネットワークに仮想マシンを移行することもできます。

> [!NOTE]
> この移行範囲では、移行中のある期間、管理プレーンおよびデータ プレーンのいずれの操作も許可されない場合があります。
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>仮想マシンの移行 (仮想ネットワーク内)
ほとんどの VM 構成では、クラシック デプロイ モデルと Resource Manager デプロイ モデルの間でメタデータのみが移行されます。 基になる VM は、同じネットワーク内の同じストレージを使用する同じハードウェアで実行されます。 移行中の一定期間は、管理プレーン操作が許可されない場合があります。 ただし、データ プレーンは引き続き機能します。 つまり、移行中は VM (クラシック) 上で実行されているアプリケーションでダウンタイムが発生することはありません。

次の構成は現在サポートされていません。 今後、これらのサポートが追加されたときに、この構成の一部の VM でダウンタイムが発生する場合があります (VM の動作の停止、割り当て解除、再起動が行われます)。

* 単一のクラウド サービスに複数の可用性セットがある。
* 単一のクラウド サービスに 1 つ以上の可用性セットと、可用性セットに存在しない VM がある。

> [!NOTE]
> 単一のクラウド サービスに 1 つ以上の可用性セットと、可用性セットに属さない VM がある 上記の特定の構成の場合は、データ プレーン ダウンタイムが発生します。
>

### <a name="migration-of-storage-accounts"></a>ストレージ アカウントの移行
シームレスに移行できるように、クラシック ストレージ アカウントで Resource Manager VM をデプロイできます。 この機能により、ストレージ アカウントとは関係なく、コンピューティングおよびネットワーク リソースを移行できます。また、移行する必要があります。 仮想マシンおよび仮想ネットワークを移行したら、ストレージ アカウントを移行して、移行プロセスを完了する必要があります。

ストレージ アカウントに関連付けられたディスクまたは Virtual Machines データが存在せず、BLOB、ファイル、テーブル、およびキューのみが存在する場合、Azure Resource Manager への移行は、依存関係のないスタンドアロン移行として実行できます。

> [!NOTE]
> Resource Manager デプロイ モデルには、従来のイメージおよびディスクという概念がありません。 クラシック イメージやディスクは、ストレージ アカウントを移行すると Resource Manager スタックには表示されなくなりますが、バッキング VHD はストレージ アカウントに残ります。

次のスクリーンショットは、Azure portal を使用してクラシック ストレージ アカウントを Azure Resource Manager ストレージ アカウントにアップグレードする方法を示しています。
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ストレージ アカウントに移動します。
3. **[設定]** セクションで、 **[ARM への移行]** をクリックします。
4. **[検証]** をクリックして、移行が可能かどうかを確かめます。
5. 検証に合格したら、 **[準備]** をクリックして移行済みのストレージ アカウントを作成します。
6. **yes** と入力して移行を確定し、 **[コミット]** をクリックして移行を完了します。

    ![ストレージ アカウントの検証](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![ストレージ アカウントの準備](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![ストレージ アカウント移行の確定](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>接続されていないリソースの移行
関連付けられたディスクまたは Virtual Machines データがないストレージ アカウントは、個別に移行できます。

仮想マシンと仮想ネットワークに接続されていないネットワーク セキュリティ グループ、ルート テーブル、および予約済み IP も、個別に移行できます。

<br>

## <a name="unsupported-features-and-configurations"></a>サポートされていない機能と構成
一部の機能と構成は、現在サポートされていません。次のセクションで、推奨事項について説明します。

### <a name="unsupported-features"></a>サポートされていない機能
次の機能は現在サポートされていません。 必要に応じて、これらの設定を削除し、VM を移行してから、Resource Manager デプロイ モデルでその設定を再度有効にすることができます。

| リソース プロバイダー | 機能 | 推奨 |
| --- | --- | --- |
| Compute | 関連付けのない仮想マシン ディスク。 | これらのディスクの背後にある VHD BLOB は、ストレージ アカウントの移行時に移行されます。 |
| Compute | 仮想マシン イメージ。 | これらのディスクの背後にある VHD BLOB は、ストレージ アカウントの移行時に移行されます。 |
| ネットワーク | エンドポイント ACL。 | エンドポイント ACL を削除して移行をやり直します。 |
| ネットワーク | Application Gateway | 移行を開始する前に Application Gateway を削除し、移行の完了後に Application Gateway を作成し直します。 |
| ネットワーク | VNet ピアリングを使用した仮想ネットワーク (VNet から ARM に移行した後、ピアリング)。 | 仮想ネットワークを Resource Manager に移行してからピアリングします。 詳細については、[VNet ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)に関するページを参照してください。 |

### <a name="unsupported-configurations"></a>サポートされていない構成
次の構成は現在サポートされていません。

| Service | 構成 | 推奨 |
| --- | --- | --- |
| リソース マネージャー |クラシック リソースのロールベースのアクセス制御 (RBAC) |リソースの URI は移行後に変更されるため、移行後に必要になる RBAC ポリシーの更新を計画しておくことをお勧めします。 |
| Compute |VM に関連付けられている複数のサブネット |1 つのサブネットのみを参照するようにサブネット構成を更新します。 この操作には、セカンダリ NIC (別のサブネットを参照している) を VM から削除し、移行の完了後に再接続することが必要な場合があります。 |
| Compute |仮想ネットワークに属しているが、明示的なサブネットが割り当てられていない仮想マシン |必要に応じて VM を削除することができます。 |
| Compute |アラートの自動スケール ポリシーが適用されている仮想マシン |移行を実行すると、これらの設定は削除されます。 したがって、移行を行う前に環境を評価することを強くお勧めします。 移行の完了後に、アラート設定を再構成することもできます。 |
| Compute |XML VM 拡張機能 (BGInfo 1.*、Visual Studio デバッガー、Web デプロイ、リモート デバッグ) |これはサポートされていません。 移行を続行するために、仮想マシンからこれらの拡張機能を削除することをお勧めします。削除していない場合、移行プロセスで自動的に削除されます。 |
| Compute |Premium storage を使用したブート診断 |VM のブート診断機能を無効にしてから移行を続行してください。 移行が完了した後に、Resource Manager スタックでブート診断を再び有効にできます。 さらに、スクリーン ショットとシリアル ログに使用されている BLOB を削除する必要があるため、これらの BLOB に対して課金されることはなくなります。 |
| Compute | Web/worker ロールを含む Cloud Services | 現在これはサポートされていません。 |
| Compute | 2 つ以上の可用性セット (つまり、複数の可用性セット) を含むクラウド サービス。 |現在これはサポートされていません。 移行前に同じ可用性セットに Virtual Machines を移動してください。 |
| Compute | Azure Security Center の拡張機能を備えた VM | Azure Security Center では、セキュリティを監視し、アラートを生成するために、仮想マシンに拡張機能を自動的にインストールします。 サブスクリプションで Azure Security Center のポリシーが有効になっている場合、通常はこれらの拡張機能が自動的にインストールされます。 Virtual Machines を移行するには、サブスクリプションでセキュリティ センター ポリシーを無効にします。これにより、Virtual Machines から Security Center の監視拡張機能が削除されます。 |
| Compute | バックアップまたはスナップショットの拡張機能を備えた VM | これらの拡張機能は、Azure Backup サービスで構成された Virtual Machines にインストールされます。 これらの VM の移行がサポートされていない間は、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault)のガイダンスに従って、移行前に作成されたバックアップを保持してください。  |
| ネットワーク |仮想マシンと Web/worker ロールを含む仮想ネットワーク |現在これはサポートされていません。 移行する前に、Web/ワーカー ロールを独自の仮想ネットワークに移動してください。 従来の仮想ネットワークが移行されると、それ以降、移行された Azure Resource Manager 仮想ネットワークは従来の仮想ネットワークを使ってピアリングされ、以前と同様の構成を実現できます。|
| ネットワーク | クラシック Express Route 回線 |現在これはサポートされていません。 これらの回線は、IaaS 移行を開始する前に、Azure Resource Manager に移行する必要があります。 詳細については、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](../articles/expressroute/expressroute-move.md)」をご覧ください。|
| Azure App Service |App Service 環境を含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure HDInsight |HDInsight サービスを含む仮想ネットワーク |現在これはサポートされていません。 |
| Microsoft Dynamics Lifecycle Services |Dynamics Lifecycle Services によって管理される仮想マシンを含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure AD Domain Services |Azure AD ドメイン サービスを含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure API Management |Azure API Management デプロイを含む仮想ネットワーク |現在これはサポートされていません。 IaaS VNET を移行するには、API Management デプロイの VNET を変更します。この操作では停止時間は発生しません。 |
