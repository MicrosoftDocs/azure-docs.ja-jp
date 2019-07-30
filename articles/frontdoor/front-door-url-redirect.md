---
title: Azure Front Door Service - URL のリダイレクト | Microsoft Docs
description: この記事では、構成されている場合に、Azure Front Door Service がそれらのルートの URL リダイレクトをサポートする仕組みについて説明します。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332283"
---
# <a name="url-redirect"></a>URL リダイレクト
Azure Front Door Service を使用して、トラフィックをリダイレクトできます。 トラフィックを複数のレベル (プロトコル、ホスト名、パス、クエリ文字列) でリダイレクトできます。また、リダイレクトはパスに基づくため、すべての機能を個々のマイクロサービスに対して構成できます。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。
</br>

![Azure Front Door の URL リダイレクト][1]

## <a name="redirection-types"></a>リダイレクトの種類
リダイレクトの種類では、クライアントがリダイレクトの目的を理解するための応答の状態コードが設定されます。 次の種類のリダイレクトがサポートされています。

- **301 (永続的に移動)** :ターゲット リソースに新しい永続 URI が割り当てられ、このリソースへの今後のすべての参照では、囲まれているいずれかの URI を使用する必要があることを示します。 状態コード 301 は HTTP から HTTPS へのリダイレクトに使用します。 
- **302 (検出)** :ターゲット リソースが一時的に別の URI に存在することを示します。 リダイレクトは場合によっては変更される可能性があるため、クライアントは今後の要求のために有効な要求 URI を引き続き使用する必要があります。
- **307 (一時的なリダイレクト)** :ターゲット リソースが一時的に別の URI に存在しており、ユーザー エージェントはその URI への自動リダイレクトを実行する場合に要求メソッドを変更 "してはならない" ことを示します。 リダイレクトは時間が経つにつれて変更される可能性があるため、クライアントは今後の要求のために元の有効な要求 URI を引き続き使用する必要があります。
- **308 (永続的なリダイレクト)** :ターゲット リソースに新しい永続 URI が割り当てられ、このリソースへの今後のすべての参照では、囲まれているいずれかの URI を使用する必要があることを示します。 リンク編集機能を持つクライアントは、可能な場合、有効な要求 URI への参照を、サーバーによって送信された 1 つ以上の新しい参照に自動的に再リンクする必要があります。

## <a name="redirection-protocol"></a>リダイレクト プロトコル
リダイレクトに使用されるプロトコルを設定できます。 これにより、リダイレクト機能の最も一般的なユース ケースである、HTTP から HTTPS へのリダイレクトの設定が可能になります。

- **HTTPS のみ**:トラフィックを HTTP から HTTPS にリダイレクトしようとする場合、プロトコルを HTTPS のみに設定します。 Azure Front Door Service は、リダイレクトを常に HTTPS のみに設定することを推奨しています。
- **HTTP のみ**:これは着信要求を HTTP にリダイレクトします。 この値は、トラフィックを HTTP (暗号化されていない) のままにする場合のみ使用します。
- **一致要求**:このオプションでは、着信要求によって使用されるプロトコルが保持されます。 そのため、HTTP 要求は HTTP のままになり、HTTPS 要求はリダイレクト後も HTTPS のままになります。

## <a name="destination-host"></a>宛先ホスト
リダイレクト ルーティングを構成する一環として、リダイレクト要求のホスト名またはドメインを変更することもできます。 このフィールドを設定して、リダイレクトのために URL のホスト名を変更したり、あるいは着信要求のホスト名を保持したりできます。 したがって、このフィールドを使用すると、 https://www.contoso.com/ * に送信されるすべての要求を https://www.fabrikam.com/ * にリダイレクトできます。

## <a name="destination-path"></a>宛先のパス
リダイレクトの一環として、URL のパス セグメントを置換する必要がある場合、このフィールドに新しいパスの値を設定できます。 そうしない場合、リダイレクトの一環として、パスの値を保持することを選択できます。 したがって、このフィールドを使用すると、 https://www.contoso.com/ * に送信されるすべての要求を https://www.contoso.com/redirected-site * にリダイレクトできます。

## <a name="query-string-parameters"></a>クエリ文字列パラメーター
リダイレクトされる URL のクエリ文字列パラメーターを置き換えることもできます。 着信要求 URL の既存のクエリ文字列を置換するには、このフィールドを [置換] に設定して適切な値を設定します。 そうしない場合、このフィールドを [保持] に設定することによって、元のクエリ文字列のセットを保持することができます。 例えば、このフィールドを使用すると、 https://www.contoso.com/foo/bar * に送信されるすべてのトラフィックを https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F * にリダイレクトできます。 

## <a name="destination-fragment"></a>宛先フラグメント
宛先フラグメントは URL の '#' より後の部分のことで、通常はブラウザーがページの特定のセクションに移動するために使用します。 このフィールドを設定して、リダイレクト URL にフラグメントを追加できます。

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png