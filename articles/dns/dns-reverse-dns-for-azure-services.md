---
title: Azure サービスの逆引き DNS - Azure DNS
description: このラーニング パスでは、Azure でホストされているサービスに対する逆引き DNS 参照の構成を始めます。
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: c72e17e601ebf87b0f344a4723159ae22abc81d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919886"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Azure でホストされているサービスの逆引き DNS を構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事では、Azure でホストされているサービスの逆引き DNS 参照を構成する方法について説明します。

Azure のサービスは、Azure によって割り当てられて、Microsoft によって所有されている IP アドレスを使います。 対応する Microsoft 所有の逆引き DNS 参照ゾーンに、これらの逆引き DNS レコード (PTR レコード) を作成する必要があります。 この記事では、その方法について説明します。

このシナリオを、[Azure DNS で割り当てられた IP アドレス範囲の逆引き DNS 参照ゾーンをホストする](dns-reverse-dns-hosting.md)機能と混同しないようにしてください。 この場合、逆引き参照ゾーンによって表される IP 範囲を、通常は ISP が、組織に割り当てる必要があります。

この記事を読む前に、[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md)について理解しておいてください。

Azure DNS では、コンピューティング リソース (仮想マシン、仮想マシンのスケール セット、Service Fabric クラスターなど) は、PublicIpAddress リソースによって公開されます。 逆引き DNS 参照は、PublicIpAddress の "ReverseFqdn" プロパティを使って構成します。


現在、Azure App Service と Application Gateway については逆引き DNS はサポートされていません。

## <a name="validation-of-reverse-dns-records"></a>逆引き DNS レコードの検証

サード パーティが、DNS ドメインに対する Azure サービス マッピングの逆引き DNS レコードを作成することができてはなりません。 これを防ぐため、Azure では、逆引き DNS レコードで指定されているドメイン名が、同じ Azure サブスクリプション内の PublicIpAddress またはクラウド サービスの DNS 名または IP アドレスと同じか、それに解決される場合にのみ、逆引き DNS レコードの作成が許可されます。

この検証は、逆引き DNS レコードが設定または変更されるときにのみ実行されます。 定期的な再検証は行われません。

たとえば、PublicIpAddress リソースの DNS 名が contosoapp1.northus.cloudapp.azure.com であり、IP アドレスが 23.96.52.53 であるものとします。 PublicIpAddress の ReverseFqdn は次のように指定できます。
* PublicIpAddress の DNS 名 (contosoapp1.northus.cloudapp.azure.com)
* 同じサブスクリプション内の異なる PublicIpAddress の DNS 名 (contosoapp2.westus.cloudapp.azure.com など)
* バニティ DNS 名 (app1.contoso.com など)。ただし、この名前が contosoapp1.northus.cloudapp.azure.com または同じサブスクリプション内の異なる PublicIpAddress に対する CNAME として、"*最初に*" 構成されている場合。
* バニティ DNS 名 (app1.contoso.com など)。ただし、この名前が IP アドレス 23.96.52.53 または同じサブスクリプション内の異なる PublicIpAddress の IP アドレスに対する A レコードとして、"*最初に*" 構成されている場合。

同じ制約が、Cloud Services の逆引き DNS にも適用されます。


## <a name="reverse-dns-for-publicipaddress-resources"></a>PublicIpAddress リソースの逆引き DNS

ここでは、Azure PowerShell、Azure CLI、または Azure CLI を使って、Resource Manager デプロイ モデルに PublicIpAddress リソースに対する逆引き DNS を構成する方法の詳細な手順を説明します。 Azure Portal を使った PublicIpAddress リソースの逆引き DNS の構成は、現在はサポートされていません。

現在、Azure は、IPv4 の PublicIpAddress リソースの逆引き DNS のみをサポートしています。 IPv6 についてはサポートされていません。

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>既存の PublicIpAddresses に逆引き DNS を追加する

#### <a name="powershell"></a>PowerShell

既存の PublicIpAddress に逆引き DNS を更新するには

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

まだ DNS 名を持っていない既存の PublicIpAddress に逆引き DNS を追加するには、DNS 名も指定する必要があります。

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

既存の PublicIpAddress に逆引き DNS を追加するには:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

まだ DNS 名を持っていない既存の PublicIpAddress に逆引き DNS を追加するには、DNS 名も指定する必要があります。

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

既存の PublicIpAddress に逆引き DNS を追加するには:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

まだ DNS 名を持っていない既存の PublicIpAddress に逆引き DNS を追加するには、DNS 名も指定する必要があります。

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>逆引き DNS でのパブリック IP アドレスの作成

既に指定されている逆引き DNS プロパティで新しい PublicIpAddress を作成するには:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>既存の PublicIpAddress の逆引き DNS を表示する

既存の PublicIpAddress に構成されている値を表示するには:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>既存のパブリック IP アドレスの逆引き DNS を削除する

既存の PublicIpAddress から逆引き DNS プロパティを削除するには:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Cloud Services の逆引き DNS を構成する

ここでは、Azure PowerShell を使って、クラシック デプロイ モデルで Cloud Services の逆引き DNS を構成する方法の詳細な手順についてを説明します。 Cloud Services に対する Azure Portal、Azure CLI、および Azure CLI からの逆引き DNS の構成はサポートされていません。

### <a name="add-reverse-dns-to-existing-cloud-services"></a>既存の Cloud Services への逆引き DNS の追加

既存の Cloud Service に逆引き DNS レコードを追加するには:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>逆引き DNS によるクラウド サービスの作成

既に指定されている逆引き DNS プロパティで新しい Cloud Service を作成するには:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>既存の Cloud Services の逆引き DNS の表示

既存の Cloud Service の逆引き DNS プロパティを表示するには:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>既存の Cloud Services からの逆引き DNS の削除

既存の Cloud Service から逆引き DNS プロパティを削除するには:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>よく寄せられる質問

### <a name="how-much-do-reverse-dns-records-cost"></a>逆引き DNS レコードのコストはどのくらいですか?

無料です。  逆引き DNS レコードまたはクエリに追加コストはかかりません。

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>逆引き DNS レコードはインターネットから解決しますか?

はい。 Azure サービスに逆引き DNS プロパティを設定すると、Azure は、すべてのインターネット ユーザーについて逆引き DNS レコードを解決するために必要なすべての DNS 委任と DNS ゾーンを管理します。

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Azure サービスに既定の逆引き DNS レコードは作成されますか?

いいえ。 逆引き DNS はオプトイン機能です。 既定の逆引き DNS レコードを構成しない設定の場合、レコードは作成されません。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完全修飾ドメイン名 (FQDN) の形式とは何ですか?

FQDN は順方向で指定します。末尾にはドットを指定する必要があります (例: "app1.contoso.com.")。

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>指定した逆引き DNS の検証チェックが失敗すると、どうなりますか?

逆引き DNS の検証チェックが失敗すると、逆引き DNS レコードを構成する操作が失敗します。 必要に応じて逆引き DNS 値を修正し、再試行してください。

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Azure App Service に対して逆引き DNS を構成できますか?

いいえ。 Azure App Service については逆引き DNS はサポートされていません。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Azure サービス用に複数の逆引き DNS を構成できますか?

いいえ。 Azure は、Azure Cloud Service または PublicIpAddress ごとに 1 つの逆引き DNS レコードをサポートしています。

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>IPv6 の PublicIpAddress リソースに対して逆引き DNS を構成できますか?

いいえ。 現在、Azure は、IPv4 の PublicIpAddress リソースおよび Cloud Services の逆引き DNS のみをサポートしています。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Azure コンピューティング サービスから外部ドメインに電子メールを送信できますか?

Azure のデプロイから電子メールを直接送信する技術的能力は、サブスクリプションの種類によって異なります。 Microsoft では、サブスクリプションの種類に関係なく、信頼できるメール リレー サービスを使用して発信メールを送信することをお勧めしています。 詳細については、「[Enhanced Azure Security for sending Emails – November 2017 Update](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)」 (電子メールを送信するための Azure セキュリティの強化 - 2017 年 11 月更新) を参照してください。

## <a name="next-steps"></a>次のステップ

逆引き DNS について詳しくは、[Wikipedia の逆引き DNS 参照](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)をご覧ください。
<br>
[Azure DNS で ISP によって割り当てられた IP アドレス範囲の逆引き参照ゾーンをホストする](dns-reverse-dns-for-azure-services.md)方法を学習してください。
