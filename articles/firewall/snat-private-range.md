---
title: Azure Firewall の SNAT プライベート IP アドレス範囲
description: SNAT の IP アドレス範囲を構成できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508930"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall の SNAT プライベート IP アドレス範囲

Azure Firewall では、パブリック IP アドレスへのすべてのアウトバウンド トラフィックに対して自動 SNAT が提供されます。 既定では、宛先 IP アドレスが [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) に従ったプライベート IP アドレス範囲内にある場合、Azure Firewall では、ネットワーク規則を使用した SNAT は行われません。 アプリケーション ルールは、宛先 IP アドレスに関係なく、常に[透過プロキシ](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)を使用して適用されます。

このロジックは、トラフィックをインターネットに直接ルーティングする場合に適しています。 ただし、[強制トンネリング](forced-tunneling.md)を有効にすると、SNAT によってインターネットへのトラフィックに AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスが適用され、対象のオンプレミスのファイアウォールからソースが隠されます。

組織でプライベート ネットワークに対してパブリック IP アドレス範囲を使用している場合、Azure Firewall は、SNAT を使用して、トラフィックのアドレスを AzureFirewallSubnet のいずれかのファイアウォール プライベート IP アドレスに変換します。 ただし、パブリック IP アドレス範囲の SNAT が **行われない** ように、Azure Firewall を構成することができます。 たとえば、個々の IP アドレスを指定するには、`192.168.1.10` のように指定します。 IP アドレスの範囲を指定するには、`192.168.1.0/24` のように指定します。

- 宛先 IP アドレスに関係なく、SNAT を **行わない** ように Azure Firewall を構成するには、プライベート IP アドレス範囲として **0.0.0.0/0** を使用します。 この構成では、Azure Firewall がトラフィックをインターネットに直接ルーティングすることはできません。 

- 宛先アドレスに関係なく、**常に** SNAT を行うようにファイアウォールを構成するには、プライベート IP アドレス範囲として **255.255.255.255/32** を使用します。

> [!IMPORTANT]
> 指定したプライベート アドレス範囲は、ネットワーク規則にのみ適用されます。 現時点では、アプリケーション ルールでは常に SNAT が使用されます。

> [!IMPORTANT]
> 独自のプライベート IP アドレス範囲を指定し、既定の IANA RFC 1918 アドレス範囲をそのまま使用する場合は、カスタム リストに IANA RFC 1918 の範囲がまだ含まれていることを確認してください。 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure PowerShell

Azure PowerShell を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

### <a name="new-firewall"></a>新しいファイアウォール

新しいファイアウォールの場合、Azure PowerShell コマンドレットは次のようになります。

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> `New-AzFirewall` を使用して Azure Firewall をデプロイするには、既存の VNet とパブリック IP アドレスが必要です。 デプロイの詳細なガイドについては、「[Azure PowerShell を使用して Azure Firewall のデプロイと構成を行う](deploy-ps.md)」を参照してください。

> [!NOTE]
> IANAPrivateRanges は Azure Firewall の現在の既定値に拡張されますが、他の範囲は追加されます。 プライベート範囲の指定で IANAPrivateRanges の既定値を維持するには、次の例に示すように、`PrivateRange` の指定に残す必要があります。

詳細については、「[New-AzFirewall](/powershell/module/az.network/new-azfirewall)」をご覧ください。

### <a name="existing-firewall"></a>既存のファイアウォール

既存のファイアウォールを構成するには、次の Azure PowerShell コマンドレットを使用します。

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure CLI

Azure CLI を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

### <a name="new-firewall"></a>新しいファイアウォール

新しいファイアウォールの場合、Azure CLI コマンドは次のようになります。

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure CLI コマンド `az network firewall create` を使用して Azure Firewall をデプロイするには、パブリック IP アドレスと IP 構成を作成するための追加の構成手順が必要です。 デプロイの詳細なガイドについては、「[Azure CLI を使用して Azure Firewall のデプロイと構成を行う](deploy-cli.md)」を参照してください。

> [!NOTE]
> IANAPrivateRanges は Azure Firewall の現在の既定値に拡張されますが、他の範囲は追加されます。 プライベート範囲の指定で IANAPrivateRanges の既定値を維持するには、次の例に示すように、`PrivateRange` の指定に残す必要があります。

### <a name="existing-firewall"></a>既存のファイアウォール

既存のファイアウォールを構成するには、Azure CLI コマンドは次のようになります。

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT のプライベート IP アドレス範囲を構成する - ARM テンプレート

ARM テンプレートのデプロイ中に SNAT を構成するには、`additionalProperties` プロパティに次を追加します。

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT のプライベート IP アドレス範囲を構成する - Azure portal

Azure portal を使用して、ファイアウォールのプライベート IP アドレス範囲を指定できます。

1. 対象のリソース グループを選択し、ファイアウォールを選択します。
2. **[概要]** ページの **[プライベート IP 範囲]** で、既定値 **[IANA RFC 1918]** を選択します。

   **[Edit Private IP Prefixes]\(プライベート IP プレフィックスの編集\)** ページが開きます。

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="[Edit private IP prefixes]\(プライベート IP プレフィックスの編集\)":::

1. 既定では、 **[IANAPrivateRanges]** が構成されています。
2. お使いの環境に合わせてプライベート IP アドレス範囲を編集し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall の強制トンネリング](forced-tunneling.md)について学習します。