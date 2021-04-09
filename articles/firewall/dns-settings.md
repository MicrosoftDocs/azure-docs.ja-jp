---
title: Azure Firewall の DNS 設定
description: DNS サーバーと DNS プロキシ設定を使用して Azure Firewall を構成できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546246"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall の DNS 設定

カスタム DNS サーバーを構成して、Azure Firewall の DNS プロキシを有効にすることができます。 これらの設定は、ファイアウォールをデプロイするときに構成するか、後で **[DNS 設定]** ページから構成します。

## <a name="dns-servers"></a>DNS サーバー

DNS サーバーでは、ドメイン名から IP アドレスに管理と解決が行われます。 Azure Firewall では、名前解決に Azure DNS が既定で使用されます。 **DNS サーバー** 設定を使用すると、Azure Firewall の名前解決に対して独自の DNS サーバーを構成できます。 1 台のサーバーまたは複数台のサーバーを構成できます。

> [!NOTE]
> Azure Firewall Manager を使用して管理される Azure Firewall インスタンスの場合、DNS 設定は関連する Azure Firewall ポリシー内に構成されます。

### <a name="configure-custom-dns-servers---azure-portal"></a>カスタム DNS サーバーを構成する - Azure portal

1. Azure Firewall の **[設定]** で、 **[DNS 設定]** を選択します。
2. **[DNS サーバー]** では、仮想ネットワークで以前に指定した既存の DNS サーバーを入力または追加できます。
3. **[保存]** を選択します。

ファイアウォールによって、名前解決のために、指定された DNS サーバーに DNS トラフィックが送信されるようになりました。

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS サーバーの設定を示すスクリーンショット。":::

### <a name="configure-custom-dns-servers---azure-cli"></a>カスタム DNS サーバーを構成する - Azure CLI

次の例では、Azure CLI を使用して、カスタム DNS サーバーで Azure Firewall を更新します。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> コマンド `az network firewall` を使用するには、Azure CLI 拡張機能 `azure-firewall` をインストールする必要があります。 コマンド `az extension add --name azure-firewall` を使用してインストールできます。 

### <a name="configure-custom-dns-servers---azure-powershell"></a>カスタム DNS サーバーを構成する - Azure PowerShell

次の例では、Azure PowerShell を使用して、カスタム DNS サーバーで Azure Firewall を更新します。

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS プロキシ

Azure Firewall が DNS プロキシとして機能するように構成できます。 DNS プロキシは、クライアント仮想マシンから DNS サーバーへの DNS 要求の仲介役です。 カスタム DNS サーバーを構成する場合は、DNS 解決の不一致を回避するために DNS プロキシを有効にして、ネットワーク ルールで FQDN (完全修飾ドメイン名) フィルタリングを有効にします。

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="カスタム DNS サーバーを使用した DNS プロキシ構成。":::


DNS プロキシを有効にしない場合、クライアントからの DNS 要求については、別の時刻に DNS サーバーに移動することもあれば、ファイアウォールのものとは異なる応答が返されることもあります。 DNS プロキシでは、不整合を回避するために、クライアント要求のパスに Azure Firewall が配置されます。

Azure Firewall が DNS プロキシの場合、次の 2 種類のキャッシュ関数を使用できます。

- **正のキャッシュ**: DNS 解決が成功しました。 ファイアウォールでは、パケットまたはオブジェクトの TTL (time to live) が使用されます。 

- **負のキャッシュ**: DNS 解決が、応答なし、または解決なしという結果になります。 ファイアウォールによって、この情報は 1 時間キャッシュされます。

DNS プロキシによって、すべての解決済み IP アドレスが FQDN からネットワーク ルールに格納されます。 ベスト プラクティスとして、1 つの IP アドレスに解決される FQDN を使用することをお勧めします。  

### <a name="dns-proxy-configuration"></a>DNS プロキシの構成

DNS プロキシの構成には、次の 3 つの手順が必要です。
1. Azure Firewall の [DNS 設定] で DNS プロキシを有効にします。
2. 必要に応じて、カスタム DNS サーバーを構成するか、指定された既定のものを使用します。
3. 仮想ネットワークの DNS サーバーの設定で、Azure Firewall のプライベート IP アドレスをカスタム DNS アドレスとして構成します。 この設定により、確実に DNS トラフィックが Azure Firewall に送信されるようになります。

#### <a name="configure-dns-proxy---azure-portal"></a>DNS プロキシを構成する - Azure portal

DNS プロキシを構成するには、ファイアウォールのプライベート IP アドレスを使用するように、仮想ネットワークの DNS サーバー設定を構成する必要があります。 次に、Azure Firewall の **[DNS 設定]** で、DNS プロキシを有効にします。

##### <a name="configure-virtual-network-dns-servers"></a>仮想ネットワークの DNS サーバーを構成する 

1. Azure Firewall インスタンス経由で DNS トラフィックがルーティングされる仮想ネットワークを選択します。
2. **[設定]** で、 **[DNS サーバー]** を選択します。
3. **[DNS サーバー]** で、 **[カスタム]** を選択します。
4. ファイアウォールのプライベート IP アドレスを入力します。
5. **[保存]** を選択します。
6. 新しい DNS サーバー設定が割り当てられるように、仮想ネットワークに接続されている VM を再起動します。 VM では、再起動されるまで、現在の DNS 設定が使用され続けます。

##### <a name="enable-dns-proxy"></a>DNS プロキシを有効にする

1. Azure Firewall インスタンスを選択します。
2. **[設定]** で、 **[DNS 設定]** を選択します。
3. 既定では、**DNS プロキシ** は無効になっています。 この設定が有効になると、ファイアウォールによって、ポート 53 がリッスンされ、構成済みの DNS サーバーに DNS 要求が転送されます。
4. **DNS サーバー** の構成を確認して、設定が環境に適していることを確認します。
5. **[保存]** を選択します。

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS プロキシの設定を示すスクリーンショット。":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS プロキシを構成する - Azure CLI

Azure CLI を使用すると、Azure Firewall で DNS プロキシ設定を構成できます。 また、それを使用することで、Azure Firewall を DNS サーバーとして使用するように仮想ネットワークを更新することもできます。

##### <a name="configure-virtual-network-dns-servers"></a>仮想ネットワークの DNS サーバーを構成する

次の例では、Azure Firewall を DNS サーバーとして使用するように仮想ネットワークを構成します。
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS プロキシを有効にする

次の例では、Azure Firewall の DNS プロキシ機能を有効にします。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS プロキシを構成する - Azure PowerShell

Azure PowerShell を使用すると、Azure Firewall で DNS プロキシ設定を構成できます。 また、それを使用することで、Azure Firewall を DNS サーバーとして使用するように仮想ネットワークを更新することもできます。

##### <a name="configure-virtual-network-dns-servers"></a>仮想ネットワークの DNS サーバーを構成する

次の例では、Azure Firewall を DNS サーバーとして使用するように仮想ネットワークを構成します。

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS プロキシを有効にする

次の例では、Azure Firewall の DNS プロキシ機能を有効にします。

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>次のステップ

[ネットワーク ルールでの FQDN フィルタリング](fqdn-filtering-network-rules.md)
