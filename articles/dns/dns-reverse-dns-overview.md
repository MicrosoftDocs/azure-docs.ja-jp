---
title: Azure での逆引き DNS の概要 - Azure DNS
description: このラーニング パスでは、逆引き DNS のしくみと Azure での使用方法についての学習を始めます
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965614"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>逆引き DNS と Azure でのサポートの概要

この記事では、逆引き DNS の動作方法の概要および Azure でサポートされている逆引き DNS のシナリオについて説明します。

## <a name="what-is-reverse-dns"></a>逆引き DNS とは何ですか?

従来の DNS レコードでは、DNS 名 (www.contoso.com など) から IP アドレス (64.4.6.100 など) へのマッピングが可能です。  逆引き DNS を使用すると、IP アドレス (64.4.6.100) を名前 (www.contoso.com) に逆変換できます。

逆引き DNS レコードは、さまざまな状況で使用されます。 たとえば、逆引き DNS レコードは、電子メール メッセージの送信者を確認して電子メール スパムに対処するために広く使用されています。  受信メール サーバーは、送信側サーバーの IP アドレスの逆引き DNS レコードを取得し、そのホストが発信元ドメインからの電子メールの送信を承認されているかどうかを確認します。 

## <a name="how-reverse-dns-works"></a>逆引き DNS のしくみ

逆引き DNS レコードは、"ARPA" ゾーンと呼ばれる特殊な DNS ゾーンでホストされます。  これらのゾーンは、"contoso.com" などのドメインをホストしている通常の階層と並行して別の DNS 階層を形成します。

たとえば、DNS レコード "www.contoso.com" は、"contoso.com" ゾーンの "www" という名前の DNS "A" レコードを使用して実装されます。  この A レコードは、対応する IP アドレス (この場合は 64.4.6.100) を指します。  逆引き参照は、"6.4.64.in-addr.arpa" ゾーンの "100" という名前の "PTR" レコードを使用して別途実装されます (IP アドレスは ARPA ゾーンで逆引きされます)。この PTR レコードは、正しく構成されている場合、"www.contoso.com" という名前を指します。

組織は、IP アドレス ブロックが割り当てられている場合、対応する ARPA ゾーンを管理する権利も取得します。 Azure によって使用される IP アドレス ブロックに対応する ARPA ゾーンは、Microsoft によってホスト、管理されます。 ISP は、ユーザー独自の IP アドレスの ARPA ゾーンをホストする場合もあれば、ユーザーが自身で選んだ DNS サービス (Azure DNS など) で ARPA ゾーンをホストできるようにする場合もあります。

> [!NOTE]
> DNS の前方参照と DNS の逆引き参照は、別々の並列した DNS 階層に実装されます。 "www.contoso.com" の逆引き参照は "contoso.com" ゾーンでホスト **されません**。代わりに、対応する IP アドレス ブロックの ARPA ゾーンでホストされます。 IPv4 と IPv6 のアドレス ブロックには、異なるゾーンが使われます。

### <a name="ipv4"></a>IPv4

IPv4 の逆引き参照ゾーンの名前は、`<IPv4 network prefix in reverse order>.in-addr.arpa` という形式にする必要があります。

たとえば、IP アドレスが 192.0.2.0/24 プレフィックスであるホストのレコードをホストする逆引きゾーンを作成するとき、ゾーン名は、アドレスのネットワーク プレフィックス (192.0.2) を分離し、順序を逆にして (2.0.192)、サフィックス `.in-addr.arpa` を追加することにより作成します。

|サブネット クラス|ネットワーク プレフィックス  |順序を逆にしたネットワーク プレフィックス  |標準のサフィックス  |逆引きゾーン名 |
|-------|----------------|------------|-----------------|---------------------------|
|クラス A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|クラス B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|クラス C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>クラスレス IPv4 の委任

場合によっては、組織に割り当てられた IP アドレスの範囲がクラス C (/24) の範囲より小さいことがあります。 この場合、IP アドレス範囲は `.in-addr.arpa` ゾーン階層内のゾーン境界に収まらず、そのため子ゾーンとして委任することはできません。

代わりに、異なるメカニズムを使って、個々の逆引き参照 (PTR) レコードの制御を専用の DNS ゾーンに転送します。 このメカニズムは、各 IP 範囲の子ゾーンを委任した後、CNAME レコードを使って、範囲内の各 IP アドレスを個別にその子ゾーンにマップします。

たとえば、組織が ISP から IP 範囲 192.0.2.128/26 を与えられているものとします。 これは、192.0.2.128 から 192.0.2.191 までの 64 個の IP アドレスを表します。 この範囲の逆引き DNS は、次のように実装されます。
- 組織は、128-26.2.0.192.in-addr.arpa という名前の逆引き参照ゾーンを作成します。 プレフィックス "128-26" は、クラス C (/24) の範囲内で組織に割り当てられたネットワーク セグメントを表します。
- ISP は、NS レコードを作成して、クラス C の親ゾーンから上のゾーンへの DNS 委任を設定します。 また、親 (クラス C) の逆引き参照ゾーンに CNAME レコードを作成し、IP アドレス範囲内の各 IP アドレスを組織によって作成された新しいゾーンにマップします。

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- その後、組織は、子ゾーン内の個々の PTR レコードを管理します。

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
IP アドレス "192.0.2.129" の逆引き参照では、"129.2.0.192.in-addr.arpa" という名前の PTR レコードのクエリが行われます。 このクエリは、親ゾーンの CNAME を使って、子ゾーンの PTR レコードに解決されます。

### <a name="ipv6"></a>IPv6

IPv6 の逆引き参照ゾーンの名前は、`<IPv6 network prefix in reverse order>.ip6.arpa` という形式にする必要があります。

次に例を示します。 IP アドレスが 2001:db8:1000:abdc::/64 プレフィックス内にあるホストのレコードをホストする逆引きゾーンを作成するとき、ゾーン名は、アドレスのネットワーク プレフィックス (2001:db8:abdc::) を分離することによって作成されます。 次に、IPv6 アドレス プレフィックスを短くするために[ゼロ圧縮](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))が使用われていた場合は (2001:0db8:abdc:0000::)、IPv6 ネットワーク プレフィックスを展開することによって削除します。 プレフィックスの各 16 進数の間の区切り記号としてピリオドを使って順序を反転させて、順序が逆のネットワーク プレフィックス (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) を作成し、サフィックス `.ip6.arpa` を追加します。


|ネットワーク プレフィックス  |展開して順序を逆にしたネットワーク プレフィックス |標準のサフィックス |逆引きゾーン名  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure による逆引き DNS のサポート

Azure では、逆引き DNS に関連して次の 2 つのシナリオがサポートされています。

**IP アドレス ブロックに対応する逆引き参照ゾーンをホストする。**
IPv4 と IPv6 の両方で、Azure DNS を使って、[逆引き参照ゾーンをホストし、各逆引き DNS 参照の PTR レコードを管理する](dns-reverse-dns-hosting.md)ことができます。  逆引き参照 (ARPA) ゾーンの作成、委任の設定、および PTR レコードの構成のプロセスは、通常の DNS ゾーンの場合と同じです。  唯一の違いは、委任の構成には DNS レジストラーではなく ISP を使用する必要があることで、PTR レコードのみを使用する必要があります。

**Azure サービスに割り当てられた IP アドレスの逆引き DNS レコードを構成する。** Azure を使うと、[Azure サービスに割り当てられた IP アドレスの逆引き参照を構成する](dns-reverse-dns-for-azure-services.md)ことができます。  この逆引き参照は、対応する ARPA ゾーン内の PTR レコードとして Azure によって構成されます。  これらの ARPA ゾーンは、Azure によって使われるすべての IP 範囲に対応しており、Microsoft によってホストされます。

## <a name="next-steps"></a>次のステップ

逆引き DNS について詳しくは、[Wikipedia の逆引き DNS 参照](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)をご覧ください。
<br>
[Azure DNS で ISP によって割り当てられた IP アドレス範囲の逆引き参照ゾーンをホストする](dns-reverse-dns-for-azure-services.md)方法を学習してください。
<br>
[Azure サービスの逆引き DNS レコードを管理する](dns-reverse-dns-for-azure-services.md)方法を学習してください。