---
title: Azure 上に Ethereum Proof-of-Authority Consortium ソリューション テンプレートをデプロイする
description: Etherereum Proof-of-Authority Consortium ソリューションを使用して Azure 上にマルチメンバー コンソーシアム型 Ethereum ネットワークをデプロイして構成する
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-javascript
ms.openlocfilehash: f0b7d056d37ffb11945a5c50e2705b5d74402007
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386114"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure 上に Ethereum Proof-of-Authority Consortium ソリューションをデプロイする

[Ethereum Proof-of-Authority Consortium (プレビュー) Azure ソリューション テンプレート](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum)を使用すると、Azure と Ethereum の最小限の知識で、マルチメンバー コンソーシアム型 Proof-of-Authority Ethereum ネットワークをデプロイ、構成、管理できます。

ソリューション テンプレートにより、各コンソーシアム メンバーは、Microsoft Azure コンピューティング、ネットワーク、およびストレージ サービスを使用してブロックチェーン ネットワーク フットプリントをプロビジョニングできます。 各コンソーシアム メンバーのネットワーク フットプリントは、一連の負荷分散型検証ノードで構成されます。アプリケーションまたはユーザーはそれらのノードと対話して、Ethereum トランザクションを送信できます。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain ソリューションの選択

Ethereum Proof-of-Authority Consortium ソリューション テンプレートを使用する前に、利用可能な Azure Blockchain オプションの一般的なユース ケースと実際のシナリオを比較してください。

> [!IMPORTANT]
> Azure ソリューション テンプレートでの Ethereum ではなく、[Azure Blockchain Service](../service/overview.md) の使用を検討します。 Azure Blockchain Service は、サポートされているマネージド Azure サービスです。 Parity Ethereum は、コミュニティ主導の開発とメンテナンスに移行されました。 詳細については、「[OpenEthereum DAO への Parity Ethereum の移行](https://www.parity.io/parity-ethereum-openethereum-dao/)」を参照してください。

オプション | サービス モデル | 一般的なユース ケース
-------|---------------|-----------------
ソリューション テンプレート | IaaS | ソリューション テンプレートは、完全に構成されたブロックチェーン ネットワーク トポロジのプロビジョニングに使用できる Azure Resource Manager テンプレートです。 これらのテンプレートでは、特定のブロックチェーン ネットワークの種類に対応する Microsoft Azure コンピューティング、ネットワーク、およびストレージ サービスをデプロイして構成します。 ソリューション テンプレートは、サービス レベル アグリーメントなしで提供されます。 サポートが必要な場合には、[Microsoft Q&A の質問ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service (プレビュー) により、コンソーシアム ブロックチェーン ネットワークの構成、管理、ガバナンスが簡素化されます。 Azure Blockchain Service は、PaaS、コンソーシアム管理、またはコントラクトとトランザクションのプライバシーを必要とするソリューションに使用します。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS および PaaS | Azure Blockchain Workbench プレビューは、ブロックチェーン アプリケーションを作成してデプロイし、ビジネス プロセスやデータを他の組織と効果的に共有できるよう設計された、Azure サービスと機能のコレクションです。 Azure Blockchain Workbench は、ブロックチェーン ソリューションまたはブロックチェーン アプリケーションの概念実証のプロトタイプを作成する際に使用します。 Azure Blockchain Workbench は、サービス レベル アグリーメントなしで提供されます。 サポートが必要な場合には、[Microsoft Q&A 質問ページ](/answers/topics/azure-blockchain-workbench.html)をご利用ください。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

Ethereum ソリューション テンプレートを使用して、単一または複数リージョンベースのマルチメンバー Ethereum Proof-of-Authority Consortium ネットワークをデプロイできます。

![デプロイ アーキテクチャ](./media/ethereum-poa-deployment/deployment-architecture.png)

各コンソーシアム メンバーのデプロイには以下が含まれます。

* PoA バリデーターを実行するための Virtual Machines
* RPC、ピアリング、およびガバナンス DApp の要求を分散するための Azure Load Balancer
* バリデーター ID をセキュリティで保護するための Azure Key Vault
* 永続的なネットワーク情報をホストし、リースを調整するための Azure Storage
* ログとパフォーマンスの統計情報を集約するための Azure Monitor
* プライベート VNet 間の VPN 接続を可能にするための VNet ゲートウェイ (オプション)

既定では、サブスクリプションとクラウド間の接続を簡単にするために、RPC とピアリング エンドポイントにはパブリック IP を使用してアクセスできます。 アプリケーション レベルのアクセス制御には、[Parity のアクセス許可コントラクト](https://wiki.parity.io/Permissioning)を使用できます。 サブスクリプション間接続に VNet ゲートウェイを利用する、VPN の背後にデプロイされたネットワークがサポートされています。 VPN と VNet のデプロイはより複雑であるため、ソリューションのプロトタイプを作成するときは、パブリック IP モデルから始めることをお勧めします。

信頼性とモジュール性を実現するために、Docker コンテナーを使用します。 バージョン管理されたイメージを各デプロイの一部としてホストして使用するために、Azure Container Registry を使用します。 コンテナー イメージは以下で構成されます。

* オーケストレーター - ID とガバナンス コントラクトを生成します。 ID ストアに ID を格納します。
* Parity クライアント - ID ストアから ID をリースします。 ピアを検出し、接続します。
* EthStats エージェント - RPC 経由でローカル ログと統計を収集し、情報を Azure Monitor にプッシュします。
* ガバナンス DApp - ガバナンス コントラクトを操作するための Web インターフェイスです。

### <a name="validator-nodes"></a>検証ノード

Proof-of-Authority プロトコルでは、バリデーター ノードが従来のマイナー ノードの代わりとなります。 各検証コントロールには、ブロック作成プロセスへの参加を可能にする一意の Ethereum ID があります。 各コンソーシアム メンバーは、geo 冗長性のために、5 つのリージョンで 2 つ以上のバリデーター ノードをプロビジョニングできます。 バリデーター ノードは他のバリデーター ノードと通信して、基礎となる分散型台帳の状態についてコンセンサスを形成します。 ネットワークへの公正な参加を保証するために、各コンソーシアム メンバーは、ネットワーク上の最初のメンバーよりも多くの検証コントロールを使用することを禁止されています。 たとえば、最初のメンバーが 3 つの検証コントロールをデプロイした場合、各メンバーが使用できる検証コントロールは最大 3 つに限られます。

### <a name="identity-store"></a>ID ストア

ID ストアは、生成された Ethereum ID を安全に保持するために各メンバーのサブスクリプションにデプロイされます。 検証コントロールごとに、オーケストレーション コンテナーによって Ethereum 秘密キーが生成され、Azure Key Vault に格納されます。

## <a name="deploy-ethereum-consortium-network"></a>Ethereum コンソーシアム ネットワークのデプロイ

このチュートリアルでは、マルチパーティー Ethereum コンソーシアム ネットワークを作成すると仮定します。 次のフローは、マルチパーティー デプロイの例を示しています。

1. 3 人のメンバーが、MetaMask を使用してそれぞれ Ethereum アカウントを生成します
1. *メンバー A* が、パブリック Ethereum アドレスを指定して Ethereum PoA をデプロイします
1. *メンバー A* が、*メンバー B* と*メンバー C* にコンソーシアムの URL を提供します
1. *メンバー B* と*メンバー C* が、自分たちのパブリック Ethereum アドレスと*メンバー A* のコンソーシアムの URL を入力して Ethereum PoA をデプロイします
1. *メンバー A* が、*メンバー B* を管理者とする投票をします
1. *メンバー A* と*メンバー B* の両方が、*メンバー C* を管理者とする投票をします

以下のセクションでは、ネットワークで最初のメンバーのフットプリントを構成する方法について説明します。

### <a name="create-resource"></a>リソースを作成する

[Azure portal](https://portal.azure.com) で、左上隅にある **[リソースの作成]** を選択します。

**[ブロックチェーン]**  >  **[Ethereum Proof-of-Authority Consortium (preview)]\(Ethereum Proof-of-Authority Consortium (プレビュー)\)** を選択します。

### <a name="basics"></a>基本

**[基本]** では、デプロイの標準パラメーターの値を指定します。

![基本](./media/ethereum-poa-deployment/basic-blade.png)

パラメーター | 説明 | 値の例
----------|-------------|--------------
Create a new network or join existing network?\(新しいネットワークの作成または既存のネットワークへの参加\) | 新しいコンソーシアム ネットワークを作成することも、既存のコンソーシアム ネットワークに参加することもできます。 既存のネットワークに参加するには、追加のパラメーターが必要です。 | 新規作成
電子メール アドレス | デプロイの完了時に、デプロイに関する情報を含む電子メール通知が送信されます。 | 有効な電子メール アドレス
VM ユーザー名 | デプロイされた各 VM の管理者ユーザー名 | 1 から 64 文字の英数字
認証の種類 | 仮想マシンに対して認証する方法。 | Password
Password | デプロイされた各仮想マシンの管理者アカウントのパスワード。 すべての VM に最初は同じパスワードが設定されます。 パスワードはプロビジョニング後に変更できます。 | 12 - 72 文字 
サブスクリプション | コンソーシアム ネットワークをデプロイするサブスクリプション |
リソース グループ| コンソーシアム ネットワークをデプロイするリソース グループ | myResourceGroup
場所 | リソース グループの Azure リージョン。 | 米国西部 2

**[OK]** を選択します。

### <a name="deployment-regions"></a>Deployment regions (デプロイ リージョン)

*[Deployment regions]\(デプロイ リージョン\)* では、リージョン数と各リージョンの場所を指定します。 最大 5 つのリージョンにデプロイできます。 1 つ目のリージョンは、 *[基本]* セクションのリソース グループの場所と一致する必要があります。 開発またはテスト用のネットワークでは、メンバーごとに 1 つのリージョンを使用できます。 運用環境では、高可用性を確保するために、2 つ以上のリージョンにデプロイします。

![[Deployment regions]\(デプロイ リージョン\)](./media/ethereum-poa-deployment/deployment-regions.png)

パラメーター | 説明 | 値の例
----------|-------------|--------------
Number of region(s) (リージョン数)|コンソーシアム ネットワークをデプロイするリージョンの数| 2
First region (リージョン 1) | コンソーシアム ネットワークをデプロイする 1 つ目のリージョン | 米国西部 2
Second region (リージョン 2) | コンソーシアム ネットワークをデプロイする 2 つ目のリージョン。 リージョン数が 2 以上の場合、追加のリージョンが表示されます。 | 米国東部 2

**[OK]** を選択します。

### <a name="network-size-and-performance"></a>Network size and performance (ネットワークのサイズとパフォーマンス)

*[Network Size and Performance]\(ネットワークのサイズとパフォーマンス\)* では、コンソーシアム ネットワークのサイズを入力します。 検証ノードのストレージ サイズにより、ブロックチェーンの可能なサイズが決まります。 サイズはデプロイ後に変更できます。

![Network size and performance (ネットワークのサイズとパフォーマンス)](./media/ethereum-poa-deployment/network-size-and-performance.png)

パラメーター | 説明 | 値の例
----------|-------------|--------------
Number of load balanced validator nodes (負荷分散型バリデータ ノードの数) | ネットワークの一部としてプロビジョニングする検証ノードの数。 | 2
Validator node storage performance (バリデーター ノード ストレージのパフォーマンス) | デプロイされる各検証ノードのマネージド ディスクの種類。 価格の詳細については、[ストレージの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。 | Standard SSD
Validator node virtual machine size (バリデーター ノードの仮想マシンのサイズ) | バリデーター ノードに使用される仮想マシンのサイズ。 価格の詳細については、[仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)に関するページをご覧ください。 | Standard D2 v3

仮想マシンとストレージ層はネットワークのパフォーマンスに影響します。  次の表を参考にして、コスト効率を選択してください。

仮想マシン SKU|ストレージ層|Price|スループット|Latency
---|---|---|---|---
F1|Standard SSD|low|low|high
D2_v3|Standard SSD|中|中|中
F16s|Premium SSD|high|high|low

**[OK]** を選択します。

### <a name="ethereum-settings"></a>Ethereum settings (Ethereum の設定)

*[Ethereum Settings]\(Ethereum の設定\)* では、Ethereum 関連の構成設定を指定します。

![Ethereum settings (Ethereum の設定)](./media/ethereum-poa-deployment/ethereum-settings.png)

パラメーター | 説明 | 値の例
----------|-------------|--------------
Consortium Member ID (コンソーシアム メンバー ID) | コンソーシアム ネットワークに参加する各メンバーに関連付けられている ID。 競合を回避するように IP アドレス空間を構成するために使用されます。 プライベート ネットワークの場合、メンバー ID は同じネットワーク内のさまざまな組織間で一意である必要があります。  同じ組織が複数のリージョンにデプロイする場合でも、一意のメンバー ID が必要です。 競合が発生しないように他の参加メンバーと共有する必要があるため、このパラメーターの値を書き留めておきます。 有効な範囲は 0 から 255 です。 | 0
ネットワーク ID | デプロイされているコンソーシアム Ethereum ネットワークのネットワーク ID。 各 Ethereum ネットワークには独自のネットワーク ID があり、1 はパブリック ネットワークの ID を示します。 有効な範囲は 5 から 999,999,999 です。 | 10101010
Admin Ethereum Address (管理者 Ethereum アドレス) | PoA ガバナンスに参加するために使用される Ethereum アカウントのアドレス。 MetaMask を使用して Ethereum アドレスを生成できます。 |
詳細オプション | Ethereum 設定の詳細オプション | 有効化
Deploy using Public IP?\(パブリック IP を使用してデプロイ\) | [Private VNet]\(プライベート VNet\) を選択した場合、ネットワークは VNet ゲートウェイの背後にデプロイされ、ピアリング アクセスが削除されます。 プライベート VNet では、接続に互換性を持たせるために、すべてのメンバーが VNet ゲートウェイを使用する必要があります。 | パブリック IP
Block Gas Limit\(ブロック ガスの制限\) | ネットワークの開始ブロック ガスの制限。 | 50000000
ブロック再シール期間 (秒) | ネットワーク上のトランザクションがない場合に空のブロックが作成される頻度。 頻度が高いとパフォーマンスが向上しますが、ストレージ コストが増加します。 | 15
Transaction Permission Contract\(トランザクションのアクセス許可コントラクト\) | トランザクションのアクセス許可コントラクトのバイトコード。 スマート コントラクトのデプロイと実行を、許可された Ethereum アカウントのリストのみに制限します。 |

**[OK]** を選択します。

### <a name="monitoring"></a>監視

[監視] では、ネットワークのログ リソースを構成できます。 監視エージェントは、ネットワークから有用なメトリックとログを収集して表示し、ネットワークの正常性やデバッグの問題をすばやく確認できるようにします。

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

パラメーター | 説明 | 値の例
----------|-------------|--------------
監視 | 監視を有効にするオプション | 有効化
既存の Azure Monitor ログへの接続 | 新しい Azure Monitor ログ インスタンスを作成するか、既存のインスタンスに参加するオプション | 新規作成
場所 | 新しいインスタンスがデプロイされるリージョン | 米国東部
既存のログ分析ワークスペース ID (既存の Azure Monitor ログへの接続 = 既存に参加)|既存の Azure Monitor ログ インスタンスのワークスペース ID||NA
既存のログ分析主キー (既存の Azure Monitor ログへの接続 = 既存に参加)|既存の Azure Monitor ログ インスタンスへの接続に使用される主キー||NA

**[OK]** を選択します。

### <a name="summary"></a>まとめ

概要の各項目をクリックして、指定した内容を確認し、基本的なデプロイ前検証を実行します。 デプロイの前に、テンプレートとパラメーターをダウンロードできます。

**[作成]** を選択してデプロイします。

デプロイに VNet ゲートウェイが含まれている場合は、デプロイに最大 45 から 50 分かかることがあります。

## <a name="deployment-output"></a>デプロイの出力

デプロイが完了したら、Azure portal を使用して必要なパラメーターにアクセスできます。

### <a name="confirmation-email"></a>確認の電子メール

電子メール アドレスを指定した場合 ([[基本] セクション](#basics))、デプロイの情報とこのドキュメントへのリンクを含む電子メールが送信されます。

![デプロイの電子メール](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>ポータル

デプロイが正常に完了し、すべてのリソースがプロビジョニングされると、リソース グループ内の出力パラメーターを表示できます。

1. ポータルで目的のリソース グループに移動します。
1. **[概要] > [デプロイ]** を選択します。

    ![リソース グループの概要](./media/ethereum-poa-deployment/resource-group-overview.png)

1. **[microsoft-azure-blockchain.azure-blockchain-ether-...]** デプロイを選択します。
1. **[出力]** セクションを選択します。

    ![デプロイの出力](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>コンソーシアムの拡張

コンソーシアムを拡張するには、まず物理ネットワークを接続する必要があります。 VPN の背後にデプロイする場合は、「[VNet ゲートウェイの接続](#connecting-vnet-gateways)」を参照し、新しいメンバーのデプロイの一部としてネットワーク接続を構成します。 デプロイが完了したら、[ガバナンス DApp](#governance-dapp) を使用してネットワーク管理者になります。

### <a name="new-member-deployment"></a>新しいメンバーのデプロイ

次の情報を参加するメンバーと共有します。 この情報は、デプロイ後の電子メールまたはポータルのデプロイの出力で確認できます。

* Consortium Data URL (コンソーシアム データの URL)
* デプロイ済みのノード数
* VNet ゲートウェイのリソース ID (VPN を使用する場合)

デプロイするメンバーは、ネットワーク プレゼンスをデプロイするときに、次のガイダンスに従って同じ Ethereum Proof-of-Authority Consortium ソリューション テンプレートを使用する必要があります。

* **[Join existing]\(既存に参加\)** を選択します
* 公正な代表制を保証するために、残りのメンバーと同数の検証ノードを選択します
* 同じ管理者 Ethereum アドレスを使用します
* *[Ethereum Settings]\(Ethereum の設定\)* で提供された*コンソーシアム データの URL* を使用します
* ネットワークの残りの部分が VPN の背後にある場合は、詳細セクションで **[Private VNet]\(プライベート VNet\)** を選択します

### <a name="connecting-vnet-gateways"></a>VNet ゲートウェイの接続

このセクションは、プライベート VNet を使用してデプロイした場合にのみ必要となります。 パブリック IP アドレスを使用している場合は、このセクションをスキップしてかまいません。

プライベート ネットワークの場合、さまざまなメンバーが VNet ゲートウェイ接続を介して接続されます。 メンバーがネットワークに参加してトランザクション トラフィックを表示する前に、既存のメンバーが最終的な構成を VPN ゲートウェイで実行し、接続を受け入れる必要があります。 参加メンバーの Ethereum ノードは、接続が確立するまで実行されません。 単一障害点が生じる可能性を減らすには、コンソーシアムに冗長ネットワーク接続を作成します。

新しいメンバーのデプロイ後に、既存のメンバーは新しいメンバーへの VNet ゲートウェイ接続を設定して、双方向接続を完成させる必要があります。 既存のメンバーには以下が必要です。

* 接続するメンバーの VNet ゲートウェイのリソース ID。 「[デプロイの出力](#deployment-output)」をご覧ください。
* 共有接続キー。

既存のメンバーは、次の PowerShell スクリプトを実行して接続を完成させる必要があります。 ポータルの右上のナビゲーション バーにある Azure Cloud Shell を使用できます。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>サービスの監視

Azure Monitor ポータルを表示するには、デプロイ電子メールにあるリンク先にアクセスするか、デプロイの出力でパラメーター [OMS_PORTAL_URL] を見つけます。

ポータルには、まず、高レベルのネットワーク統計情報とノードの概要が表示されます。

![監視のカテゴリ](./media/ethereum-poa-deployment/monitor-categories.png)

**[Node Overview]\(ノードの概要\)** を選択すると、ノードごとのインフラストラクチャ統計が表示されます。

![ノードの統計](./media/ethereum-poa-deployment/node-stats.png)

**[Network Stats]\(ネットワークの統計\)** を選択すると、Ethereum ネットワークの統計が表示されます。

![ネットワークの統計](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

監視ログのクエリを実行して、エラーを調査したり、しきい値のアラートを設定したりできます。 "*ログ検索*" ツールで実行できるクエリの例を以下に示します。

複数の検証コントロール クエリによって報告されたリスト ブロックは、チェーンのフォークを見つける際に役立ちます。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

指定した検証ノードの、5 分間のバケットで平均された平均ピア数を取得します。

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH アクセス

セキュリティ上の理由から、既定では、SSH ポートのアクセスはネットワーク グループ セキュリティ ルールによって 拒否されます。 PoA ネットワークの仮想マシン インスタンスにアクセスするには、次のセキュリティ ルールを *[許可]* に変更する必要があります。

1. Azure portal で、デプロイされているリソース グループの **[概要]** セクションに移動します。

    ![SSH の概要](./media/ethereum-poa-deployment/ssh-overview.png)

1. アクセスする VM のリージョンの **[ネットワーク セキュリティ グループ]** を選択します。

    ![SSH の NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. **[allow-ssh]** ルールを選択します。

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. **[アクション]** を **[許可]** に変更します。

    ![SSH 有効化を許可](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. **[保存]** を選択します。 変更が適用されるまでに数分かかることがあります。

指定した管理者ユーザー名とパスワード/SSH キーを使用して、検証ノードの仮想マシンに SSH 経由でリモート接続できます。 最初の検証ノードにアクセスするための SSH コマンドは、テンプレートのデプロイの出力に示されます。 次に例を示します。

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

追加のトランザクション ノードを取得するには、ポート番号を 1 ずつ増やします。

複数のリージョンにデプロイした場合は、コマンドを、そのリージョン内のロード バランサーの DNS 名または IP アドレスに変更します。 他のリージョンの DNS 名または IP アドレスを見つけるには、名前付け規則 **\*\*\*\*\*-lbpip-reg\#** が使用されたリソースを見つけ、その DNS 名と IP アドレスのプロパティを確認します。

## <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager の負荷分散

Azure Traffic Manager を使用すると、異なるリージョン内の複数のデプロイ間で着信トラフィックをルーティングすることにより、PoA ネットワークのダウンタイムを短縮し、応答性を向上できます。 組み込みの正常性チェックと自動再ルーティングにより、RPC エンドポイントとガバナンス DApp の高可用性を確保できます。 この機能は、複数のリージョンにデプロイ済みで稼働準備ができている場合に便利です。

Traffic Manager を使用して、自動フェールオーバーによって PoA ネットワークの可用性を向上させます。 また、Traffic Manager を使用して、ネットワーク待機時間が最も短い Azure の場所にエンド ユーザーをルーティングすることによって、ネットワークの応答性を高めることもできます。

Traffic Manager プロファイルを作成すると、プロファイルの DNS 名を使用してネットワークにアクセスできます。 また、他のコンソーシアム メンバーがネットワークに追加されたら、そのメンバーがデプロイしたバリデーター間の負荷分散に Traffic Manager を使用できます。

### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

1. [Azure portal](https://portal.azure.com) で、左上隅にある **[リソースの作成]** を選択します。
1. **[Traffic Manager プロファイル]** を検索します。

    ![Azure Traffic Manager の検索](./media/ethereum-poa-deployment/traffic-manager-search.png)

    プロファイルに一意の名前を付け、PoA のデプロイに使用されたリソース グループを選択します。

1. **[作成]** を選択してデプロイします。

    ![Traffic Manager の作成](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. デプロイしたら、リソース グループでインスタンスを選択します。 Traffic Manager にアクセスするための DNS 名は、[概要] タブで確認できます。

    ![Traffic Manager の DNS の確認](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. **[エンドポイント]** タブを選択し、 **[追加]** ボタンを選択します。
1. エンドポイントに一意の名前を付けます。
1. **[ターゲット リソースの種類]** で、 **[パブリック IP アドレス]** を選択します。
1. 1 つ目のリージョンのロード バランサーのパブリック IP アドレスを選択します。

    ![Traffic Manager のルーティング](./media/ethereum-poa-deployment/traffic-manager-routing.png)

同じ手順を、デプロイ済みのネットワークのリージョンごとに繰り返します。 エンドポイントは、状態が **[有効]** になると自動的に読み込まれ、Traffic Manager の DNS 名でリージョン負荷分散されます。 これで、この記事の他の手順で [CONSORTIUM_DATA_URL] パラメーターの代わりにこの DNS を使用できます。

## <a name="data-api"></a>データ API

各コンソーシアム メンバーは、他のメンバーがネットワークに接続するために必要な情報をホストします。 接続を容易にするために、各メンバーはデータ API エンドポイントで一連の接続情報をホストします。

既存のメンバーは、メンバーのデプロイの前に [CONSORTIUM_DATA_URL] を提供します。 デプロイ時に、参加するメンバーは次のエンドポイントで JSON インターフェイスから情報を取得します。

`<CONSORTIUM_DATA_URL>/networkinfo`

応答には、参加するメンバーに役立つ情報 (ジェネシス ブロック、検証コントロール セットのコントラクト ABI、ブートノード) と、既存のメンバーに役立つ情報 (検証コントロールのアドレス) が含まれています。 この標準化を使用して、複数のクラウド プロバイダーにわたってコンソーシアムを拡張できます。 この API では、次の構造を持つ JSON 形式の応答が返されます。

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>ガバナンス DApp

Proof-of-Authority の中核をなすのは分散型ガバナンスです。 Proof-of-Authority は、ネットワーク機関の許可済みリストによってネットワークの正常性を維持するため、この許可リストに変更を加えるための公正なメカニズムを提供することが重要です。 各デプロイには、この許可済みリストのオンチェーン ガバナンスのための一連のスマート コントラクトとポータルが付属しています。 提案された変更がコンソーシアム メンバーによる投票で過半数に達すると、変更が適用されます。 投票により、新しいコンセンサス参加者の追加や侵害を受けた参加者の削除を透明な方法で行うことができ、誠実なネットワークが促進されます。

ガバナンス DApp は、ネットワーク上の機関を管理するために使用される、事前にデプロイされた[スマート コントラクト](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts)のセットと Web アプリケーションです。 機関は管理 ID と検証ノードに分けられます。
管理者は、コンセンサスへの参加を一連の検証ノードに委任する権限を持ちます。 また、管理者は、他の管理者のネットワーク参加またはネットワーク脱退に投票することもできます。

![ガバナンス DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **分散型ガバナンス:** ネットワーク機関での変更は、一部の管理者によるオンチェーン投票を通じて管理されます。
* **検証コントロールへの委任:** 機関は、各 PoA デプロイに設定されている検証ノードを管理できます。
* **監査可能な変更履歴:** 各変更はブロックチェーンに記録され、透明性と監査機能が提供されます。

### <a name="getting-started-with-governance"></a>ガバナンスの概要

ガバナンス DApp を使用して任意の種類のトランザクションを実行するには、Ethereum ウォレットを使用する必要があります。 最も簡単な方法は、[MetaMask](https://metamask.io) などのブラウザー内ウォレットを使用することですが、これらのスマート コントラクトはネットワークにデプロイされているため、ガバナンス コントラクトとのやり取りを自動化することもできます。

MetaMask をインストールした後、ブラウザーのガバナンス DApp に移動します。  URL は、Azure portal を使用してデプロイの出力で見つけることができます。  ブラウザー内ウォレットがインストールされていない場合、アクションを実行することはできませんが、管理者の状態を表示することは可能です。  

### <a name="becoming-an-admin"></a>管理者になる

ネットワークにデプロイされた最初のメンバーが自動的に管理者になり、そのパリティ ノードが検証コントロールとしてリストされます。 ネットワークに参加している場合は、既存の管理者セットの大多数 (50% 超) によって管理者として投票される必要があります。 管理者にならないことを選択をした場合でも、ノードはブロックチェーンを同期し、検証しますが、ブロック作成プロセスに参加することはありません。 管理者になるための投票プロセスを開始するには、 **[申請]** を選択し、Ethereum アドレスおよび別名を入力します。

![申請](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候補

**[候補]** タブを選択すると、現在の管理者候補が表示されます。  候補者が現在の管理者による投票の過半数に達すると、その候補者は管理者に昇格します。候補者に投票するには、その行を選択し、 **[投票]** を選択します。 投票について気が変わった場合は、候補者を選択し、 **[Rescind vote]\(投票の取り消し\)** を選択します。

![候補](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理者

**[管理者]** タブでは、現在の管理者が表示され、反対投票を行うことができます。  管理者が 50% を超える支持を失うと、ネットワークの管理者から削除されます。 その管理者が所有している検証ノードは検証コントロールのステータスを失い、ネットワークのトランザクション ノードになります。 管理者が削除される理由はさまざまですが、事前にポリシーに同意するかどうかはコンソーシアム次第です。

![管理者](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>検証コントロール

**[検証コントロール]** タブを選択すると、インスタンスに現在デプロイされているパリティ ノードとその現在のステータス (ノードのタイプ) が表示されます。 このビューは現在デプロイされているコンソーシアム メンバーを表しているので、このリストに含まれる検証コントロール セットはコンソーシアム メンバーごとに異なります。 インスタンスが新しくデプロイされ、検証コントロールを追加していない場合は、 **[Add Validators]\(検証コントロールの追加\)** オプションが表示されます。 検証コントロールを追加すると、リージョン負荷分散されたパリティ ノードのセットが自動的に選択され、検証コントロール セットに割り当てられます。 許可されている容量を超えるノードをデプロイした場合、残りのノードはネットワークのトランザクション ノードになります。

検証コントロールそれぞれのアドレスは Azure の [ID ストア](#identity-store)によって自動的に割り当てられます。  ノードがダウンした場合は、その ID が放棄されるので、デプロイ内の別のノードに引き継ぐことができます。 このプロセスにより、コンセンサスへの参加の高可用性が確保されます。

![検証コントロール](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>コンソーシアム名

管理者はコンソーシアム名を更新できます。  左上の歯車アイコンを選択してコンソーシアム名を更新します。

### <a name="account-menu"></a>アカウント メニュー

右上には、Ethereum アカウントの別名と ID があります。  管理者は別名を更新できます。

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum の開発<a id="tutorials"></a>

スマート コントラクトをコンパイル、デプロイ、テストするために、Ethereum の開発で検討できるオプションを次に示します。
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - クライアントベースの Ethereum 開発環境
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - ブラウザベースのローカルの Ethereum 開発環境

### <a name="compile-deploy-and-execute-smart-contract"></a>スマート コントラクトをコンパイル、デプロイ、実行する

次の例では、単純なスマート コントラクトを作成します。 Truffle を使用して、スマート コントラクトをコンパイルし、ブロックチェーン ネットワークにデプロイします。 デプロイしたら、トランザクションを介してスマート コントラクト関数を呼び出します。

#### <a name="prerequisites"></a>前提条件

* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) をインストールする。 Truffle と Web3 には Python が必要です。 Python をパスに含めるインストール オプションを選択します。
* Truffle v5.0.5 をインストールします (`npm install -g truffle@v5.0.5`)。 Truffle では、[Node.js](https://nodejs.org)、[Git](https://git-scm.com/) など、いくつかのツールがインストールされている必要があります。 詳細については、[Truffle のドキュメント](https://github.com/trufflesuite/truffle)をご覧ください。

### <a name="create-truffle-project"></a>Truffle プロジェクトを作成する

スマート コントラクトをコンパイルしてデプロイするには、Truffle プロジェクトを作成しておく必要があります。

1. コマンド プロンプトまたはシェルを開きます。
1. `HelloWorld` という名前のフォルダーを作成します。
1. ディレクトリを新しい `HelloWorld` フォルダーに変更します。
1. `truffle init` コマンドを使用して、新しい Truffle プロジェクトを初期化します。

    ![新しい Truffle プロジェクトの作成](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>スマート コントラクトを追加する

Truffle プロジェクトの **contracts** サブディレクトリにスマート コントラクトを作成します。

1. Truffle プロジェクトの **contracts** サブディレクトリに、`postBox.sol` という名前のファイルを作成します。
1. **postBox.sol** に次の Solidity コードを追加します。

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Truffle を使用してスマート コントラクトをデプロイする

Truffle プロジェクトには、ブロックチェーン ネットワーク接続の詳細の構成ファイルが含まれています。 この構成ファイルを変更して、ネットワークの接続情報を含めます。

> [!WARNING]
> Ethereum 秘密キーをネットワーク経由で送信することは絶対にしないでください。 必ず、最初に各トランザクションに署名してから、署名済みのトランザクションをネットワークで送信するようにしてください。

1. [ブロックチェーン ネットワークのデプロイ時に使用される Ethereum 管理者アカウント](#ethereum-settings)のニーモニック フレーズが必要です。 MetaMask を使用してアカウントを作成した場合は、MetaMask からニーモニックを取得できます。 MetaMask 拡張機能の右上にある管理者アカウント アイコンを選択し、 **[Settings]\(設定\) > [Security & Privacy]\(セキュリティとプライバシー\) > [Reveal Seed Words]\(シード ワードの表示\)** を選択します。
1. Truffle プロジェクトの `truffle-config.js` の内容を次の内容に置き換えます。 プレースホルダーのエンドポイント値とニーモニック値を置き換えます。

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Truffle HD Wallet プロバイダーを使用しているので、`npm install truffle-hdwallet-provider --save` コマンドを使用してプロジェクトにモジュールをインストールします。

Truffle では、移行スクリプトを使用して、スマート コントラクトをブロックチェーン ネットワークにデプロイします。 新しいスマート コントラクトをデプロイするには、移行スクリプトが必要です。

1. 新しいコントラクトをデプロイするための新しい移行を追加します。 Truffle プロジェクトの **migrations** サブディレクトリに、`2_deploy_contracts.js` ファイルを作成します。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Truffle 移行コマンドを使用して、PoA ネットワークにデプロイします。 Truffle プロジェクト ディレクトリのコマンド プロンプトで、次のコマンドを実行します。

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>スマート コントラクト関数を呼び出す

スマート コントラクトがデプロイされたので、関数を呼び出すトランザクションを送信できます。

1. Truffle プロジェクト ディレクトリに、`sendtransaction.js` という名前の新しいファイルを作成します。
1. **sendtransaction.js** に次の内容を追加します。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Truffle 実行コマンドを使用してスクリプトを実行します。

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![トランザクションを介して関数を呼び出すスクリプトの実行](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly (WASM) のサポート

新しくデプロイされた PoA ネットワークでは、WebAssembly のサポートが既に有効になっています。 これにより、Web-Assembly (Rust、C、C++) にトランスパイルされる任意の言語でスマート コントラクトの開発を実行できます。 詳細については、次を参照してください。[Parity による WebAssembly の概要](https://wiki.parity.io/WebAssembly-Home)および[Parity Technologies 提供のチュートリアル](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>よく寄せられる質問

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>送信しなかった多数のトランザクションがネットワーク上にあることに気づきました。 これはどこから生じたものですか?

[パーソナル API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) をロック解除することは安全ではありません。 ボットはロック解除された Ethereum アカウントを待機しており、資金を流出させようと試みます。 ボットはこれらのアカウントが実際の ether を含んでいると想定し、最初に残高を奪おうとします。 ネットワーク上でパーソナル API を有効にしないでください。 代わりに、MetaMask などのウォレットを使用して手動で、またはプログラムで、トランザクションに事前に署名します。

### <a name="how-to-ssh-onto-a-vm"></a>VM に SSH 接続するにはどうすればよいですか?

SSH ポートはセキュリティ上の理由で公開されません。 [このガイドに従って SSH ポートを有効化](#ssh-access)してください。

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>監査メンバーまたはトランザクション ノードを設定するにはどうすればよいですか?

トランザクション ノードは、ネットワークとピアリングされていても、コンセンサスには参加していない一連の Parity クライアントです。 これらのノードを使用しても、Ethereum トランザクションを送信し、スマート コントラクトの状態を読み取ることは可能です。 このメカニズムは、ネットワーク上の権限のないコンソーシアム メンバーに監査機能を提供するために機能します。 これを実現するには、「[コンソーシアムの拡張](#growing-the-consortium)」の手順に従います。

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask トランザクションに長時間かかっているのはなぜですか?

トランザクションが正しい順序で受信されるようにするために、各 Ethereum トランザクションには増分される nonce が付けられています。 別のネットワークで MetaMask のアカウントを使用した場合、nonce 値をリセットする必要があります。 設定アイコン (3 本のバー)、[設定]、[アカウントのリセット] の順にクリックします。 トランザクションの履歴がクリアされ、トランザクションを再送信できるようになります。

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>MetaMask でガスの料金を指定する必要がありますか?

Ether は proof-of-authority コンソーシアムの目的では動作しません。 そのため、MetaMask でトランザクションを送信するときに、ガスの料金を指定する必要はありません。

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Azure OMS のプロビジョニングに失敗したためにデプロイに失敗した場合はどうすればよいですか?

監視はオプション機能です。 Azure Monitor リソースを正常にプロビジョニングできないためにデプロイが失敗するというまれなケースでは、Azure Monitor なしで再デプロイできます。

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>パブリック IP のデプロイは、プライベート ネットワークのデプロイと互換性がありますか?

いいえ。 ピアリングには双方向通信が必要であるため、ネットワーク全体がパブリックまたはプライベートのいずれかである必要があります。

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Proof-of-Authority の予想されるトランザクション スループットはどのくらいですか?

トランザクションのスループットは、トランザクションの種類とネットワーク トポロジに大きく依存します。 単純なトランザクションでは、複数の領域にデプロイされているネットワークで、1 秒あたり平均 400 トランザクションをベンチマークしました。

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>スマート コントラクト イベントをサブスクライブする方法は?

Ethereum の Proof-of-Authority は Web ソケットをサポートするようになりました。  デプロイの出力を確認して、Web ソケットの URL とポートを見つけてください。

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Blockchain の最新情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)をご覧ください。Azure Blockchain エンジニアリング チームからの情報やブロックチェーン サービスの内容に関する最新の情報を把握することができます。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)で投稿またはアイデアに投票してください。

### <a name="community-support"></a>コミュニティ サポート

Microsoft のエンジニアや Azure Blockchain コミュニティのエキスパートと交流できます。

* [Microsoft Q&A 質問ページ](/answers/topics/azure-blockchain-workbench.html)。 ブロックチェーン テンプレートに関するエンジニアリング サポートは、デプロイに関する問題に限定されています。
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>次のステップ

その他の Azure Blockchain ソリューションについては、[Azure Blockchain のドキュメント](../index.yml)をご覧ください。
