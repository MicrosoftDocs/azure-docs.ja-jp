---
title: Azure CLI を使用して Azure Firewall をデプロイして構成する
description: この記事では、Azure CLI を使用して Azure Firewall をデプロイして構成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 4d611a6d575fbc94a555006882f77e5a31753164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051464"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Azure CLI を使用して Azure Firewall をデプロイして構成する

アウトバウンド ネットワーク アクセスを制御することは、ネットワーク セキュリティ プラン全体の重要な要素です。 たとえば、Web サイトへのアクセスを制限することができます。 また、アクセスできるアウトバウンドの IP アドレスとポートを制限することもできます。

Azure サブネットから外に向かうアウトバウンド ネットワーク アクセスを制御する方法の 1 つとして、Azure Firewall の使用が挙げられます。 Azure Firewall を使用して、次のルールを構成できます。

* アプリケーション ルール: サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を定義します。 FQDN には [SQL インスタンスを含める](sql-fqdn-filtering.md)こともできます。
* ネットワーク ルール: 送信元アドレス、プロトコル、宛先ポート、送信先アドレスを定義します。

ネットワーク トラフィックは、サブネットの既定ゲートウェイとしてのファイアウォールにルーティングしたときに、構成されているファイアウォール ルールに制約されます。

この記事では、デプロイしやすいよう単純化して、3 つのサブネットを含んだ単一の VNet を作成します。 運用環境のデプロイには、[ハブ アンド スポーク モデル](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)が推奨されます。 ファイアウォールは独自の VNet に含まれています。 ワークロード サーバーは、1 つ以上のサブネットを含む同じリージョンのピアリングされた VNet に配置されます。

* **AzureFirewallSubnet** - このサブネットにファイアウォールが存在します。
* **Workload-SN** - このサブネットにはワークロード サーバーがあります。 このサブネットのネットワーク トラフィックは、ファイアウォールを通過します。
* **Jump-SN** - このサブネットには "ジャンプ" サーバーがあります。 ジャンプ サーバーには、リモート デスクトップを使用して接続できるパブリック IP アドレスがあります。 そこから、(別のリモート デスクトップを使用して) ワークロード サーバーに接続できます。

![チュートリアルのネットワーク インフラストラクチャ](media/tutorial-firewall-rules-portal/Tutorial_network.png)

この記事では、次の方法について説明します。

* テスト ネットワーク環境を設定する
* ファイアウォールをデプロイする
* 既定のルートを作成する
* www.google.com へのアクセスを許可するようにアプリケーションを構成する
* 外部 DNS サーバーへのアクセスを許可するようにネットワーク ルールを構成する
* ファイアウォールをテストする

必要に応じて、この手順は [Azure portal](tutorial-firewall-deploy-portal.md) または [Azure PowerShell](deploy-ps.md) を使用して完了できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="set-up-the-network"></a>ネットワークのセットアップ

最初に、ファイアウォールをデプロイするために必要なリソースを含めるリソース グループを作成します。 次に、VNet、サブネット、およびテスト サーバーを作成します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

このリソース グループには、デプロイのためのすべてのリソースが含まれます。

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>VNet を作成する

この仮想ネットワークには 3 つのサブネットが含まれています。

> [!NOTE]
> AzureFirewallSubnet サブネットのサイズは /26 です。 サブネットのサイズの詳細については、「[Azure Firewall に関する FAQ](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size)」を参照してください。

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次に、ジャンプおよびワークロード仮想マシンを作成し、適切なサブネットに配置します。
メッセージが表示されたら、仮想マシンのパスワードを入力します。

Srv-Jump 仮想マシンを作成します。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



特定の DNS サーバーの IP アドレスを指定し、テストに使用するパブリック IP アドレスはなしで Srv-Work の NIC を作成します。

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

次に、ワークロード仮想マシンを作成します。
メッセージが表示されたら、仮想マシンのパスワードを入力します。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>ファイアウォールをデプロイする

次に、仮想ネットワークにファイアウォールをデプロイします。

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

プライベート IP アドレスをメモします。 後で既定のルートを作成するときにこれを使用します。

## <a name="create-a-default-route"></a>既定のルートを作成する

BGP ルート伝達が無効になっているテーブルを作成します

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

ルートを作成します。

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

ルート テーブルをサブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>アプリケーション ルールを構成する

このアプリケーション ルールでは www.google.com へのアウトバウンド アクセスが許可されます。

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 詳細については、[インフラストラクチャ FQDN](infrastructure-fqdns.md) に関する記事を参照してください。

## <a name="configure-a-network-rule"></a>ネットワーク ルールを構成する

このネットワーク ルールにより、ポート 53 (DNS) で 2 つの IP アドレスへのアウトバウンド アクセスが許可されます。

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>ファイアウォールをテストする

今度は、ファイアウォールをテストして、想定したように機能することを確認します。

1. **Srv-Work** の仮想マシンのプライベート IP アドレスをメモしてください。

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. リモート デスクトップを **Srv-Jump** 仮想マシンに接続し、サインインします。 そこから **Srv-Work** のプライベート IP アドレスへのリモート デスクトップ接続を開いて、サインインします。

3. **[SRV-Work]** で PowerShell ウィンドウを開き、次のコマンドを実行します。

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   どちらのコマンドでも、DNS クエリがファイアウォールを通過していることを示す回答を返します。

1. 次のコマンドを実行します。

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com` 要求は成功し、`www.microsoft.com` 要求は失敗します。 これは、ファイアウォール ルールが予想どおりに動作していることを示します。

これで、ファイアウォール ルールが動作していることを確認できました。

* 構成された外部 DNS サーバーを使用して DNS 名を解決できます。
* 1 つの許可された FQDN は参照できますが、それ以外は参照できません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォール リソースは、次のチュートリアルのために残しておいてもかまいませんが、不要であれば、**Test-FW-RG** リソース グループを削除して、ファイアウォール関連のすべてのリソースを削除してください。

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure Firewall のログを監視する](./firewall-diagnostics.md)