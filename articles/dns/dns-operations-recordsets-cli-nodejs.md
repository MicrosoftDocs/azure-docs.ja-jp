---
title: "Azure CLI 1.0 を使用した Azure DNS の DNS レコードの管理 | Microsoft Docs"
description: "Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。 レコード セットとレコードを操作するための CLI 1.0 コマンドをすべて紹介します。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 3074bf378f809a9857c7ea72521961368a14772c
ms.lasthandoff: 02/27/2017

---

# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用した Azure DNS の DNS レコードの管理

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

この記事では、クロス プラットフォームの Azure コマンド ライン インターフェイス (CLI) を使用して DNS ゾーンの DNS レコードを管理する方法について説明します。CLI は、Windows、Mac、および Linux で使用できます。 DNS レコードは、[Azure PowerShell](dns-operations-recordsets.md) または [Azure Portal](dns-operations-recordsets-portal.md) を使用して管理することもできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI。
* [Azure CLI 2.0](dns-operations-recordsets-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

この記事の例では、[Azure CLI 1.0 のインストール、サインイン、DNS ゾーンの作成](dns-operations-dnszones-cli-nodejs.md)が既に完了していることを前提としています。

## <a name="introduction"></a>はじめに

Azure DNS で DNS レコードを作成する前に、まず、Azure DNS における DNS レコード セットでの DNS レコードの編成方法を理解する必要があります。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS における DNS レコードの詳細については、「[DNS ゾーンとレコード](dns-zones-records.md)」を参照してください。

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`azure network dns record-set add-record` コマンドを使用します。 `azure network dns record-set add-record -h` を使用すると、ヘルプが表示されます。

レコードの作成時に、リソース グループ名、ゾーン名、レコード セット名、レコードの種類、および作成するレコードの詳細を指定する必要があります。 指定するレコード セット名は、ゾーン名を除いた*相対*名にする必要があります。

レコード セットがまだ存在していない場合は、このコマンドによって作成されます。 レコード セットが既に存在する場合、このコマンドは、指定されたレコードを既存のレコード セットに追加します。

新しいレコード セットが作成される場合は、既定の Time-to-Live (TTL) である 3600 が使用されます。 さまざまな TTL を使用する方法については、「[DNS レコード セットを作成する](#create-a-dns-record-set)」を参照してください。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* に *www* という A レコードを作成します。 A レコードの IP アドレスは、*1.2.3.4* です。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

ゾーンの頂点 (この例では "contoso.com") にレコードを作成するには、レコード名 "@" (引用符を含みます) を使用します。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>DNS レコード セットを作成する

上記の例では、DNS レコードは既存のレコード セットに追加されるか、レコード セットが*暗黙的に*作成されました。 レコード セットは、レコードを追加する前に*明示的に*作成することもできます。 Azure DNS では、DNS レコードの作成前に DNS 名を予約するためのプレースホルダーとして機能する "空" のレコード セットがサポートされています。 空のレコード セットは、Azure DNS コントロール プレーンには表示されるものの、Azure DNS ネーム サーバーには表示されません。

レコード セットは、`azure network dns record-set create` コマンドを使用して作成します。 `azure network dns record-set create -h` を使用すると、ヘルプが表示されます。

レコード セットを明示的に作成すると、[Time-To-Live (TTL)](dns-zones-records.md#time-to-live) などのレコード セット プロパティとメタデータを指定することができます。 [レコード セットのメタデータ](dns-zones-records.md#tags-and-metadata)を使用すると、アプリケーション固有のデータを、キーと値のペアとして各レコード セットに関連付けることができます。

次の例では、空のレコード セットを作成しますが、`--ttl` パラメーター (短縮形は `-l`) を指定することで 60 秒間の TTL を設定します。

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

次の例では、`--metadata` パラメーター (短縮形は `-m`) を指定することで、"dept=finance" と "environment=production" という&2; つのメタデータ エントリを含むレコード セットを作成します。

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

空のレコード セットが作成されたため、「[DNS レコードの作成](#create-a-dns-record)」で説明したように、`azure network dns record-set add-record` を使用してレコードを追加することができます。

## <a name="create-records-of-other-types"></a>その他の種類のレコードの作成

先ほど "A" レコードの作成方法について詳しく説明しましたが、次の例では、Azure DNS でサポートされているその他の種類のレコードの作成方法を示します。

レコード データを指定するためのパラメーターは、レコードの種類によって異なります。 たとえば、種類 "A" のレコードの場合は、パラメーター `-a <IPv4 address>` に IPv4 アドレスを指定します。 各レコードの種類のパラメーターは、`azure network dns record-set add-record -h` を使用して表示できます。

各ケースで、1 つのレコードの作成方法を説明します。 レコードは、既存のレコード セットまたは暗黙的に作成されたレコード セットに追加されます。 レコード セットを作成してレコード セット パラメーターを明示的に定義する方法の詳細については、「[DNS レコード セットを作成する](#create-a-dns-record-set)」を参照してください。

SOA レコード セットを作成する例は示しません。SOA は各 DNS ゾーンと共に作成および削除されるため、単独で作成または削除することはできません。 ただし、[後の例に示すとおり、SOA を変更することはできます](#to-modify-an-SOA-record)。

### <a name="create-an-aaaa-record"></a>AAAA レコードの作成

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>CNAME レコードを作成する

> [!NOTE]
> DNS 標準では、ゾーンの頂点 (`-Name "@"`) に CNAME レコードを作成することは許可されていません。また、複数のレコードを含むレコード セットの作成も許可されていません。
> 
> 詳細については、「[CNAME レコード](dns-zones-records.md#cname-records)」を参照してください。

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>MX レコードの作成

この例では、レコード セット名 "@" を使用してゾーンの頂点 (この場合は "contoso.com" ) に MX レコードを作成します。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>NS レコードの作成

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>PTR レコードの作成

ここで "my-arpa-zone.com" は IP 範囲を表す ARPA ゾーンを表します。 このゾーンの各 PTR レコード セットは、この IP の範囲内の IP アドレスに対応します。  レコード名「10」は、このレコードによって表されるこの IP 範囲内の IP アドレスの最後のオクテットです。

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>SRV レコードの作成

[SRV レコード セット](dns-zones-records.md#srv-records)を作成するときは、レコード セット名に *\_service* と *\_protocol* を指定します。 ゾーンの頂点で SRV レコード セットを作成するときは、レコード セット名に "@" を含める必要はありません。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>TXT レコードの作成

次の例は、TXT レコードを作成する方法を示しています。 TXT レコードでサポートされている文字列の最大長の詳細については、[TXT レコード](dns-zones-records.md#txt-records)に関するセクションを参照してください。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>レコード セットの取得

既存のレコード セットを取得するには、 `azure network dns record-set show`を使用します。 `azure network dns record-set show -h` を使用すると、ヘルプが表示されます。

レコードやレコード セットの作成時と同様、指定するレコード セット名は*相対*名にする必要があります。つまり、ゾーン名を除く必要があります。 レコードの種類のほか、レコード セットを含むゾーンと、ゾーンを含むリソース グループも指定する必要があります。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* から *www* という種類 A のレコードを取得します。

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>レコード セットの一覧を表示する

`azure network dns record-set list` コマンドを使用すると、DNS ゾーンの全レコードを一覧表示できます。 `azure network dns record-set list -h` を使用すると、ヘルプが表示されます。

この例では、名前やレコードの種類に関係なく、リソース グループ *MyResourceGroup* のゾーン *contoso.com* 内のすべてのレコード セットを返します。

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

この例では、指定したレコードの種類 (この場合は 'A' レコード) に一致するすべてのレコード セットが返されます。

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>既存のレコード セットへのレコードの追加

`azure network dns record-set add-record` は、新しいレコード セットのレコードの作成と、既存のレコード セットへのレコードの追加の両方に、使用することができます。

詳細については、上述の「[DNS レコードの作成](#create-a-dns-record)」と「[その他の種類のレコードの作成](#create-records-of-other-types)」を参照してください。

## <a name="remove-a-record-from-an-existing-record-set"></a>既存のレコード セットからのレコードの削除

既存のレコード セットからレコードを削除するには、`azure network dns record-set delete-record` を使用します。 `azure network dns record-set delete-record -h` を使用すると、ヘルプが表示されます。

このコマンドは、DNS レコードをレコード セットから削除します。 レコード セットの最後のレコードを削除しても、レコード セット自体は削除**されません**。 代わりに、空のレコード セットが残されます。 その代わりにレコード セットを削除する場合は、「[レコード セットの削除](#delete-a-record-set)」を参照してください。

削除するレコードと、該当レコードを削除するゾーンを指定する必要があります。レコード作成時と同じパラメーター `azure network dns record-set add-record` を使用します。 このパラメーターについては、上述の「[DNS レコードの作成](#create-a-dns-record)」と「[その他の種類のレコードの作成](#create-records-of-other-types)」で説明しています。

このコマンドは、確認を求めるプロンプトを表示します。 `--quiet` スイッチ (短縮形は `-q`) を使用すると、このプロンプトを表示しないようにすることができます。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* の *www* というレコード セットから値 '1.2.3.4' を持つ A レコードを削除します。 確認のプロンプトは表示されません。

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>既存のレコード セットの変更

各レコード セットには、[Time-to-Live (TTL)](dns-zones-records.md#time-to-live)、[メタデータ](dns-zones-records.md#tags-and-metadata)、および DNS レコードが含まれています。 次のセクションでは、これらの各プロパティを変更する方法を説明します。

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>A、AAAA、MX、NS、PTR、SRV、または TXT レコードを変更するには

種類が A、AAAA、MX、NS、PTR、SRV、TXT の既存のレコードを変更するには、最初に新しいレコードを追加してから既存のレコードを削除する必要があります。 レコードを削除し追加する方法の詳細な手順については、この記事の前半のセクションを参照してください。

次の例では、'A' レコードを IP アドレス 1.2.3.4 から IP アドレス 5.6.7.8 に変更する方法を示します。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

ゾーンの頂点 (`-Name "@"`、引用符を含む) に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。 このレコード セットで許可されている変更は、レコード セットの TTL とメタデータの変更のみです。

### <a name="to-modify-a-cname-record"></a>CNAME レコードを変更するには

CNAME レコードを変更するには、`azure network dns record-set add-record` を使用して新しいレコードの値を追加します。 その他のレコードの種類とは異なり、CNAME レコード セットはレコードを&1; つだけ含むことができます。 そのため、新しいレコードが追加されたときに既存のレコードが*置換*されます。個別に削除する必要はありません。  この置換の受け入れを求めるプロンプトが表示されます。

例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* の CNAME レコード セット *www* を変更して、既存の値ではなく 'www.fabrikam.net' を指すようにします。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>SOA レコードを変更するには

`azure network dns record-set set-soa-record` を使用して指定した DNS ゾーンの SOA を変更します。 `azure network dns record-set set-soa-record -h` を使用すると、ヘルプが表示されます。

次の例は、リソース グループ *MyResourceGroup* のゾーン *contoso.com* の SOA レコードの 'email' プロパティを設定する方法を示します。

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>既存のレコード セットの TTL を変更するには

既存のレコード セットの TTL を変更するには、`azure network dns record-set set` を使用します。 `azure network dns record-set set -h` を使用すると、ヘルプが表示されます。

次の例では、レコード セットの TTL を変更する方法を示します。ここでは 60 秒に変更します。

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>既存のレコード セットのメタデータを変更するには

[レコード セットのメタデータ](dns-zones-records.md#tags-and-metadata)を使用すると、アプリケーション固有のデータを、キーと値のペアとして各レコード セットに関連付けることができます。 既存のレコード セットのメタデータを変更するには、`azure network dns record-set set` を使用します。 `azure network dns record-set set -h` を使用すると、ヘルプが表示されます。

次の例では、`--metadata` パラメーター (短縮形は `-m`) を指定することで、"dept=finance" と "environment=production" という&2; つのメタデータ エントリを含むレコード セットを変更する方法を示します。 既存のメタデータは指定した値に*置換*されることに注意してください。

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>レコード セットの削除

レコード セットは `azure network dns record-set delete` コマンドで削除できます。 `azure network dns record-set delete -h` を使用すると、ヘルプが表示されます。 レコード セットを削除すると、そのレコード セット内のレコードもすべて削除されます。

> [!NOTE]
> ゾーンの頂点 (`-Name "@"`) の SOA および NS レコード セットを削除することはできません。  これらはゾーンの作成時に自動的に作成され、ゾーンを削除すると自動的に削除されます。

次の例では、リソース グループ *MyResourceGroup* のゾーン *contoso.com* から *www* という種類 A のレコード セットを削除します。

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

削除操作の確認を求めるプロンプトが表示されます。 このプロンプトを抑制するのには、`--quiet` スイッチ (短縮形は `-q`) を使用します。

## <a name="next-steps"></a>次のステップ

[Azure DNS におけるゾーンとレコード](dns-zones-records.md)について確認します。
<br>
Azure DNS の使用時に[ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)方法について確認します。

