---
title: Azure Private Link を使用して、ネットワークを Azure Automation に安全に接続する
description: Azure Private Link を使用して、ネットワークを Azure Automation に安全に接続する
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: c81d9774dccf8c02d2eab7b1ebbb69e6671869e8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423798"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>Azure Private Link を使用して、ネットワークを Azure Automation に安全に接続する (プレビュー)

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、Automation サービスを実質的に VNet に取り込みます。 VNet 上のマシンと Automation アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットからの露出を排除します。

たとえば、発信インターネット アクセスを無効にした VNet があるとします。 ただし、Automation アカウントにプライベートにアクセスし、Hybrid Runbook Worker 上で Webhook、State Configuration、runbook ジョブなどの Automation 機能を使用する必要があります。 さらに、VNET を通じてのみユーザーが Automation アカウントにアクセスできるようにしたいと考えています。  プライベート エンドポイントをデプロイすると、これらの目標を達成することができます。

この記事では、Automation アカウントでプライベート エンドポイントを使用するタイミングとその設定方法 (プレビュー) について説明します。

![Azure Automation のプライベート リンクの概念の概要](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Azure Automation でのプライベート リンクのサポート (プレビュー) は、Azure Commercial クラウドと Azure US Government クラウドでのみご利用いただけます。

## <a name="advantages"></a>長所

Private Link を使用すると、次のことができます。

- パブリック ネットワーク アクセスを開かずに Azure Automation にプライベートに接続する。
- パブリック ネットワーク アクセスを開かずに Azure Monitor Log Analytics ワークスペースにプライベートに接続する。

    >[!NOTE]
    >これが必要になるのは、ジョブ データを転送するために Automation アカウントが Log Analytics ワークスペースにリンクされていて、[更新の管理]、[変更履歴とインベントリ]、[状態の構成]、[作業時間外に VM を開始/停止する] などの機能が有効になっている場合です。 Azure Monitor のプライベート リンクの詳細については、「[Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](../../azure-monitor/platform/private-link-security.md)」を参照してください。

- 承認されたプライベート ネットワーク経由でのみ Automation データにアクセスできるようにする。
- プライベート エンドポイント経由で接続する Azure Automation リソースを定義して、プライベート ネットワーク経由のデータ流出を防ぐ。
- ExpressRoute と Private Link を使用して、オンプレミスのプライベート ネットワークを Azure Automation に安全に接続する。
- すべてのトラフィックを Microsoft Azure のバックボーン ネットワーク内に収める。

詳細については、[Private Link の主な利点](../../private-link/private-link-overview.md#key-benefits)に関するページを参照してください。

## <a name="how-it-works"></a>しくみ

Azure Automation プライベート リンクは、1 つまたは複数のプライベート エンドポイント (および、それらが含まれている仮想ネットワーク) を、Automation アカウント リソースに接続します。 これらのエンドポイントは、Webhook を使用して Runbook を開始するマシン、Hybrid Runbook Worker ロールをホストするマシン、および DSC ノードです。

Automation のプライベート エンドポイントを作成した後で、公開される Automation URL (ユーザーつまりコンピューターが直接アクセスできる) のそれぞれが、VNet の 1 つのプライベート エンドポイントにマップされます。

プレビュー リリースの一部として、Automation アカウントは、プライベート エンドポイントを使用してセキュリティ保護された Azure リソースにはアクセスできません。 たとえば、Azure Key Vault、Azure SQL、Azure Storage アカウントなどです。

### <a name="webhook-scenario"></a>Webhook のシナリオ

Webhook URL に対して POST を行うことで、Runbook を開始できます。 たとえば、URL は `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d` のようになります。

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc) のシナリオ

State Configuration は、任意のクラウドまたはオンプレミスのデータセンターのノードについて PowerShell Desired State Configuration (DSC) の構成を記述、管理、およびコンパイルできる Azure 構成管理サービスを提供します。

コンピューター上のエージェントは DSC サービスに登録され、サービス エンドポイントを使用して DSC 構成をプルします。 エージェント サービス エンドポイントは `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` のようになります。

パブリック エンドポイントとプライベート エンドポイントの URL は同じですが、プライベート リンクが有効になっている場合は、プライベート IP アドレスにマップされます。

## <a name="planning-based-on-your-network"></a>ネットワークに基づく計画

Automation アカウント リソースを設定する前に、ネットワークの分離要件を検討してください。 パブリック インターネットへの仮想ネットワークへのアクセスと、Automation アカウントに対するアクセス制限を評価します (Automation アカウントと統合されている場合は、Azure Monitor ログに対するプライベート リンク グループ スコープの設定も含みます)。 また、サポートされている機能が問題なく動作することを確保するためのプランの一部として、Automation サービス [DNS レコード](./automation-region-dns-records.md)のレビューも含みます。

### <a name="connect-to-a-private-endpoint"></a>プライベート エンドポイントへの接続

ネットワークに接続するためのプライベート エンドポイントを作成します。 これは、[Azure portal プライベート リンク センター](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)で作成できます。 PublicNetworkAccess とプライベート リンクに対する変更が適用されてから、有効になるまでに最大 35 分かかることがあります。

このセクションでは、Automation アカウントのプライベート エンドポイントを作成します。

1. 画面の左上で、 **[リソースの作成] > [ネットワーキング] > [プライベート リンク センター (プレビュー)]** を選択します。

2. **[プライベート リンク センター - 概要]** の **[サービスへのプライベート接続を構築する]** オプションで、 **[開始]** を選択します。

3. **[仮想マシンの作成 - 基本]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[myResourceGroup]** を選択します。 これは前のセクションで作成しました。  |
    | **インスタンスの詳細** |  |
    | 名前 | *PrivateEndpoint* を入力します。 |
    | リージョン | **YourRegion** を選択します。 |
    |||

4. **[Next:リソース]** を選択します。

5. **[プライベート エンドポイントの作成 - リソース]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | 自分のディレクトリ内の Azure リソースに接続するように選択します。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **Microsoft.Automation/automationAccounts** を選択します。 |
    | リソース |*myAutomationAccount* を選択します。|
    |ターゲット サブリソース |シナリオに応じて *Webhook* または *DSCAndHybridWorker* を選択します。|
    |||

6. **[Next:構成]** を選択します。

7. **[プライベート エンドポイントの作成 - 構成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| *[MyVirtualNetwork]* を選択します。 |
    | Subnet | *[mySubnet]* を選択します。 |
    |**プライベート DNS 統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 |
    |プライベート DNS ゾーン |*(New)privatelink.azure-automation.net* を選択します。 |
    |||

8. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。

9. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

**[プライベート リンク センター (プレビュー)]** で、 **[プライベート エンドポイント]** を選択して、プライベート リンク リソースを表示します。

![Automation リソース プライベート リンク](./media/private-link-security/private-link-automation-resource.png)

リソースを選択すると、すべての詳細が表示されます。 これにより、Automation アカウントの新しいプライベート エンドポイントが作成され、仮想ネットワークのプライベート IP アドレスが割り当てられます。 **[接続状態]** に **[承認済み]** と表示されていることを確認します。

同様に、State Configuration (agentsvc) と Hybrid Runbook Worker ジョブ ランタイム (jrds) 用の一意の完全修飾ドメイン名 (FQDN) が作成されます。 それぞれには VNet から別の IP が割り当てられ、 **[接続状態]** に **[承認済み]** と表示されます。

サービスのコンシューマーに Automation のリソースに対する RBAC アクセス許可がある場合、コンシューマーは自動の承認方法を選択できます。 この場合、要求が Automation プロバイダーのリソースに到達したときにサービス プロバイダーからのアクションは必要なく、接続が自動的に承認されます。

## <a name="set-public-network-access-flags"></a>パブリック ネットワーク アクセスのフラグを設定する

Automation アカウントの構成によって、すべてのパブリック構成を拒否し、プライベート エンドポイント経由の接続のみを許可するようにして、ネットワーク セキュリティを強化できます。 Automation アカウントへのアクセスを VNet 内のみに制限し、パブリック インターネットからのアクセスを許可しない場合は、`publicNetworkAccess` プロパティを `$false` に設定できます。

**[パブリック ネットワーク アクセス]** 設定を `$false` に設定すると、プライベート エンドポイント経由の接続のみが許可され、パブリック エンドポイント経由のすべての接続は Unauthorized というエラー メッセージと HTTP 状態コード 401 が表示されて拒否されます。 

次の PowerShell スクリプトは、**パブリック ネットワーク アクセス** プロパティを Automation アカウント レベルで `Get` および `Set` する方法を示しています。

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS の構成

接続文字列の一部として完全修飾ドメイン名 (FQDN) を使用してプライベート リンク リソースに接続する場合は、割り当てられたプライベート IP アドレスに解決されるように DNS 設定を正しく構成することが重要です。 既存の Azure サービスには、パブリック エンドポイント経由で接続するときに使用する DNS 構成が既に存在している場合があります。 プライベート エンドポイントを使用して接続するには、DNS 構成を確認して更新する必要があります。

プライベート エンドポイントに関連付けられたネットワーク インターフェイスには、特定のプライベート リンク リソースに割り当てられた FQDN やプライベート IP アドレスなど、DNS を構成するために必要な情報の完全なセットが含まれています。

次のオプションを使用して、プライベート エンドポイントの DNS 設定を構成できます。

* ホスト ファイルを使用する (テストにのみ推奨)。 仮想マシン上のホスト ファイルを使用して、最初に名前解決のために DNS を使用してオーバーライドすることができます。

* [プライベート DNS ゾーン](../../dns/private-dns-privatednszone.md)を使用する。 プライベート DNS ゾーンを使用して、特定のプライベート エンドポイントの DNS 解決をオーバーライドすることができます。 プライベート DNS ゾーンを自分の仮想ネットワークにリンクして、特定のドメインを解決することができます。 仮想マシン上のエージェントがプライベート エンドポイント経由で通信できるようにするには、プライベート DNS レコードを `privatelink.azure-automation.net` として作成します。 新しい DNS *A* レコード マッピングをプライベート エンドポイントの IP に追加します。

* DNS フォワーダーを使用する (オプション)。 DNS フォワーダーを使用して、特定のプライベート リンク リソースの DNS 解決をオーバーライドすることができます。 お使いの [DNS サーバー](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)が仮想ネットワーク上にホストされている場合は、プライベート DNS ゾーンを使用する DNS 転送規則を作成して、すべてのプライベート リンク リソースの構成を簡略化することができます。

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../../private-link/private-endpoint-dns.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは](../../private-link/private-endpoint-overview.md)」を参照してください。