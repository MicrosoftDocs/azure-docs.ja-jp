---
title: Azure 上に Ethereum Proof-of-Authority Consortium ソリューション テンプレートをデプロイする
description: Etherereum Proof-of-Authority Consortium ソリューションを使用して Azure 上にマルチメンバー コンソーシアム型 Ethereum ネットワークをデプロイして構成する
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097943"
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

既定では、サブスクリプションとクラウド間の接続を簡単にするために、RPC とピアリング エンドポイントにはパブリック IP を使用してアクセスできます。 アプリケーション レベルのアクセス制御には、[Parity のアクセス許可コントラクト](https://openethereum.github.io/Permissioning.html)を使用できます。 サブスクリプション間接続に VNet ゲートウェイを利用する、VPN の背後にデプロイされたネットワークがサポートされています。 VPN と VNet のデプロイはより複雑であるため、ソリューションのプロトタイプを作成するときは、パブリック IP モデルから始めることをお勧めします。

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
1. *メンバー A* が、*メンバー B* と *メンバー C* にコンソーシアムの URL を提供します
1. *メンバー B* と *メンバー C* が、自分たちのパブリック Ethereum アドレスと *メンバー A* のコンソーシアムの URL を入力して Ethereum PoA をデプロイします
1. *メンバー A* が、*メンバー B* を管理者とする投票をします
1. *メンバー A* と *メンバー B* の両方が、*メンバー C* を管理者とする投票をします

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
* *[Ethereum Settings]\(Ethereum の設定\)* で提供された *コンソーシアム データの URL* を使用します
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

## <a name="support-and-feedback"></a>サポートとフィードバック<a id="tutorials"></a>

Azure Blockchain の最新情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)をご覧ください。Azure Blockchain エンジニアリング チームからの情報やブロックチェーン サービスの内容に関する最新の情報を把握することができます。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)で投稿またはアイデアに投票してください。

### <a name="community-support"></a>コミュニティ サポート

Microsoft のエンジニアや Azure Blockchain コミュニティのエキスパートと交流できます。

* [Microsoft Q&A 質問ページ](/answers/topics/azure-blockchain-workbench.html)。 ブロックチェーン テンプレートに関するエンジニアリング サポートは、デプロイに関する問題に限定されています。
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>次のステップ

その他の Azure Blockchain ソリューションについては、[Azure Blockchain のドキュメント](../index.yml)をご覧ください。
