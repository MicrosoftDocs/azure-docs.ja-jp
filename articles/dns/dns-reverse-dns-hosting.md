---
title: Azure DNS での逆引き DNS 参照ゾーンのホスト
description: Azure DNS を使って IP アドレス範囲の逆引き DNS 参照ゾーンをホストする方法について説明します
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525129"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Azure DNS での逆引き DNS 参照ゾーンのホスト

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事では、割り当てられた IP アドレス範囲の逆引き DNS 参照ゾーンを Azure DNS でホストする方法について説明します。 逆引き参照ゾーンによって表される IP 範囲を、通常は ISP が、組織に割り当てる必要があります。

Azure サービスに割り当てられている Azure が所有する IP アドレスの逆引き DNS を構成するには、「[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)」を参照してください。

この記事を読む前に、[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md)について理解しておいてください。

この記事では、Azure portal、Azure PowerShell、Azure クラシック CLI、または Azure CLI を使って、最初の逆引き参照 DNS ゾーンとレコードを作成する手順について説明します。

## <a name="create-a-reverse-lookup-dns-zone"></a>逆引き参照 DNS ゾーンを作成する

1. [Azure portal](https://portal.azure.com) にサインインする
1. **[ハブ]** メニューで、 **[新規]**  >  **[ネットワーク]** の順に選択し、 **[DNS ゾーン]** を選択します。

   ![[DNS ゾーン] の選択](./media/dns-reverse-dns-hosting/figure1.png)

1. **[DNS ゾーンの作成]** ウィンドウで、DNS ゾーンの名前を入力します。 ゾーンの名前の構造は、IPv4 プレフィックスと IPv6 プレフィックスでは異なります。 [IPv4](#ipv4) または [IPv6](#ipv6) の手順を使って、ゾーンの名前を設定します。 完了したら、 **[作成]** を選択してゾーンを作成します。

### <a name="ipv4"></a>IPv4

IPv4 の逆引き参照ゾーンの名前は、それが表す IP アドレスの範囲に基づきます。 `<IPv4 network prefix in reverse order>.in-addr.arpa` という形式にする必要があります。 例については、「[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md#ipv4)」をご覧ください。

> [!NOTE]
> クラスレスの逆引き DNS 参照ゾーンを Azure DNS で作成する場合は、ゾーン名のスラッシュ (`/`) の代わりにハイフン (`-`) を使う必要があります。
>
> たとえば、IP アドレス範囲が 192.0.2.128/26 の場合は、ゾーン名として `128/26.2.0.192.in-addr.arpa` ではなく `128-26.2.0.192.in-addr.arpa` を使う必要があります。
>
> DNS 標準では両方の方法がサポートされていますが、Azure DNS では、スラッシュ (`/`) 文字を含む DNS ゾーン名はサポートされていません。

次の例では、`2.0.192.in-addr.arpa` という名前のクラス C 逆引き DNS ゾーンを、Azure Portal を使って Azure DNS に作成する方法を示します。

 ![2\.0.192.in-addr.arpa という名前のクラス C 逆引き DNS ゾーンを、Azure portal を使って Azure DNS に作成する方法を示すスクリーンショット。](./media/dns-reverse-dns-hosting/figure2.png)

**[リソース グループの場所]** では、リソース グループの場所を定義します。 これは、DNS ゾーンには影響を与えません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

次の例では、Azure PowerShell と Azure CLI を使用してこのタスクを行う方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

IPv6 の逆引き参照ゾーンの名前は、`<IPv6 network prefix in reverse order>.ip6.arpa` という形式にする必要があります。  例については、「[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md#ipv6)」をご覧ください。


次の例では、`0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` という名前の IPv6 逆引き DNS 参照ゾーンを、Azure Portal を使って Azure DNS に作成する方法を示します。

 ![ボックスに入力された [DNS ゾーンの作成] ウィンドウ](./media/dns-reverse-dns-hosting/figure3.png)

**[リソース グループの場所]** では、リソース グループの場所を定義します。 これは、DNS ゾーンには影響を与えません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

次の例では、Azure PowerShell と Azure CLI を使用してこのタスクを行う方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>逆引き DNS 参照ゾーンを委任する

逆引き DNS 参照ゾーンを作成したので、ゾーンが親ゾーンから委任されることを確認する必要があります。 DNS の委任によって、DNS 名前解決プロセスは、逆引き DNS 参照ゾーンをホストしているネーム サーバーを見つけることができます。 これにより、これらのネーム サーバーは、アドレス範囲内の IP アドレスについての DNS 逆引きクエリに応答できます。

前方参照ゾーンで DNS ゾーンを委任するプロセスについては、「[Azure DNS にドメインを委任する](dns-delegate-domain-azure-dns.md)」をご覧ください。 逆引き参照ゾーンの委任も同じように動作します。 唯一の違いは、ネーム サーバーを構成するときに、ドメイン名レジストラーではなく、IP 範囲を提供した ISP と協力する必要があることです。

## <a name="create-a-dns-ptr-record"></a>DNS PTR レコードを作成する

### <a name="ipv4"></a>IPv4

次の例では、Azure DNS で逆引き DNS ゾーンの PTR レコードを作成する手順を説明します。 他のレコードの種類と、既存のレコードの変更については、「[Azure Portal を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。

1. **[DNS ゾーン]** ウィンドウの上部にある **[+ レコード セット]** を選択して **[レコード セットの追加]** ウィンドウを開きます。

   ![[+ レコード セット] ボタンを指す矢印がある [DNS ゾーン] ウィンドウのスクリーンショット。](./media/dns-reverse-dns-hosting/figure4.png)

1. PTR レコードのレコード セットの名前は、IPv4 アドレスの残りの部分を逆の順序にしたものでなければなりません。 

   この例では、最初の 3 つのオクテットは既にゾーン名の一部として設定されています (.2.0.192)。 したがって、 **[名前]** ボックスでは最後のオクテットだけを指定します。 たとえば、IP アドレスが 192.0.2.15 のリソースでは、レコード セットの名前を **15** にします。  
1. **[種類]** には **[PTR]** を選択します。  
1. **[ドメイン名]** に、その IP アドレスを使うリソースの完全修飾ドメイン名 (FQDN) を入力します。
1. ウィンドウ下部の **[OK]** を選択すると、DNS レコードが作成されます。

   ![ボックスに入力された [レコード セットの追加] ウィンドウ](./media/dns-reverse-dns-hosting/figure5.png)

次の例では、PowerShell または Azure CLI を使用してこのタスクを行う方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

次の例では、新しい PTR レコードを作成する手順を説明します。 他のレコードの種類と、既存のレコードの変更については、「[Azure Portal を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。

1. **[DNS ゾーン]** ウィンドウの上部にある **[+ レコード セット]** を選択して **[レコード セットの追加]** ウィンドウを開きます。

   ![レコード セットを作成するためのボタン](./media/dns-reverse-dns-hosting/figure6.png)

2. PTR レコードのレコード セットの名前は、IPv6 アドレスの残りの部分を逆の順序にしたものでなければなりません。 ゼロ圧縮を含めることはできません。 

   この例では、IPv6 の最初の 64 ビットはゾーン名の一部として既に設定されています (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa)。 したがって、 **[名前]** ボックスでは最後の 64 ビットだけを指定します。 IP アドレスの最後の 64 ビットを逆の順序で入力し、各 16 進数の間の区切り記号としてピリオドを使います。 たとえば、IP アドレスが 2001:0db8:abdc:0000:f524:10bc:1af9:405e であるリソースの場合、レコード セットの名前は **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** となります。  
3. **[種類]** には **[PTR]** を選択します。  
4. **[ドメイン名]** に、その IP アドレスを使うリソースの FQDN を入力します。
5. ウィンドウ下部の **[OK]** を選択すると、DNS レコードが作成されます。

![[種類] フィールドの値を指す矢印がある [レコードセットの追加] ウィンドウを示すスクリーンショット。](./media/dns-reverse-dns-hosting/figure7.png)

次の例では、PowerShell または Azure CLI を使用してこのタスクを行う方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>レコードの表示

作成したレコードを表示するには、Azure Portal で DNS ゾーンを参照します。 **[DNS ゾーン]** ウィンドウの下部に、その DNS ゾーンのレコードが表示されます。 既定の NS レコードと SOA レコードに加え、作成した新しいレコードが表示されます。 NS レコードと SOA レコードは、すべてのゾーンに作成されます。 

### <a name="ipv4"></a>IPv4

**[DNS ゾーン]** ウィンドウに IPv4 PTR レコードが表示されます。

![IPv4 レコードが表示された [DNS ゾーン] ウィンドウ](./media/dns-reverse-dns-hosting/figure8.png)

次の例では、PowerShell または Azure CLI を使って PTR レコードを表示する方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**[DNS ゾーン]** ウィンドウに IPv6 PTR レコードが表示されます。

![IPv6 レコードが表示された [DNS ゾーン] ウィンドウ](./media/dns-reverse-dns-hosting/figure9.png)

次の例では、PowerShell または Azure CLI を使ってレコードを表示する方法を示します。

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure クラシック CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>よく寄せられる質問

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>ISP から割り当てられた IP アドレス ブロックの逆引き DNS 参照ゾーンを Azure DNS でホストできますか?

はい。 独自の IP アドレス範囲の逆引き参照 (ARPA) ゾーンの Azure DNS でのホストは、完全にサポートされています。

この記事の説明に従って Azure DNS に逆引き参照ゾーンを作成した後、ISP と協力して[ゾーンを委任](dns-domain-delegation.md)します。 その後、他のレコードの種類と同じ方法で、各逆引き参照の PTR レコードを管理できます。

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>逆引き DNS 参照ゾーンをホストする費用はどのくらいですか?

ISP によって割り当てられた IP アドレス ブロックの逆引き DNS 参照ゾーンを Azure DNS でホストする場合、[Azure DNS の標準料金](https://azure.microsoft.com/pricing/details/dns/)がかかります。

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Azure DNS で IPv4 と IPv6 の両方のアドレスの逆引き DNS 参照ゾーンをホストできますか?

はい。 この記事では、IPv4 と IPv6 両方の逆引き DNS 参照ゾーンを Azure DNS に作成する方法を説明してあります。

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>既存の逆引き DNS 参照ゾーンをインポートすることはできますか?

はい。 Azure CLI を使って、既存の DNS ゾーンを Azure DNS にインポートすることができます。 この方法は、前方参照ゾーンと逆引き参照ゾーンの両方に対して機能します。

詳しくは、[Azure CLI を使用した DNS ゾーン ファイルのインポートとエクスポート](dns-import-export.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

逆引き DNS について詳しくは、[Wikipedia の逆引き DNS 参照](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)をご覧ください。
<br>
[Azure サービスの逆引き DNS レコードを管理する](dns-reverse-dns-for-azure-services.md)方法を学習してください。
