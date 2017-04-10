---
title: "PowerShell を使用した Azure DNS での逆引き DNS レコードの管理 | Microsoft Docs"
description: "Azure DNS では、Resource Manager で PowerShell を使用して、Azure サービスの逆引き DNS レコードまたは PTR レコードを管理できます"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 78de3b8dd2d8bd0992bfbacb1079825dca486442
ms.lasthandoff: 02/10/2017


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>PowerShell を使用した Azure サービスの逆引き DNS レコードの管理

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

クラシック デプロイメント モデルの詳細については、「[Azure PowerShell を使用して Azure サービス (クラシック) の逆引き DNS レコードを管理する方法](dns-reverse-dns-record-operations-classic-ps.md)」を参照してください。

## <a name="validation-of-reverse-dns-records"></a>逆引き DNS レコードの検証

Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレス リソースの "Fqdn"、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" と同じである (例: "ReverseFqdn" が "contosoapp1.northus.cloudapp.azure.com." である)。
* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレスの名前または IP か、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" または IP に対して前方解決される (例: "ReverseFqdn" が、"contosoapp1.northus.cloudapp.azure.com" の CName エイリアスの "app1.contoso.com" である )。

検証チェックが実行されるのは、パブリック IP アドレスの逆引き DNS プロパティの設定時または変更時のみです。 定期的な再検証は行われません。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>既存の Public IP Addresses への逆引き DNS の追加

逆引き DNS を既存のパブリック IP アドレスに追加するには、`Set-AzureRmPublicIpAddress` コマンドレットを使います。

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

まだ DNS 名を持っていない既存のパブリック IP アドレスに逆引き DNS を追加する場合は、DNS 名も指定する必要があります。 これは、"Set-AzureRmPublicIpAddress" コマンドレットを使って行うことができます。

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>逆引き DNS でのパブリック IP アドレスの作成

逆引き DNS プロパティを指定して新しいパブリック IP アドレスを追加するには、`New-AzureRmPublicIpAddress` コマンドレットを使います。

```powershell
New-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>既存の Public IP Addresses の逆引き DNS の表示

既存のパブリック IP アドレスで構成されている値を確認するには、`Get-AzureRmPublicIpAddress` コマンドレットを使います。

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS"
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>既存のパブリック IP アドレスの逆引き DNS を削除する

既存のパブリック IP アドレスの逆引き DNS プロパティを削除するには、`Set-AzureRmPublicIpAddress` コマンドレットを使います。 この操作を行うには、ReverseFqdn プロパティの値を空白にします。

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


