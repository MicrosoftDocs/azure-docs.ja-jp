---
title: Azure Front Door Standard/Premium (プレビュー) を使用した URL リダイレクトと URL 書き換え
description: この記事では、Azure Front Door が Azure Front Door ルール セットを使用して URL リダイレクトと URL 書き換えをサポートする方法を理解するのに役立ちます。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098010"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) を使用した URL リダイレクトと URL 書き換え

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事は、Azure Front Door Standard/Premium が、ルール セットで使用される URL リダイレクトと URL 書き換えにどのように使用されるのかを理解するのに役立ちます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="url-redirect"></a>URL リダイレクト

Azure Front Door を使用すると、プロトコル、ホスト名、パス、クエリ文字列、およびフラグメントの各レベルでトラフィックをリダイレクトできます。 リダイレクトはパス ベースであるため、これらの機能を個々のマイクロサービスに対して構成できます。 URL リダイレクトにより、リソースの使用を最適化することで、アプリケーションの構成を簡約化し、グローバルなパス ベースのリダイレクトを含む、新しいリダイレクト シナリオをサポートすることができます。

URL リダイレクトは、ルール セットを使用して構成できます。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="ルール セットを使用して URL リダイレクトを作成する画面のスクリーンショット。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>リダイレクトの種類
リダイレクトの種類では、クライアントがリダイレクトの目的を理解するための応答の状態コードが設定されます。 次の種類のリダイレクトがサポートされています。

* **301 (永続的に移動)** :ターゲット リソースに新しい永続的な URI が割り当てられていることを示します。 このリソースへの今後の参照によって、囲まれた URI のいずれかが使用されます。 状態コード 301 は HTTP から HTTPS へのリダイレクトに使用します。
* **302 (検出)** :ターゲット リソースが一時的に別の URI に存在することを示します。 リダイレクトは場合によっては変更される可能性があるため、クライアントは今後の要求のために有効な要求 URI を引き続き使用する必要があります。
* **307 (一時的なリダイレクト)** :ターゲット リソースが一時的に別の URI に存在することを示します。 ユーザー エージェントからその URI への自動リダイレクトを行う場合、要求メソッドを変更することはできません。 リダイレクトは時間が経つにつれて変更される可能性があるため、クライアントは今後の要求のために元の有効な要求 URI を引き続き使用する必要があります。
* **308 (永続的なリダイレクト)** :ターゲット リソースに新しい永続的な URI が割り当てられていることを示します。 このリソースへの今後の参照によって、囲まれた URI のいずれかが使用される必要があります。

### <a name="redirection-protocol"></a>リダイレクト プロトコル
リダイレクトに使用されるプロトコルを設定できます。 リダイレクト機能の最も一般的なユース ケースでは、HTTP から HTTPS へのリダイレクトを設定します。

* **HTTPS のみ**:トラフィックを HTTP から HTTPS にリダイレクトしようとする場合、プロトコルを HTTPS のみに設定します。 Azure Front Door では、リダイレクトを常に HTTPS のみに設定することが推奨されています。
* **HTTP のみ**:着信要求を HTTP にリダイレクトします。 この値は、トラフィックを HTTP (暗号化されていない) のままにする場合のみ使用します。
* **一致要求**:このオプションを使用すると、着信要求によって使用されるプロトコルが保持されます。 そのため、HTTP 要求は HTTP のままになり、HTTPS 要求はリダイレクト後も HTTPS のままになります。

### <a name="destination-host"></a>宛先ホスト
リダイレクト ルーティングを構成する一環として、リダイレクト要求のホスト名またはドメインを変更することもできます。 このフィールドを設定して、リダイレクトのために URL のホスト名を変更したり、あるいは着信要求のホスト名を保持したりできます。 したがって、このフィールドを使用すると、`https://www.contoso.com/*` に送信されるすべての要求を `https://www.fabrikam.com/*` にリダイレクトできます。

### <a name="destination-path"></a>宛先のパス
リダイレクトの一環として、URL のパス セグメントを置換する必要がある場合、このフィールドに新しいパスの値を設定できます。 そうしない場合、リダイレクトの一環として、パスの値を保持することを選択できます。 したがって、このフィールドを使用すると、`https://www.contoso.com/\*` に送信されるすべての要求を `https://www.contoso.com/redirected-site` にリダイレクトできます。

### <a name="query-string-parameters"></a>クエリ文字列パラメーター
リダイレクトされる URL のクエリ文字列パラメーターを置き換えることもできます。 着信要求 URL の既存のクエリ文字列を置換するには、このフィールドを [置換] に設定して適切な値を設定します。 そうしない場合、このフィールドを [保持] に設定することによって、元のクエリ文字列のセットを保持することができます。 例えば、このフィールドを使用すると、`https://www.contoso.com/foo/bar`* に送信されるすべてのトラフィックを `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`* にリダイレクトできます。 

### <a name="destination-fragment"></a>宛先フラグメント
宛先フラグメントは URL の '#' より後の部分のことで、これはブラウザーが Web ページの特定のセクションに移動するために使用されます。 このフィールドを設定して、リダイレクト URL にフラグメントを追加できます。

## <a name="url-rewrite"></a>URL 書き換え

Azure Front Door では、配信元に送信中の要求のパスを書き換えるための URL 書き換えがサポートされています。 URL 書き換えでは、条件を追加することで、特定の条件が満たされた場合にのみ、URL または指定したヘッダーが確実に書き換えられるようにできます。 これらの条件は、要求と応答の情報に基づいています。

この機能を使用すると、シナリオ、デバイスの種類、および要求されたファイルの種類に基づいて、異なる配信元にユーザーをリダイレクトできます。

URL リダイレクトは、ルール セットを使用して構成できます。

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="ルール セットを使用して URL 書き換えを作成する画面のスクリーンショット。" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>ソース パターン

ソース パターンとは、置換するソース要求の URL パスです。 現在、ソース パターンではプレフィックスに基づく一致が使用されます。 すべての URL パスを一致させるには、ソース パターン値としてスラッシュ (/) を使用します。

### <a name="destination"></a>到着地

書き換えに使用する宛先パスを定義できます。 宛先のパスは、ソース パターンを上書きします。

### <a name="preserve-unmatched-path"></a>一致しないパスを保持する

[一致しないパスを保持する] を指定すると、ソース パターンの後に残っているパスを新しいパスに追加できます。

たとえば、 **[一致しないパスを保持する] を [はい]** に設定したとします。
* 着信要求が `www.contoso.com/sub/1.jpg` の場合、ソース パターンは `/` に設定され、宛先は `/foo/` に設定されて、コンテンツは配信元の `/foo/sub/1`.jpg から取得されます。

* 着信要求が `www.contoso.com/sub/image/1.jpg` の場合、ソース パターンは `/sub/` に設定され、宛先は `/foo/` に設定されて、コンテンツは配信元の `/foo/image/1.jpg` から取得されます。

たとえば、 **[一致しないパスを保持する] を [いいえ]** に設定したとします。
* 着信要求が `www.contoso.com/sub/image/1.jpg` の場合、ソース パターンは `/sub/` に設定され、宛先は `/foo/2.jpg` に設定されて、`wwww.contoso.com/sub/` でどのパスが後続していてもコンテンツは配信元の `/foo/2.jpg` から取得されます。

## <a name="next-steps"></a>次のステップ

* [Azure Front Door Standard/Premium ルール セットについて](concept-rule-set.md)説明します。
