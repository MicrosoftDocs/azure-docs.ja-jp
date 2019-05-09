---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124489"
---
ドメイン ネーム システム (DNS) は、インターネット上で要素を検索するために使用されます。 たとえば、ブラウザーにアドレスを入力するか、Web ページでリンクをクリックすると、DNS を使用してドメインが IP アドレスに変換されます。 IP アドレスは住所に似ていますが、人間にとってわかりやすい情報ではありません。 たとえば、 **contoso.com** のような DNS 名の方が、192.168.1.88 や 2001:0:4137:1f67:24a2:3888:9cce:fea3 などの IP アドレスよりはるかに覚えやすい情報です。

DNS システムは *レコード*に基づいたしくみです。 *contoso.com*のような特定の **名前**が、レコードによって IP アドレスまたは別の DNS 名に関連付けられます。 Web ブラウザーなどのアプリケーションは、DNS で名前を検索し、レコードを検索して、そのレコードが指すアドレスを使用します。 レコードが指す値が IP アドレスの場合、ブラウザーはその値を使用します。 レコードが別の DNS 名を指す場合、アプリケーションは、もう一度名前を解決する必要があります。 こうして最終的には、すべての名前が解決され、IP アドレスが取得されます。

DNS 名は、Azure Web サイトの作成時に、サイトに自動的に割り当てられます。 この名前には、**&lt;yoursitename&gt;.azurewebsites.net** という形式が使用されます。 Web サイトを Azure Traffic Manager エンドポイントとして追加すると、**&lt;yourtrafficmanagerprofile&gt;.trafficmanager.net** ドメイン経由で Web サイトにアクセスできます。

> [!NOTE]
> Web サイトが Traffic Manager エンドポイントとして構成されている場合、DNS レコードの作成時に **.trafficmanager.net** アドレスを使用します。
> 
> Traffic Manager では、CNAME レコードのみ使用できます。
> 
> 

レコードにも複数の種類があり、それぞれに独自の機能と制約がありますが、Traffic Manager エンドポイントとして構成された Web サイトに関して重要なのは *CNAME* レコードのみです。

### <a name="cname-or-alias-record"></a>CNAME レコードまたはエイリアス レコード
CNAME レコードは、**mail.contoso.com** や **www\.contoso.com** などの "*特定の*" DNS 名を別の (正規の) ドメイン名にマップします。 Traffic Manager を使用した Azure Web サイトの場合、正規のドメイン名は、Traffic Manager プロファイルの **&lt;myapp>.trafficmanager.net** ドメイン名です。 CNAME を作成すると、**&lt;myapp>.trafficmanager.net** ドメイン名のエイリアスが CNAME によって作成されます。 CNAME エントリは **&lt;myapp>.trafficmanager.net** ドメイン名の IP アドレスを自動的に解決するため、Web サイトの IP アドレスが変更されても、特別な対応を行う必要はありません。

Traffic Manager に到着したトラフィックは、構成された負荷分散方法を使用して、Web サイトにルーティングされます。 これは、Web サイトにアクセスするユーザーに対して透過的です。 ユーザーのブラウザーには、カスタム ドメイン名のみが表示されます。

> [!NOTE]
> いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (**contoso.com** など) ではなく、サブドメイン (**www\.contoso.com** など) のみです。 CNAME レコードの詳細については、レジストラーが提供するドキュメント、<a href="https://en.wikipedia.org/wiki/CNAME_record">CNAME レコードに関するウィキペディアの項目</a>、または <a href="https://tools.ietf.org/html/rfc1035">IETF ドメイン名 - 実装と仕様書</a>を参照してください。

