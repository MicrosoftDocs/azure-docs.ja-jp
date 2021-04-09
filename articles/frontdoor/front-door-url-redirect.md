---
title: Azure Front Door - URL のリダイレクト | Microsoft Docs
description: この記事は、Azure Front Door によってそれらのルーティング規則の URL リダイレクトがサポートされるしくみについて理解するのに役立ちます。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91442044"
---
# <a name="url-redirect"></a>URL リダイレクト
Azure Front Door を使用すると、プロトコル、ホスト名、パス、クエリ文字列の各レベルでトラフィックをリダイレクトできます。 リダイレクトはパス ベースであるため、これらの機能を個々のマイクロサービスに対して構成できます。 これにより、リソースの使用を最適化することで、アプリケーションの構成を簡約化し、グローバルなパス ベースのリダイレクトを含む、新しいリダイレクト シナリオをサポートすることができます。
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure Front Door の URL リダイレクト":::

## <a name="redirection-types"></a>リダイレクトの種類
リダイレクトの種類では、クライアントがリダイレクトの目的を理解するための応答の状態コードが設定されます。 次の種類のリダイレクトがサポートされています。

- **301 (永続的に移動)** :ターゲット リソースに新しい永続的な URI が割り当てられていることを示します。 このリソースへの今後の参照によって、囲まれた URI のいずれかが使用されます。 状態コード 301 は HTTP から HTTPS へのリダイレクトに使用します。 
- **302 (検出)** :ターゲット リソースが一時的に別の URI に存在することを示します。 リダイレクトは場合によっては変更される可能性があるため、クライアントは今後の要求のために有効な要求 URI を引き続き使用する必要があります。
- **307 (一時的なリダイレクト)** :ターゲット リソースが一時的に別の URI に存在することを示します。 ユーザー エージェントからその URI への自動リダイレクトを行う場合、要求メソッドを変更することはできません。 リダイレクトは時間が経つにつれて変更される可能性があるため、クライアントは今後の要求のために元の有効な要求 URI を引き続き使用する必要があります。
- **308 (永続的なリダイレクト)** :ターゲット リソースに新しい永続的な URI が割り当てられていることを示します。 このリソースへの今後の参照によって、囲まれた URI のいずれかが使用される必要があります。

## <a name="redirection-protocol"></a>リダイレクト プロトコル
リダイレクトに使用されるプロトコルを設定できます。 リダイレクト機能の最も一般的なユース ケースでは、HTTP から HTTPS へのリダイレクトを設定します。

- **HTTPS のみ**:トラフィックを HTTP から HTTPS にリダイレクトしようとする場合、プロトコルを HTTPS のみに設定します。 Azure Front Door では、リダイレクトを常に HTTPS のみに設定することが推奨されています。
- **HTTP のみ**:着信要求を HTTP にリダイレクトします。 この値は、トラフィックを HTTP (暗号化されていない) のままにする場合のみ使用します。
- **一致要求**:このオプションを使用すると、着信要求によって使用されるプロトコルが保持されます。 そのため、HTTP 要求は HTTP のままになり、HTTPS 要求はリダイレクト後も HTTPS のままになります。

## <a name="destination-host"></a>宛先ホスト
リダイレクト ルーティングを構成する一環として、リダイレクト要求のホスト名またはドメインを変更することもできます。 このフィールドを設定して、リダイレクトのために URL のホスト名を変更したり、あるいは着信要求のホスト名を保持したりできます。 したがって、このフィールドを使用すると、`https://www.contoso.com/*` に送信されるすべての要求を `https://www.fabrikam.com/*` にリダイレクトできます。

## <a name="destination-path"></a>宛先のパス
リダイレクトの一環として、URL のパス セグメントを置換する必要がある場合、このフィールドに新しいパスの値を設定できます。 そうしない場合、リダイレクトの一環として、パスの値を保持することを選択できます。 したがって、このフィールドを使用すると、`https://www.contoso.com/\*` に送信されるすべての要求を `https://www.contoso.com/redirected-site` にリダイレクトできます。

## <a name="query-string-parameters"></a>クエリ文字列パラメーター
リダイレクトされる URL のクエリ文字列パラメーターを置き換えることもできます。 着信要求 URL の既存のクエリ文字列を置換するには、このフィールドを [置換] に設定して適切な値を設定します。 そうしない場合、このフィールドを [保持] に設定することによって、元のクエリ文字列のセットを保持することができます。 例えば、このフィールドを使用すると、`https://www.contoso.com/foo/bar`* に送信されるすべてのトラフィックを `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`* にリダイレクトできます。 

## <a name="destination-fragment"></a>宛先フラグメント
宛先フラグメントは URL の '#' より後の部分のことで、これはブラウザーが Web ページの特定のセクションに移動するために使用されます。 このフィールドを設定して、リダイレクト URL にフラグメントを追加できます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
