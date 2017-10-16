---
title: "Azure DNS へのドメインの委任 | Microsoft Docs"
description: "ドメインの委任を変更し、ドメインのホストに Azure DNS ネーム サーバーを使用する方法を説明します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.openlocfilehash: 33b3ec24432ff1268860b9a2e9d5098600a8dedc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="delegate-a-domain-to-azure-dns"></a>Azure DNS へのドメインの委任

Azure DNS を使用すると、DNS ゾーンをホストし、Azure のドメインの DNS レコードを管理できます。 ドメインに対する DNS クエリを Azure DNS に到達させるには、ドメインを親ドメインから Azure DNS に委任する必要があります。 Azure DNS はドメイン レジストラーではないことに注意してください。 この記事では、ドメインを Azure DNS に委任する方法について説明します。

レジストラーから購入したドメインの場合は、レジストラーから、これらの NS レコードを設定するオプションが提供されます。 ドメインを所有していなくても、Azure DNS 内にそのドメイン名で DNS ゾーンを作成できます。 ただし、レジストラーを使用して Azure DNS への委任を設定するには、ドメインを所有する必要があります。

たとえば、ドメイン "contoso.net" を購入し、Azure DNS に "contoso.net" という名前のゾーンを作成するとします。 レジストラーは、ドメインの所有者として、ドメインのネーム サーバー アドレス (つまり、NS レコード) を構成するオプションを提供します。 レジストラーはこれらの NS レコードを親ドメイン (この場合は ".net") に格納します。 その後は、クライアントが世界のどこにあっても、"contoso.net" 内の DNS レコードを解決しようとした時点で、Azure DNS ゾーン内のドメインに転送することができます。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure Portal にサインインします。
1. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-domain-delegation/dns.png)

1. **[DNS ゾーンの作成]** ブレードで次の値を入力してから、**[作成]** をクリックします。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|contoso.net|DNS ゾーンの名前です。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="retrieve-name-servers"></a>ネーム サーバーの取得

DNS ゾーンを Azure DNS に委任するには、事前にゾーンのネーム サーバー名を把握する必要があります。 Azure DNS は、ゾーンが作成されるたびに、プールからネーム サーバーを割り当てます。

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 **[すべてのリソース]** ブレードで **contoso.net** DNS ゾーンをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、[名前でフィルター] ボックスに「**contoso.net**」と入力すると、 目的のアプリケーション ゲートウェイがすぐに見つかります。 

1. [DNS ゾーン] ブレードでネーム サーバーを取得します。 この例では、ゾーン "contoso.net" に、ネーム サーバー "ns1-01.azure-dns.com"、"ns2-01.azure-dns.net"、"ns3-01.azure-dns.org"、"ns4-01.azure-dns.info" が割り当てられています。

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

割り当てられたネーム サーバーが含まれたゾーンに、権限のある NS レコードが自動的に作成されます。  Azure PowerShell または Azure CLI を使用してネーム サーバー名を確認する際は、これらのレコードを取得するだけで済みます。

次の例も、PowerShell と Azure CLI を使用して Azure DNS ゾーンのネーム サーバーを取得する手順を示したものです。

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

次の例は応答です。

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set show --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

次の例は応答です。

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>ドメインの委任

DNS ゾーンを作成してネーム サーバーを取得したところで、Azure DNS ネーム サーバーを使用して親ドメインを更新する必要があります。 各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。 レジストラーの DNS 管理ページで、NS レコードを編集し、その NS レコードを、Azure DNS で作成されたレコードに置き換えます。

ドメインを Azure DNS に委任する場合は、Azure DNS によって提供されるネーム サーバー名を使用する必要があります。 対象のドメインの名前に関係なく、4 つのネーム サーバー名すべてを使用することをお勧めします。 ドメインの委任では、対象のドメインと同じトップレベル ドメインがネーム サーバー名に使用される必要はありません。

Azure DNS ネーム サーバーの IP アドレスは後で変更される可能性があるため、これらの IP アドレスを指すために "グルー レコード" を使用しないでください。 独自のゾーンのネーム サーバー名 ("バニティ ネーム サーバー" と呼ばれることもあります) を使用した委任は、現在、Azure DNS ではサポートされていません。

## <a name="verify-name-resolution-is-working"></a>名前解決の動作確認

委任が完了したら、"nslookup" などのツールを使用してゾーンの SOA レコードを照会することで、名前解決が動作していることを確認できます (SOA レコードは、ゾーンの作成時にも自動的に作成されます)。

Azure DNS ネーム サーバーを指定する必要はありません。委任が正しく設定されている場合は、通常の DNS 解決プロセスで自動的にネーム サーバーが検出されます。

```
nslookup -type=SOA contoso.com
```

前のコマンドから返される応答のサンプルを次に示します。

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-sub-domains-in-azure-dns"></a>Azure DNS でのサブドメインの委任

別の子ゾーンを設定する必要がある場合は、Azure DNS でサブドメインを委任できます。 たとえば、Azure DNS で "contoso.net" を設定して委任した後、別の子ゾーン "partners.contoso.net" を設定するとします。

1. Azure DNS で子ゾーン "partners.contoso.net" を作成します。
2. 子ゾーンで権限のある NS レコードを検索し、Azure DNS で子ゾーンをホストするネーム サーバーを取得します。
3. 子ゾーンを指す親ゾーンで NS レコードを構成することで、子ゾーンを委任します。

### <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure Portal にサインインします。
1. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-domain-delegation/dns.png)

1. **[DNS ゾーンの作成]** ブレードで次の値を入力してから、**[作成]** をクリックします。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|partners.contoso.net|DNS ゾーンの名前です。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[既存のものを使用]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

### <a name="retrieve-name-servers"></a>ネーム サーバーの取得

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 **[すべてのリソース]** ブレードで **partners.contoso.net** DNS ゾーンをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、[名前でフィルター] ボックスに「**partners.contoso.net**」と入力すると、 目的の DNS ゾーンがすぐに見つかります。

1. [DNS ゾーン] ブレードでネーム サーバーを取得します。 この例では、ゾーン "contoso.net" に、ネーム サーバー "ns1-01.azure-dns.com"、"ns2-01.azure-dns.net"、"ns3-01.azure-dns.org"、"ns4-01.azure-dns.info" が割り当てられています。

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

割り当てられたネーム サーバーが含まれたゾーンに、権限のある NS レコードが自動的に作成されます。  Azure PowerShell または Azure CLI を使用してネーム サーバー名を確認する際は、これらのレコードを取得するだけで済みます。

### <a name="create-name-server-record-in-parent-zone"></a>親ゾーンでのネーム サーバー レコードの作成

1. Azure Portal で **contoso.net** DNS ゾーンに移動します。
1. **[+ レコード セット]** をクリックします。
1. **[レコード セットの追加]** ブレードで次の値を入力してから、**[OK]** をクリックします。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|partners|子 DNS ゾーンの名前です。|
   |**型**|NS|ネーム サーバー レコードを表す NS を使用します。|
   |**TTL**|1|有効期限です。|
   |**TTL の単位**|時間|有効期限の単位を時間に設定します。|
   |**ネーム サーバー**|<partners.contoso.net ゾーンから取得したネーム サーバー>|partners.contoso.net ゾーンから取得した 4 つのネーム サーバーをすべて入力します。 |

   ![Dns-nameserver](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>その他のツールによる Azure DNS でのサブドメインの委任

次の例では、Azure DNS で PowerShell と CLI を使用してサブドメインを委任する手順について説明します。

#### <a name="powershell"></a>PowerShell

これを PowerShell で行う例を次に示します。 同じ手順を、Azure Portal、またはクロスプラットフォームの Azure CLI を使用して実行できます。

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

`nslookup` を使用して子ゾーンの SOA レコードを検索することで、すべてが正しく設定されていることを確認します。

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

`partners.contoso.net` ゾーンのネーム サーバーを出力から取得します。

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

レコード セットと、各ネーム サーバーの NS レコードを作成します。

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したリソースをすべて削除するには、次の手順を実行します。

1. Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **contosorg** リソース グループをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**contosorg**」と入力すると、 目的のリソース グループがすぐに見つかります。
1. **contosorg** ブレードで **[削除]** ボタンをクリックします。
1. 削除の意図を確認するために、リソース グループの名前を入力するよう求められます。 リソース グループの名前として「*contosorg*」と入力し、**[削除]** をクリックします。 リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。削除する前に、リソース グループの内容を必ず確認してください。 まずリソース グループに含まれているすべてのリソースが削除された後、リソース グループそのものが削除されます。 このプロセスには数分かかります。

## <a name="next-steps"></a>次のステップ

[DNS ゾーンの管理](dns-operations-dnszones.md)

[DNS レコードの管理](dns-operations-recordsets.md)
