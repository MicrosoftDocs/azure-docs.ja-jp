---
title: Azure DNS での DNS ゾーンの管理 - PowerShell | Microsoft Docs
description: Azure Powershell を使用して DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6abcca9d9888dc8968d7233e7aee6cd76aa215f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965750"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell を使用して DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [ポータル](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure クラシック CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

この記事では、Azure PowerShell を使用して DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-operations-dnszones-cli.md) または Azure Portal を使用して、管理することもできます。

このガイドでは、特にパブリック DNS ゾーンについて説明します。 Azure PowerShell を使用する Azure DNS での Private Zones の管理については、「[PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)」を参照してください。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzureRmDnsZone` コマンドレットを使用して作成します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

次の例では、*project = demo* と *env = test* の 2 つ [Azure Resource Manager タグ](dns-zones-records.md#tags)を含む DNS ゾーンの作成方法を示します。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS は、プライベート DNS ゾーンもサポートします。  プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。 プライベート DNS ゾーンを作成する例については、「[PowerShell で Azure DNS プライベート ゾーンの使用を開始する](./private-dns-getstarted-powershell.md)」を参照してください。

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `Get-AzureRmDnsZone` コマンドレットを使用します。 この操作により、Azure DNS 内の既存のゾーンに対応する DNS ゾーン オブジェクトが返されます。 このオブジェクトには、ゾーンに関するデータ (レコード セットの数など) が含まれますが、レコード セット自体は含まれません (`Get-AzureRmDnsRecordSet` を参照)。

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

`Get-AzureRmDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

`Get-AzureRmDnsZone` でゾーン名とリソース グループ名の両方を省略すると、Azure サブスクリプションにすべてのゾーンを列挙できます。

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `Set-AzureRmDnsZone`を使用して行うことができます。 このコマンドレットによって、ゾーン内の DNS レコード セットが更新されることはありません (「[DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現在、書き込み可能ゾーンのプロパティは、[ゾーンのリソースの Azure Resource Manager の "タグ"](dns-zones-records.md#tags) に限定されています。

DNS ゾーンを更新するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>ゾーン名とリソース グループを使用してゾーンを指定する

この手法では、既存のゾーン タグが、指定された値に置き換えられます。

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

この手法では、既存のゾーン オブジェクトが取得された後、タグが変更されたうえで、変更がコミットされます。 この方法では既存のタグを保持できます。

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

$zone オブジェクトで `Set-AzureRmDnsZone` を使用すると、同時変更が上書きされないように [Etag チェック](dns-zones-records.md#etags)が使用されます。 オプションの `-Overwrite` スイッチを使用すると、これらのチェックを抑制できます。

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンを削除するには、`Remove-AzureRmDnsZone` コマンドレットを使用します。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 この操作を元に戻すことはできません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。


DNS ゾーンを削除するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>ゾーン名とリソース グループ名を使用してゾーンを指定する

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

削除するゾーンを指定するには、`Get-AzureRmDnsZone` によって返された `$zone` オブジェクトを使用します。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

ゾーン オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

`Set-AzureRmDnsZone` と同様に、`$zone` オブジェクトを使用してゾーンを指定すると、Etag チェックによって同時変更の削除を防止することができます。 これらのチェックを抑制するには、`-Overwrite` スイッチを使用します。

## <a name="confirmation-prompts"></a>確認のプロンプト

`New-AzureRmDnsZone`、`Set-AzureRmDnsZone`、および `Remove-AzureRmDnsZone` コマンドレットは、いずれも確認のプロンプトをサポートしています。

`New-AzureRmDnsZone` と `Set-AzureRmDnsZone` の両方が、`$ConfirmPreference` PowerShell 設定変数の値が `Medium` 以下である場合に、確認のプロンプトを表示します。 DNS ゾーン削除の影響は大きくなる可能性があるため、`Remove-AzureRmDnsZone` コマンドレットは、`$ConfirmPreference` PowerShell 変数の値が `None` 以外の場合に確認のプロンプトを表示します。

`$ConfirmPreference` の既定値は `High` であるため、既定では `Remove-AzureRmDnsZone` のみが確認のプロンプトを表示します。

現在の `$ConfirmPreference` 設定は `-Confirm` パラメーターを使用してオーバーライドできます。 `-Confirm` または `-Confirm:$True` を指定した場合は、コマンドレットによって実行前に確認のプロンプトが表示されます。 `-Confirm:$False` を指定した場合は、コマンドレットによって確認のプロンプトが表示されません。

`-Confirm` と `$ConfirmPreference` の詳細については、「[About Preference Variables (設定変数について)](/powershell/module/microsoft.powershell.core/about/about_preference_variables)」を参照してください。

## <a name="next-steps"></a>次のステップ

DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](dns-operations-recordsets.md)をご覧ください。
<br>
Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。
<br>
[Azure DNS PowerShell のリファレンス ドキュメント](/powershell/module/azurerm.dns)を確認します。