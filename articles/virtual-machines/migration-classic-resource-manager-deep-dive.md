---
title: プラットフォームでサポートされている移行ツール
description: プラットフォームでサポートされている、クラシック デプロイ モデルから Azure Resource Manager へのリソースの移行に関する技術的な詳細。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: 43473b734968b25ca90bd8809c8291625011d46f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667008"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨について[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](./classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

Azure クラシック デプロイ モデルから、Azure Resource Manager デプロイ モデルへの移行を詳しく見ていきましょう。 Azure Platform 上の 2 つのデプロイメント モデルの間で、どのようにリソースが移行されるかを理解できるように、リソースと機能レベルでリソースについて説明していきます。 詳細については、次のサービス告知記事を参照してください。「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md)」。


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>クラシック デプロイ モデルから Azure Resource Manager への IaaS リソースの移行
最初に、サービスとしてのインフラストラクチャ (IaaS) リソースにおけるデータ プレーン操作と管理プレーン操作の違いについて理解することが重要です。

* "*管理/コントロール プレーン*" は、リソースを変更するための管理/コントロール プレーンまたは API を指します。 たとえば、VM の作成、VM の再起動、新しいサブネットでの仮想ネットワークの更新などの操作は、実行中のリソースを管理しますが、 VM への接続には直接影響しません。
* "*データ プレーン*" (アプリケーション) はアプリケーション自体のランタイムを指します。ここには、Azure API を経由しないインスタンスとのやり取りが含まれます。 たとえば、Web サイトへのアクセスや実行中の SQL Server インスタンスまたは MongoDB サーバーからのデータのプルは、データ プレーンまたはアプリケーションによるやり取りです。 その他の例としては、ストレージ アカウントから BLOB をコピーすることや、パブリック IP アドレスにアクセスしてリモート デスクトップ プロトコル (RDP) または Secure Shell (SSH) で仮想マシンに接続することが挙げられます。 これらの操作の場合、計算、ネットワーク、およびストレージでアプリケーションは実行されたままになります。

クラシック デプロイ モデル スタックと Resource Manager スタックとで、データ プレーンに違いはありません。 両者の違いは、移行プロセス時にクラシック デプロイ モデルのリソースが Resource Manager スタックにおける表現に自動で変換される点にあります。 したがって、Resource Manager スタックのリソースは、新しいツール、API、SDK を使って管理する必要があります。

![管理/コントロール プレーンとデータ プレーンの違いを示す図](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> 一部の移行シナリオでは、Azure Platform が仮想マシンを停止および割り当て解除してから再起動します。 その結果、短時間のデータ プレーン ダウンタイムが発生します。
>

## <a name="the-migration-experience"></a>移行エクスペリエンス
移行を開始する前に:

* 移行するリソースで、サポートされていない機能や構成が使用されていないことを確認します。 通常プラットフォームでは、これらの問題が検出され、エラーが生成されます。
* 仮想ネットワーク内にない VM は準備操作の一環として停止され、割り当てが解除されます。 パブリック IP アドレスが失われないようにする場合は、準備操作をトリガーする前に IP アドレスの予約を検討してください。 仮想ネットワーク内にある VM は停止されず、割り当ては解除されません。
* 移行中に発生する可能性のある予期しないエラーに備え、営業時間外での移行を計画します。
* 準備手順の完了後に検証しやすいように、PowerShell、コマンド ライン インターフェイス (CLI) コマンド、または REST API を使用して、VM の現在の構成をダウンロードします。
* 移行を開始する前に、Resource Manager デプロイ モデルを処理する自動化スクリプトと運用化スクリプトを更新します。 必要に応じて、リソースの準備ができた状態で GET 操作を実行できます。
* クラシック デプロイ モデルの IaaS リソースで構成されている Azure ロールベースのアクセス制御 (Azure RBAC) ポリシーを評価し、移行が完了したら計画します。

移行のワークフローを次に示します。

![移行のワークフローを示す図](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> 以降のセクションで説明する操作はすべてべき等です。 サポートされていない機能や構成エラー以外の問題が発生した場合、準備、中止、またはコミットの操作を再試行してください。 Azure でもう一度アクションが試行されます。
>
>

### <a name="validate"></a>検証
検証操作は、移行プロセスの最初の手順です。 この手順の目的は、クラシック デプロイ モデルから移行するリソースの状態を分析することです。 この操作では、リソースを移行できるかどうかが評価されます (成功または失敗)。

移行を検証する仮想ネットワークまたはクラウド サービス (仮想ネットワークでない場合) を選択します。 リソースを移行できない場合、その理由が Azure によって一覧表示されます。

#### <a name="checks-not-done-in-the-validate-operation"></a>検証操作で実施されないチェック

検証操作で分析されるのは、クラシック デプロイ モデルにおけるリソースの状態に限られます。 クラシック デプロイ モデルの各種構成に起因する障害やサポート対象外のシナリオについてはすべてチェックすることができます。 一方、移行中に Azure Resource Manager スタックが原因で起こりうるリソースの問題については一切チェックすることができません。 これらの問題は、移行に続く手順 (準備操作) でそれらのリソースに変換処理が適用されるときにだけチェックされます。 次の表は、検証操作でチェックされないすべての問題を一覧にしたものです。


|検証操作で実施されないネットワーク チェック|
|-|
|仮想ネットワークに ER ゲートウェイと VPN ゲートウェイの両方が割り当てられている。|
|仮想ネットワーク ゲートウェイ接続が切断状態。|
|Azure Resource Manager スタックにすべての ER 回線が事前に移行されている。|
|Azure Resource Manager のネットワーク リソースのクォータ チェック (例: 静的パブリック IP、動的パブリック IP、ロード バランサー、ネットワーク セキュリティ グループ、ルート テーブル、ネットワーク インターフェイス)。 |
| すべてのロード バランサー規則がデプロイと仮想ネットワークにわたって有効である。 |
| 同じ仮想ネットワーク内にある停止済み (割り当て解除済み) VM 間のプライベート IP の競合。 |

### <a name="prepare"></a>準備
準備操作は、移行プロセスの 2 番目の手順です。 この手順の目的は、クラシック デプロイ モデルから Resource Manager リソースへの IaaS リソースの変換をシミュレートすることです。 さらに、準備操作ではこれをサイドバイサイドで視覚的に示します。

> [!NOTE] 
> この手順では、クラシック デプロイ モデルのリソースは変更されません。 この手順で安全に移行を試すことができます。 

移行を準備する仮想ネットワークまたはクラウド サービス (仮想ネットワークでない場合) を選択します。

* リソースを移行できない場合は、移行プロセスが停止され、準備操作が失敗した理由が一覧表示されます。
* リソースを移行できる場合は、移行対象のリソースに対する管理プレーン操作がロックされます。 たとえば、移行中、VM にデータ ディスクを追加することはできません。

次に、リソースを移行するために、クラシック デプロイ モデルから Resource Manager へのメタデータの移行が開始されます。

準備操作が完了すると、クラシック デプロイ モデルと Resource Manager の両方でリソースを視覚化するためのオプションが表示されます。 クラシック デプロイ モデルのすべてのクラウド サービスに対して、`cloud-service-name>-Migrated`というパターンのリソース グループ名が Azure Platform で作成されます。

> [!NOTE]
> 移行されるリソースのために作成されたリソース グループの名前 (つまり "-Migrated") は選択できません。 ただし、移行の完了後には、Azure Resource Manager の移動機能を使用して、リソースを任意のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

次の 2 つのスクリーンショットには、準備操作が正常に終了した後の結果が表示されています。 最初のスクリーンショットには、元のクラウド サービスを含むリソース グループが表示されています。 2 番目のスクリーンショットには、"-Migrated" の付いた新しいリソース グループが表示されています。ここには、同じ Azure Resource Manager リソースが含まれています。

![元のクラウド サービスを示すスクリーンショット](./media/migration-classic-resource-manager/portal-classic.png)

![準備操作時の Azure Resource Manager リソースを示すスクリーンショット](./media/migration-classic-resource-manager/portal-arm.png)

ここで、準備フェーズ完了後のリソースを概観しておきましょう。 データ プレーンのリソースは同じであることに注意してください。 管理プレーン (クラシック デプロイ モデル) とコントロール プレーン (Resource Manager) の両方で表されています。

![準備フェーズの図](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> クラシック デプロイ モデルの仮想ネットワーク内にない VM は、この移行フェーズで "停止済みかつ割り当て解除済み" になります。
>

### <a name="check-manual-or-scripted"></a>チェック (手動またはスクリプト)
このチェック手順では、移行が正しく行われていることを検証するために、前の手順でダウンロードした構成を使用できます。 また、ポータルにサインインし、プロパティとリソースをスポット チェックして、メタデータが正しく移行されていることを検証することもできます。

仮想ネットワークを移行する場合、仮想マシンの構成のほとんどが再開されません。 これらの VM 上のアプリケーションについて、アプリケーションがまだ実行されていることを検証できます。

監視スクリプトと操作スクリプトをテストすることで、VM が期待どおりに機能していること、および更新したスクリプトが正しく機能することを確認できます。 リソースが準備されている状態の場合は、GET 操作のみがサポートされます。

移行のコミットが必要になるまでの期間は設定されません。 この状態で必要なだけ時間をかけることができます。 ただし、中止またはコミットするまで、これらのリソースに対して管理プレーンがロックされることに注意してください。

問題がある場合は、いつでも移行を中止し、クラシック デプロイ モデルに戻ることができます。 戻ると、リソースに対する管理プレーン操作がロック解除されるため、クラシック デプロイ モデルでそれらの VM に対する通常の操作を再開することができます。

### <a name="abort"></a>中止
この手順は省略可能です。クラシック デプロイ モデルに対する変更を元に戻し、移行を中止したい場合に使用します。 リソースの (準備手順で作成された) Resource Manager メタデータは、この操作で削除されます。 

![中止手順の図](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> この操作は、コミット操作をトリガーした後には実行できません。     
>

### <a name="commit"></a>Commit
検証が完了したら、移行をコミットできます。 リソースは Resource Manager デプロイ モデルでのみ使用できるようになります。クラシック デプロイ モデルには表示されません。 つまり、移行されたリソースは新しいポータルでのみ管理できます。

> [!NOTE]
> これはべき等操作です。 失敗した場合、操作を再試行してください。 失敗が続く場合は、サポート チケットを作成するか、[Microsoft Q&A](/answers/index.html) でフォーラムを作成してください。
>
>

![コミット手順の図](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>移行フローチャート

次のフローチャートで移行の流れを示します。

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>クラシック デプロイ モデル リソースから Resource Manager リソースへの変換
次の表では、リソースはクラシック デプロイ モデルと Resource Manager で呼称が異なる場合があります。 その他の機能とリソースは現在サポートされていません。

| クラシック表示 | Resource Manager の表示 | Notes |
| --- | --- | --- |
| クラウド サービス名 (ホステッド サービス名) |DNS name |移行中、 `<cloudservicename>-migrated`の命名パターンで、すべてのクラウド サービスに新しいリソース グループが作成されます。 このリソース グループには、すべてのリソースが含まれています。 クラウド サービス名は、パブリック IP アドレスが関連付けられた DNS 名になります。 |
| 仮想マシン |仮想マシン |VM 固有のプロパティはそのまま移行されます。 コンピューター名など、一部の osProfile 情報はクラシック デプロイ モデルに格納されておらず、移行後も空のままです。 |
| VM に接続されているディスク リソース |VM に接続されている暗黙的なディスク |ディスクは、Resource Manager デプロイ モデルの最上位リソースとしてモデル化されません。 VM の下で暗黙的なディスクとして移行されます。 VM に接続されているディスクのみがサポートされています。 現在 Resource Manager VM ではクラシック デプロイ モデルのストレージ アカウントを使用できるため、更新がなくてもディスクを簡単に移行できます。 |
| VM 拡張機能 |VM 拡張機能 |XML 拡張機能を除くすべてのリソース拡張機能が、クラシック デプロイ モデルから移行されます。 |
| 仮想マシン証明書 |Azure Key Vault の証明書 |クラウド サービスにサービス証明書が含まれている場合、移行によってクラウド サービスごとに新しい Azure キー コンテナーが作成され、証明書はそこに移動されます。 VM は、Key Vault の証明書を参照するように更新されます。 <br><br> キー コンテナーは削除しないでください。 VM の状態が失敗に変わる可能性があります。 |
| WinRM 構成 |osProfile の WinRM 構成 |Windows リモート管理の構成は、移行の一環として、そのまま移動されます。 |
| 可用性セットのプロパティ |可用性セットのリソース | クラシック デプロイ モデルでは、可用性セットの指定は VM のプロパティです。 可用性セットは、移行の一環として、上位のリソースになります。 1 つのクラウド サービスに対して複数の可用性セット、または 1 つ以上の可用性セットとクラウド サービスの可用性セットにない VM という構成はサポートされていません。 |
| VM のネットワーク構成 |プライマリ ネットワーク インターフェイス |移行後、VM のネットワーク構成がプライマリ ネットワーク インターフェイス リソースとして表示されます。 仮想ネットワークにない VM の場合は、内部 IP アドレスが移行中に変わります。 |
| VM の複数のネットワーク インターフェイス |ネットワーク インターフェイス |VM に複数のネットワーク インターフェイスが関連付けられている場合、移行の一環として、各ネットワーク インターフェイスがすべてのプロパティと共に上位のリソースになります。 |
| 負荷分散エンドポイント セット |Load Balancer |クラシック デプロイ モデルでは、クラウド サービスごとに暗黙的なロード バランサーが割り当てられました。 移行中に、新しいロード バランサー リソースが作成され、そのロード バランサー エンドポイント セットはロード バランサー ルールになります。 |
| 受信 NAT のルール |受信 NAT のルール |VM に定義されている入力エンドポイントは、移行中、ロード バランサーで受信ネットワーク アクセス変換ルールに変換されます。 |
| VIP アドレス |DNS 名のあるパブリック IP アドレス |仮想 IP アドレスがパブリック IP アドレスになり、ロード バランサーに関連付けられます。 仮想 IP は、それに割り当てられている入力エンドポイントがある場合にのみ移行することができます。 |
| 仮想ネットワーク |仮想ネットワーク |仮想ネットワークとそのすべてのプロパティが Resource Manager デプロイ モデルに移行されます。 `-migrated`という名前で新しいリソース グループが作成されます。 |
| 予約済み IP |静的割り当て方法のあるパブリック IP アドレス |ロード バランサーに関連付けられている予約済み IP が、クラウド サービスまたは仮想マシンの移行に伴って移行されます。 関連付けられていない予約済み IP は、[Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip) を使用して移行できます。  |
| VM ごとのパブリック IP アドレス |動的割り当て方法のあるパブリック IP アドレス |VM に関連付けられているパブリック IP アドレスは、割り当て方法が静的に設定されているパブリック IP アドレス リソースとして変換されます。 |
| NSG |NSG |サブネットに関連付けられているネットワーク セキュリティ グループは、Resource Manager デプロイ モデルへの移行の一環として複製されます。 移行中、クラシック デプロイ モデルの NSG は削除されません。 ただし、NSG の管理プレーン操作は、移行中はブロックされます。 関連付けられていない NSG は、[Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup) を使用して移行できます。|
| DNS サーバー |DNS サーバー |仮想ネットワークまたは VM に関連付けられている DNS サーバーは、該当するリソース移行の一環として、すべてのプロパティと共に移行されます。 |
| UDR |UDR |サブネットに関連付けられているユーザー定義のルートは、Resource Manager デプロイ モデルへの移行の一環として複製されます。 移行中、クラシック デプロイ モデルの UDR は削除されません。 UDR の管理プレーン操作は、移行中はブロックされます。 関連付けられていない UDR は、[Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable) を使用して移行できます。 |
| VM のネットワーク構成の IP 転送プロパティ |NIC の IP 転送プロパティ |VM上 の IP 転送プロパティは、移行中、ネットワーク インターフェイスでプロパティに変換されます。 |
| 複数の IP のあるロード バランサー |複数のパブリック IP リソースのあるロード バランサー |移行後、ロード バランサーが関連付けられているすべてのパブリック IP がパブリック IP リソースに変換され、ロード バランサーに関連付けられます。 |
| VM の内部 DNS 名 |NIC の内部 DNS 名 |移行中、VM の内部 DNS サフィックスは、NIC の "InternalDomainNameSuffix" という名前の読み取り専用プロパティに移行されます。 サフィックス名は移行後もそのままで、VM 解決は前と同じように動作し続けます。 |
| 仮想ネットワーク ゲートウェイ |仮想ネットワーク ゲートウェイ |仮想ネットワーク ゲートウェイのプロパティはそのまま移行されます。 ゲートウェイに関連付けられている VIP も変更されません。 |
| ローカル ネットワーク サイト |ローカル ネットワーク ゲートウェイ |ローカル ネットワーク サイトのプロパティは、ローカル ネットワーク ゲートウェイと呼ばれる新しいリソースにそのまま移行されます。 これは、オンプレミス アドレス プレフィックスおよびリモート ゲートウェイ IP を表します。 |
| 接続の参照 |Connection |ネットワーク構成のゲートウェイとローカル ネットワーク サイトを結ぶ接続の参照は、"接続" と呼ばれる新しいリソースによって表されます。 ネットワーク構成ファイル内の接続の参照に関するすべてのプロパティは、"接続" リソースにそのままコピーされます。 クラシック デプロイ モデルの仮想ネットワーク間の接続は、仮想ネットワークを表すローカル ネットワーク サイトへの 2 つの IPsec トンネルを作成することで実現されます。 Resource Manager モデルでは、これが VNet2VNet の接続の種類に変更され、ローカル ネットワーク ゲートウェイは不要になります。 |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>移行後のオートメーションおよびツールの変更
クラシック デプロイ モデルから Resource Manager デプロイ モデルへのリソース移行の一環として、既存のオートメーションまたはツールを、リソース移行後も確実に機能し続けるように更新する必要があります。


## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-cli.md)
* [VPN Gateway クラシックから Resource Manager への移行](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../expressroute/expressroute-migration-classic-resource-manager.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md)
* [Review most common migration errors](migration-classic-resource-manager-errors.md) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md)
