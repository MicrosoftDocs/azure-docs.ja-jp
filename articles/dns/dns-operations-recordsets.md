---
title: Azure PowerShell を使用した Azure DNS での DNS レコードの管理 | Microsoft Docs
description: Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。 レコード セットとレコードに対する操作のための PowerShell コマンドをすべて紹介します。
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011593"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Azure PowerShell を使用して Azure DNS のレコードおよびレコード セットを管理する

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure クラシック CLI](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

この記事では、Azure PowerShell を使用して DNS ゾーンの DNS レコードを管理する方法について説明します。 DNS レコードは、クロスプラットフォームの [Azure CLI](dns-operations-recordsets-cli.md) または [Azure Portal](dns-operations-recordsets-portal.md) を使用して管理することもできます。

この記事の例では、[Azure PowerShell のインストール、サインイン、DNS ゾーンの作成](dns-operations-dnszones.md)が既に完了していることを前提としています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>はじめに

Azure DNS で DNS レコードを作成する前に、まず、Azure DNS における DNS レコード セットでの DNS レコードの編成方法を理解する必要があります。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS における DNS レコードの詳細については、「[DNS ゾーンとレコード](dns-zones-records.md)」を参照してください。


## <a name="create-a-new-dns-record"></a>新しい DNS レコードの作成

新しいレコードの名前と種類が既存のレコードと同じである場合は、[そのレコードを既存のレコード セットに追加する](#add-a-record-to-an-existing-record-set)必要があります。 新しいレコードの名前と種類が既存のすべてのレコードと異なる場合は、新しいレコード セットを作成する必要があります。 

### <a name="create-a-records-in-a-new-record-set"></a>新しいレコード セットの "A" レコードの作成

レコード セットは、`New-AzDnsRecordSet` コマンドレットを使用して作成します。 レコード セットを作成する際には、レコード セット名、ゾーン、Time to Live (TTL)、レコードの種類、作成するレコードを指定する必要があります。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。 たとえば、"A" という種類のレコード セットを使う場合、`-IPv4Address` パラメーターを使って IP アドレスを指定する必要があります。 他のレコードの種類には他のパラメーターを使用します。 詳細については、その他のレコードの種類の例に関するセクションを参照してください。

下の例では、DNS ゾーン "contoso.com" に相対名 "www" を持つレコード セットを作成します。 レコード セットの完全修飾名は、"www.contoso.com" になります。 レコードの種類は "A" で、TTL は 3,600 秒です。 レコード セットには、"1.2.3.4" という IP アドレスの 1 つのレコードが含まれています。

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

ゾーンの "頂点" (この例では "contoso.com") にレコード セットを作成するには、レコード セット名 '\@' (引用符を除く) を使います。

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

複数のレコードを含むレコード セットを作成する必要がある場合は、まず、ローカル配列を作成してレコードを追加し、次のようにその配列を `New-AzDnsRecordSet` に渡す必要があります。

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[レコード セット メタデータ](dns-zones-records.md#tags-and-metadata)を使用すると、アプリケーション固有のデータを、キーと値のペアとして各レコード セットに関連付けることができます。 次の例は、"dept=finance" と "environment=production" という 2 つのメタデータ エントリを含むレコード セットを作成する方法を示しています。

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS では、DNS レコードの作成前に DNS 名を予約するためのプレースホルダーとして機能する "空" のレコード セットもサポートされています。 空のレコード セットは、Azure DNS コントロール プレーンには表示されるものの、Azure DNS ネーム サーバーには表示されません。 次の例では、空のレコード セットが作成されます。

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>その他の種類のレコードの作成

先ほど "A" レコードの作成方法について詳しく説明しましたが、次の例では、Azure DNS でサポートされているその他の種類のレコードの作成方法を示します。

各ケースで、1 つのレコードを含むレコード セットの作成方法を説明します。 "A" レコードに関する前の例は、メタデータを使って複数のレコードを含むその他の種類のレコード セットを作成したり、空のレコード セットを作成したりする場合に応用できます。

SOA レコード セットを作成する例は示しません。SOA は各 DNS ゾーンと共に作成および削除されるため、単独で作成または削除することはできません。 ただし、[後の例に示すとおり、SOA を変更することはできます](#to-modify-an-soa-record)。

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>1 つのレコードを含む AAAA レコード セットの作成

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>1 つのレコードを含む CAA レコード セットの作成

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>1 つのレコードを含む CNAME レコード セットの作成

> [!NOTE]
> DNS 標準では、ゾーンの頂点 (`-Name '@'`) に CNAME レコードを作成することは許可されていません。また、複数のレコードを含むレコード セットの作成も許可されていません。
> 
> 詳細については、「[CNAME レコード](dns-zones-records.md#cname-records)」を参照してください。


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>1 つのレコードを含む MX レコード セットの作成

この例では、レコード セット名 '\@' を使ってゾーンの頂点 (この場合は "contoso.com") に MX レコードを作成します。


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>1 つのレコードを含む NS レコード セットの作成

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>1 つのレコードを含む PTR レコード セットの作成

ここで "my-arpa-zone.com" は IP 範囲を表す ARPA 逆引き参照ゾーンを表します。 このゾーンの各 PTR レコード セットは、この IP の範囲内の IP アドレスに対応します。 レコード名「10」は、このレコードによって表されるこの IP 範囲内の IP アドレスの最後のオクテットです。

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>1 つのレコードを含む SRV レコード セットの作成

[SRV レコード セット](dns-zones-records.md#srv-records)を作成するときは、レコード セット名に *\_service* と *\_protocol* を指定します。 ゾーンの頂点で SRV レコード セットを作成するときは、レコード セット名に '\@' を含める必要はありません。

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>1 つのレコードを含む TXT レコード セットの作成

次の例は、TXT レコードを作成する方法を示しています。 TXT レコードでサポートされている文字列の最大長の詳細については、[TXT レコード](dns-zones-records.md#txt-records)に関するセクションを参照してください。

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>レコード セットの取得

既存のレコード セットを取得するには、 `Get-AzDnsRecordSet`を使用します。 このコマンドレットは、Azure DNS のレコード セットを表すローカル オブジェクトを返します。

`New-AzDnsRecordSet` と同様に、レコード セット名は、ゾーン名を除いた "*相対*" 名にする必要があります。 レコードの種類のほか、レコード セットを含むゾーンも指定する必要があります。

次の例は、レコード セットを取得する方法を示しています。 この例では、ゾーンは `-ZoneName` および `-ResourceGroupName` パラメーターを使用して指定されています。

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

このほか、`-Zone` パラメーターを使って渡されるゾーン オブジェクトを使ってゾーンを指定する方法もあります。

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>レコード セットの一覧を表示する

`Get-AzDnsZone` を使用すると、`-Name` パラメーターまたは `-RecordType` パラメーターを省略してゾーン内のレコード セットを一覧表示することもできます。

次の例は、ゾーン内のすべてのレコード セットを返します。

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

次の例は、レコードの種類を指定し、レコード セット名は省略して、特定の種類のレコード セットをすべて取得する方法を示しています。

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

レコードの種類を問わず、特定の名前のレコード セットをすべて取得するには、すべてのレコード セットを取得したうえで、結果をフィルター処理する必要があります。

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

上記のどの例でも、ゾーンを指定するには、例に示した `-ZoneName` パラメーターと `-ResourceGroupName` パラメーターを使用するか、ゾーン オブジェクトを指定します。

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>既存のレコード セットへのレコードの追加

既存のレコード セットにレコードを追加するには、次の次の 3 つの手順を実行します。

1. 既存のレコード セットの取得

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. ローカル レコード セットに新しいレコードを追加します。 これはオフライン操作です。

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS サービスに変更をコミットバックします。 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

 を使用すると、Azure DNS 内の既存のレコード セット (およびそれに含まれるすべてのレコード) が、指定したレコード セットに "`Set-AzDnsRecordSet` *置き換えられます*"。 同時変更が上書きされないように [ETag チェック](dns-zones-records.md#etags)が使用されます。 オプションの `-Overwrite` スイッチを使用すると、これらのチェックを抑制できます。

一連の操作は "*パイプ*" することもできます。つまり、レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことができます。

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

上記の例は、種類が "A" の既存のレコード セットに "A" レコードを追加する方法を示しています。 同様の操作は、他の種類のレコード セットにレコードを追加するときにも使用されます。そのときは、`Add-AzDnsRecordConfig` の `-Ipv4Address` パラメーターをレコードの種類それぞれに固有の他のパラメーターに置き換えます。 レコードの種類それぞれに対応したパラメーターは、上記のその他のレコードの種類の例に関するセクションに示したとおり、`New-AzDnsRecordConfig` コマンドレットと同じです。

種類が "CNAME" または "SOA" のレコード セットに複数のレコードを含めることはできません。 この制約は DNS 標準によるものです。 Azure DNS の制限ではありません。

## <a name="remove-a-record-from-an-existing-record-set"></a>既存のレコード セットからのレコードの削除

レコード セットからレコードを削除するプロセスは、既存のレコード セットにレコードを追加するプロセスに似ています。

1. 既存のレコード セットの取得

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. ローカル レコード セット オブジェクトからレコードを削除します。 これはオフライン操作です。 削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS サービスに変更をコミットバックします。 同時変更の [ETag チェック](dns-zones-records.md#etags)を抑制するには、オプションの `-Overwrite` スイッチを使用します。

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

上記の操作でレコード セットから最後のレコードを削除しても、レコード セットは削除されず、空のレコード セットが残されます。 レコード セットを完全に削除するには、「[レコード セットの削除](#delete-a-record-set)」を参照してください。

レコードをレコード セットに追加する操作と同様に、レコード セットを削除する一連の操作もパイプすることがことができます。

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

レコードの種類に固有の適切なパラメーターを `Remove-AzDnsRecordSet` に渡すことで、さまざまなレコードの種類がサポートされます。 レコードの種類それぞれに対応したパラメーターは、上記のその他のレコードの種類の例に関するセクションに示したとおり、`New-AzDnsRecordConfig` コマンドレットと同じです。


## <a name="modify-an-existing-record-set"></a>既存のレコード セットの変更

既存のレコード セットを変更する手順は、レコード セットのレコードを追加または削除するときの手順と似ています。

1. `Get-AzDnsRecordSet`を使用して既存のレコード セットを取得します。
2. 次の方法で、ローカル レコード セット オブジェクトを変更します。
    * レコードを追加または削除する
    * 既存のレコードのパラメーターを変更する
    * レコード セット メタデータと Time to Live (TTL) を変更する
3. `Set-AzDnsRecordSet` コマンドレットを使用して変更をコミットします。 これにより、Azure DNS 内の既存のレコード セットは、指定したレコード セットに "*置き換えられます*"。

`Set-AzDnsRecordSet` の使用時は、同時変更が上書きされないように [ETag チェック](dns-zones-records.md#etags)が使用されます。 オプションの `-Overwrite` スイッチを使用すると、これらのチェックを抑制できます。

### <a name="to-update-a-record-in-an-existing-record-set"></a>既存のレコード セット内のレコードを更新するには

この例では、既存の "A" レコードの IP アドレスを変更します。

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>SOA レコードを変更するには

ゾーンの頂点 (`-Name "@"`、引用符を含む) に自動的に作成された SOA レコード セットのレコードを追加または削除することはできません。 ただし、("ホスト" を除く) SOA レコードおよびレコード セットの TTL 内のパラメーターを変更することはできます。

次の例では、SOA レコードの *Email* プロパティを変更する方法を示します。

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>ゾーンの頂点にある NS レコードを変更するには

ゾーンの頂点にある NS レコード セットは各 DNS ゾーンで自動的に作成されます。 ゾーンに割り当てられている Azure DNS ネーム サーバーの名前が含まれています。

複数の DNS プロバイダーによる共同ホスト ドメインをサポートする目的で、この NS レコード セットにネーム サーバーを追加できます。 このレコード セットの TTL とメタデータを変更することもできます。 ただし、あらかじめ入力されている Azure DNS ネーム サーバーを削除または変更することはできません。

これは、ゾーンの頂点にある NS レコード セットにのみ適用されます。 (子ゾーンの委任に使用される) ゾーンの他の NS レコード セットは制約なしで変更できます。

次の例は、ゾーンの頂点にある NS レコード セットにネーム サーバーを追加する方法を示しています。

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>レコード セット メタデータを変更するには

[レコード セット メタデータ](dns-zones-records.md#tags-and-metadata)を使用すると、アプリケーション固有のデータを、キーと値のペアとして各レコード セットに関連付けることができます。

次の例は、既存のレコード セットのメタデータを変更する方法を示しています。

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>レコード セットの削除

レコード セットは `Remove-AzDnsRecordSet` コマンドレットで削除できます。 レコード セットを削除すると、そのレコード セット内のレコードもすべて削除されます。

> [!NOTE]
> ゾーンの頂点 (`-Name '@'`) の SOA および NS レコード セットを削除することはできません。  Azure DNS はこれらをゾーンの作成時に自動的に作成し、ゾーンの削除時に自動的に削除します。

次の例は、レコード セットを削除する方法を示しています。 この例では、レコード セット名、レコード セットの種類、ゾーン名、およびリソース グループはそれぞれ明示的に指定しています。

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

このほかレコード セットは、オブジェクトを使用して指定したゾーンのほか、名前と種類によって指定することもできます。

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

3 つ目の方法として、レコード セット オブジェクトを使用してレコード セット自体を指定することもできます。

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

レコード セット オブジェクトを使用して削除対象のレコード セットを指定した場合、[ETag チェック](dns-zones-records.md#etags)によって同時変更の削除が防止されます。 オプションの `-Overwrite` スイッチを使用すると、これらのチェックを抑制できます。

レコード セット オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>確認のプロンプト

`New-AzDnsRecordSet`、`Set-AzDnsRecordSet`、および `Remove-AzDnsRecordSet` コマンドレットは、いずれも確認のプロンプトをサポートしています。

各コマンドレットは、`$ConfirmPreference` PowerShell 設定変数の値が `Medium` 以下である場合に、確認のプロンプトを表示します。 `$ConfirmPreference` の既定値は `High` であるため、既定の PowerShell 設定を使用しているときはこれらのプロンプトは表示されません。

現在の `$ConfirmPreference` 設定は `-Confirm` パラメーターを使用してオーバーライドできます。 `-Confirm` または `-Confirm:$True` を指定した場合は、コマンドレットによって実行前に確認のプロンプトが表示されます。 `-Confirm:$False` を指定した場合は、コマンドレットによって確認のプロンプトが表示されません。 

`-Confirm` と `$ConfirmPreference` の詳細については、「[About Preference Variables (設定変数について)](/powershell/module/microsoft.powershell.core/about/about_preference_variables)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure DNS におけるゾーンとレコード](dns-zones-records.md)について確認します。
<br>
Azure DNS の使用時に[ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)方法について確認します。
<br>
[Azure DNS PowerShell のリファレンス ドキュメント](/powershell/module/az.dns)を確認します。