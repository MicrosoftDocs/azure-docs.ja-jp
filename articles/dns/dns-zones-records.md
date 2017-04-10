---
title: "DNS ゾーンとレコードの概要 - Azure DNS | Microsoft Docs"
description: "Microsoft Azure DNS での DNS ゾーンとレコードのホスティングに対するサポートの概要"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 119275f335344858cd20b6a17ef87e3ef32b6e12
ms.openlocfilehash: 4e25ec1ece6017dc58c24ce593802293b7fc12b8
ms.lasthandoff: 03/01/2017

---

# <a name="overview-of-dns-zones-and-records"></a>DNS ゾーンとレコードの概要

このページでは、ドメイン、DNS ゾーン、DNS レコードおよびレコード セットの主要な概念と、Azure DNS でのサポートについて説明します。

## <a name="domain-names"></a>ドメイン名

ドメイン ネーム システムはドメインの階層構造です。 階層は、"**.**" という名前の "root" ドメインから始まります。  その下には "com"、"net"、"org"、"uk"、"jp" などのトップ レベル ドメインがあります。  さらに、これらの下には "org.uk" や "co.jp" などの第&2; レベル ドメインがあります。 DNS 階層のドメインはグローバルに分散していて、世界中の DNS ネーム サーバーでホストされています。

"contoso.com" などのドメイン名は、ドメイン名レジストラーという組織から購入できます。  ドメイン名を購入すると、"www.contoso.com" という名前で会社の Web サイトを表示するなど、そのドメイン名で DNS 階層を制御する権限が付与されます。 レジストラーが顧客に代わって自社のネーム サーバーでドメインをホストする場合もあれば、顧客が他のネーム サーバーを指定できる場合もあります。

Azure DNS では世界各地に分散された高可用性ネーム サーバー インフラストラクチャを提供しており、お客様はこれを利用してドメインをホストできます。 Azure DNS でドメインをホストすることで、他の Azure サービスと同じ資格情報、API、ツール、課金情報、サポートを使用して DNS レコードを管理できます。

Azure DNS では、現在、ドメイン名の購入はサポートされていません。 ドメイン名を購入する場合は、サードパーティのドメイン名レジストラーを利用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストし、DNS レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

## <a name="dns-zones"></a>DNS ゾーン

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS レコード

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-To-Live

Time-to-Live (TTL) は、各レコードが再度照会されるまでクライアントによってキャッシュされる期間を指定します。 上の例では、TTL は 3600 秒、つまり 1 時間です。

Azure DNS では、TTL は各レコードではなくレコード セットに対して指定されるため、そのレコード セット内のすべてのレコードに同じ値が使用されます。  TTL には 1 秒から 2,147,483,647 秒までの間の値を指定できます。

### <a name="wildcard-records"></a>ワイルドカード レコード

Azure DNS は、[ワイルドカード レコード](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をサポートしています。 ワイルドカード レコードは、一致する名前を含むクエリへの応答として返されます (非ワイルドカード レコード セットに、より近い一致がない場合)。 Azure DNS では、NS と SOA を除くすべてのレコードの種類でワイルドカード レコード セットをサポートします。

ワイルドカード レコード セットを作成するには、レコード セット名 "\*" を使用します。 または、左端のラベルを "\*" とした名前も使用できます (例: "\*.foo")。

### <a name="cname-records"></a>CNAME レコード

CNAME レコード セットは、同じ名前を持つ他のレコード セットとは共存できません。 たとえば、相対名 "www" を持つ CNAME レコード セットと相対名 "www" を持つ A レコードを同時に作成することはできません。

ゾーンの頂点 (名前は "@") には、ゾーンの作成時に作成された NS および SOA レコード セットが必ず含まれるため、ゾーンの頂点で CNAME レコード セットを作成することはできません。

これらの制約は、DNS 標準から生じるものであり、Azure DNS の制限事項ではありません。

### <a name="ns-records"></a>NS レコード

NS レコード セットは各ゾーンの頂点に自動的に作成され (名前は "@")、ゾーンが削除されると自動的に削除されます (個別に削除することはできません)。  このレコード セットの TTL は変更できますが、レコードに変更を加えることはできません。レコードは、ゾーンに割り当てられている Azure DNS ネーム サーバーを参照するよう事前に構成されています。

ゾーンの頂点を除いて、ゾーン内で NS レコードを作成および削除できます。  この操作により、子ゾーンを構成できます (詳細については、「[Azure DNS サブドメインの委任](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)」を参照してください)。

### <a name="soa-records"></a>SOA レコード

SOA レコード セットは各ゾーンの頂点に自動的に作成され (名前は "@")、ゾーンが削除されると自動的に削除されます。  SOA レコードを個別に作成または削除することはできません。

SOA レコードのプロパティはすべて変更可能ですが、"host" プロパティは変更できません。このプロパティは、Azure DNS で提供されているプライマリ ネーム サーバー名を参照するよう事前構成されています。

### <a name="spf-records"></a>SPF レコード

Sender Policy Framework (SPF) レコードは、特定のドメイン名を使って電子メールを送信することが許可される電子メール サーバーを指定するために使用されます。  SPF レコードを正しく構成するには、送信した電子メールが受信者に "迷惑メール" として分類されないようにする必要があります。

もともと DNS に関する RFC では、このようなシナリオに対応するレコードの種類として新しく "SPF" が導入されていました。 また、旧式のネーム サーバーに対応するために、レコードの種類として TXT を使用して SPF レコードを指定することも許可されていました。  このあいまいさが原因で混乱が生じていましたが、[RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) によって解決されました。  この RFC では、SPF レコードはレコードの種類として TXT を使用して作成しなければならないことが規定され、レコードの種類としての SPF は廃止されました。

**SPF レコードは Azure DNS でもサポートされていますが、レコードの種類として TXT を使用して作成する必要があります。** 廃止されたレコードの種類としての SPF はサポートされていません。 [DNS ゾーン ファイルをインポートする](dns-import-export.md)際、レコードの種類として SPF が使用されている SPF レコードは、レコードの種類が TXT に変換されます。

### <a name="srv-records"></a>SRV レコード

[SRV レコード](https://en.wikipedia.org/wiki/SRV_record)は、さまざまなサービスでサーバーの場所を指定するために使用されます。 Azure DNS で SRV レコードを指定する際は、次の点に注意してください。

* "*サービス*" と "*プロトコル*" は、レコード セット名の一部として、先頭にアンダースコアを付けて指定する必要があります。  たとえば、"\_sip.\_tcp.name" のように指定します。  ゾーンの頂点にあるレコードの場合は、レコード名に "@" を指定する必要はありません。サービスとプロトコルのみを使用します。たとえば、"\_sip.\_tcp" のように指定します。
* "*優先度*"、"*重み*"、"*ポート*"、および "*ターゲット*" は、レコード セット内の各レコードのパラメーターとして指定されます。

### <a name="txt-records"></a>TXT レコード

TXT レコードは、ドメイン名を任意のテキスト文字列にマップするために使用されます。 TXT レコードは多様なアプリケーションで使用されますが、特に [SPF (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) や [DKIM (DomainKeys Identified Mail)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail) などの電子メール構成に関連して使用されます。

DNS 標準では、1 つの TXT レコードに複数の文字列を含めることができ、各文字列の長さは最大 254 文字が可能です。 複数の文字列が使用された場合は、クライアントによって文字列が連結され、1 つの文字列として処理されます。

Azure DNS の REST API を呼び出す場合は、各 TXT 文字列を別々に指定する必要があります。  Azure Portal、PowerShell、または CLI インターフェイスを使用する場合は、レコードごとに 1 つの文字列を指定する必要があります。指定した文字列は、必要に応じて 254 文字のセグメントに自動的に分割されます。

DNS レコード内の複数の文字列と TXT レコード セット内の複数の TXT レコードを混同しないでください。  TXT レコード セットには、複数のレコードを含めることができ、"*レコードごとに*" 複数の文字列を含めることができます。  Azure DNS は、TXT レコード セットごとに最長 1024 文字の文字列をサポートします (すべてのレコードの文字列を結合します)。 

## <a name="tags-and-metadata"></a>タグとメタデータ

### <a name="tags"></a>タグ

タグは名前と値のペアのリストで、Azure Resource Manager でリソースのラベル付けに使用されます。  Azure Resource Manager ではこのタグを使用して、Azure の課金内容に関するフィルター ビューを表示したり、必須タグに関するポリシーを設定したりします。 タグの詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/resource-group-using-tags.md)を参照してください。

Azure DNS では、DNS ゾーン リソースに対して Azure Resource Manager のタグを使用できます。  DNS レコード セットのタグはサポートされませんが、その代わりとして、DNS レコード セットでは、この後説明する 'メタデータ' がサポートされます。

### <a name="metadata"></a>Metadata

レコード セットのタグの代わりに、Azure DNS では "メタデータ" を使用してレコード セットに注釈を付けることができます。  メタデータを使用すると、タグと同じように、各レコード セットに名前と値のペアを関連付けることができます。  これは、各レコード セットの用途を記録しておきたい場合などに便利です。  タグと異なる点として、メタデータは、Azure の課金内容に関するフィルター ビューを提供するためには使用できず、Azure Resource Manager のポリシーで指定することもできません。

## <a name="etags"></a>Etag

たとえば、2 人のユーザーまたは&2; つのプロセスが同時に DNS レコードを変更しようとするとします。 どちらの変更が優先されるでしょうか。 また、優先された側は、他のユーザーまたはプロセスによって行われた変更を上書きしたことに気付くのでしょうか。

Azure DNS は、Etag を使用して同じリソースへの同時変更を安全に処理します。 Etag と [Azure Resource Manager の 'タグ' ](#tags) は独立しています。 各 DNS リソース (ゾーンまたはレコード セット) には、関連付けられている Etag があります。 リソースが取得されるときは、常に Etag も取得されます。 リソースを更新する場合は、Azure DNS がサーバー上の Etag の一致を確認できるように Etag を返すこともできます。 リソースを更新するたびに Etag が再生成されるため、Etag の不一致は同時変更が発生していることを示します。 Etag は、既存のリソースがないことを確認するために、新しいリソースの作成時にも使用されます。

Azure DNS PowerShell は、既定で、ゾーンおよびレコード セットへの同時変更のブロックに Etag を使用します。 オプションの *-Overwrite* スイッチを使用すると、Etag チェックを実行しないように設定できます。この場合、発生したすべての同時変更が上書きされます。

Azure DNS REST API のレベルでは、HTTP ヘッダーを使用して Etag を指定します。  次の表に各ヘッダーの動作を示します。

| ヘッダー | 動作 |
| --- | --- |
| なし |PUT は常に成功します (Etag チェックなし)。 |
| If-match <etag> |PUT は、リソースが存在し、Etag が一致する場合にのみ成功します |
| If-match * |PUT はリソースが存在する場合にのみ成功します |
| If-none-match * |PUT はリソースが存在しない場合にのみ成功します |


## <a name="limits"></a>制限

Azure DNS を使用する際は、次の制限が既定で適用されます。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>次のステップ

* Azure DNS の使用を開始する場合は、[DNS ゾーンの作成方法](dns-getstarted-create-dnszone-portal.md)と [DNS レコードの作成方法](dns-getstarted-create-recordset-portal.md)について確認してください。
* 既存の DNS ゾーンを移行する場合は、[DNS ゾーン ファイルのインポートとエクスポート](dns-import-export.md)の実行方法を確認してください。


