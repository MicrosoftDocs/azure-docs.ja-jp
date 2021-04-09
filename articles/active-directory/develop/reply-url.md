---
title: リダイレクト URI (応答 URL) に関する制限 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft の ID プラットフォームによって適用される、リダイレクト URI (応答 URL) 形式に関する制約と制限についての説明。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 91df89a69368056c1967e641562cf8515f44ade0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582810"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>リダイレクト URI (応答 URL) に関する制約と制限

リダイレクト URI、すなわち応答 URL は、アプリが正しく承認され、認証コードまたはアクセス トークンが付与されると、承認サーバーがユーザーを送り出す場所です。 承認サーバーは、リダイレクト URI にこのコードまたはトークンを送信するため、アプリ登録プロセスの一環として正しい場所を登録することが重要です。

 リダイレクト URI には、次の制限があります。

* リダイレクト URL は、スキーム `https` で始まる必要があります。 [localhost リダイレクト URI には例外](#localhost-exceptions)がいくつかあります。

* リダイレクト URI では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 たとえば、そのパス `.../abc/response-oidc` の一部としてアプリケーションが含まれている場合に、リダイレクト URL 内で `.../ABC/response-oidc` と指定しないでください。 Web ブラウザーでは大文字と小文字を区別を区別するものとしてパスが処理されるため、`.../abc/response-oidc` に関連付けられている cookie は、大文字と小文字が一致しない `.../ABC/response-oidc` URL にリダイレクトされた場合に除外される可能性があります。

## <a name="maximum-number-of-redirect-uris"></a>リダイレクト URI の最大数

次の表に、Microsoft の ID プラットフォームでアプリの登録に追加できるリダイレクト URI の最大数を示します。

| サインイン中のアカウント | リダイレクト URI の最大数 | 説明 |
|--------------------------|---------------------------------|-------------|
| 組織の Azure Active Directory (Azure AD) テナントにある Microsoft の職場または学校アカウント | 256 | アプリケーション マニフェストの `signInAudience` フィールドは *AzureADMyOrg* か *AzureADMultipleOrgs* に設定されています |
| Microsoft の個人用アカウント、職場用アカウント、学校用アカウント | 100 | アプリケーション マニフェストの `signInAudience` フィールドは *AzureADandPersonalMicrosoftAccount* に設定されています |

## <a name="maximum-uri-length"></a>URI の最大長

アプリの登録に追加するリダイレクト URI ごとに最大 256 文字を使用できます。

## <a name="supported-schemes"></a>サポートされているスキーム

現在、Azure Active Directory (Azure AD) アプリケーション モデルでは、任意の組織の Azure AD テナントで Microsoft の職場または学校アカウントにサインインするアプリに対して、HTTP と HTTPS の両方のスキームがサポートされています。 これらのアカウントの種類は、アプリケーション マニフェストの `signInAudience` フィールドの `AzureADMyOrg` および `AzureADMultipleOrgs` の値によって指定されます。 個人用の Microsoft アカウント (MSA) *に加えて* 職場と学校のアカウントにサインインするアプリ (つまり、`signInAudience` が `AzureADandPersonalMicrosoftAccount` に設定されている) の場合、HTTPS スキームのみが許可されます。

職場または学校のアカウントにサインインするアプリの登録に、HTTP スキームを使用するリダイレクト URI を追加するには、Azure portal の [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) で、アプリケーション マニフェスト エディターを使用します。 ただし、マニフェスト エディターを使用して HTTP ベースのリダイレクト URI を設定することは可能ですが、リダイレクト URI には HTTPS スキームを使用することが *強く* 推奨されます。

## <a name="localhost-exceptions"></a>Localhost の例外

[RFC 8252 のセクション 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) と [7.3](https://tools.ietf.org/html/rfc8252#section-7.3) に従って、"loopback" または "localhost" リダイレクト URI には、次の 2 つの特別な考慮事項があります。

1. リダイレクトがデバイスから切り離されることはないため、`http` URI スキームを指定できます。 そのため、これら両方の URI を使用できます。
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. ネイティブ アプリケーションでは一時的なポート範囲が頻繁に必要とされるため、リダイレクト URI の照合目的では、ポート コンポーネント (`:5001`や `:443` など) は無視されます。 結果として、これらの URI はすべて同等と見なされます。
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

開発の観点から見ると、これはいくつかのことを意味します。

* ポートのみが異なる複数のリダイレクト URI を登録しないでください。 ログイン サーバーでは任意のものが選択され、そのリダイレクト URI に関連付けられている動作が使用されます (たとえば、リダイレクトが `web` 型か、`native` 型か、`spa` 型か)。

    これは、同じアプリケーションの登録で異なる認証フロー (認可コードの付与と暗黙のフローなど) を使用する場合に特に重要です。 各リダイレクト URI に適切な応答動作を関連付けるには、ログイン サーバーがリダイレクト URI を区別できる必要があります。また、ポートのみが異なる場合は、リダイレクト URI を区別できません。
* 複数のリダイレクト URI を localhost に登録して開発中にさまざまなフローをテストする場合は、URI の "*パス*" コンポーネントを使用してそれらを区別します。 たとえば、`http://localhost/MyWebApp` は `http://localhost/MyNativeApp` と一致しません。
* IPv6 ループバック アドレス (`[::1]`) は、現在サポートされていません。

#### <a name="prefer-127001-over-localhost"></a>127.0.0.1 以上の localhost が推奨されています

ファイアウォールの構成が正しくなかったりネットワーク インターフェイスの名前が変更されたりしたことによってアプリが破損しないようにするには、リダイレクト URI で `localhost` ではなく IP リテラル ループバック アドレス `127.0.0.1` を使用します。 たとえば、`https://127.0.0.1` のようにします。

ただし、Azure portal の **[リダイレクト URI]** テキスト ボックスを使用して、`http` スキームを使用するループバックベースのリダイレクト URI を追加することはできません。

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="許可されていない HTTP ベースのループバック リダイレクト URI を示す Azure portal のエラー ダイアログ":::

`127.0.0.1` ループバック アドレスで `http` スキームを使用するリダイレクト URI を追加するには、現在のところ[アプリケーション マニフェスト](reference-app-manifest.md)で [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) 属性を変更する必要があります。

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>リダイレクト URI のワイルドカードに関する制限事項

`https://*.contoso.com` のようなワイルドカード URI は、便利に思えることもありますが、セキュリティへの影響のため、回避する必要があります。 OAuth 2.0 仕様 ([セクション 3.1.2 of RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) によると、リダイレクト エンドポイント URI は絶対 URI にする必要があります。

ワイルドカード URI は、現在、個人用 Microsoft アカウントと職場または学校のアカウントにサインインするように構成されているアプリの登録ではサポートされていません。 ただし、組織の Azure AD テナントで、職場または学校のアカウントにのみサインインするように構成されているアプリの場合は、ワイルドカード URI が許可されます。

職場または学校のアカウントにサインインするアプリの登録に、ワイルドカードを含むリダイレクト URI を追加するには、Azure portal の [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) で、アプリケーション マニフェスト エディターを使用します。 マニフェスト エディターを使用して、ワイルドカードを含むリダイレクト URI を設定することも可能ですが、[RFC 6749 のセクション 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2) に準拠し、絶対 URI のみを使用することが *強く* 推奨されます。

シナリオ上、許可される最大制限を超えるリダイレクト URI が必要な場合は、ワイルドカードを含むリダイレクト URI を追加する代わりに、[次の状態パラメーター アプローチ](#use-a-state-parameter)を検討してください。

#### <a name="use-a-state-parameter"></a>状態パラメーターを使用する

いくつかのサブドメインがあり、シナリオ上それが必要である場合は、認証成功時にユーザーを開始時と同じページにリダイレクトしますが、状態パラメーターを使用すると便利な場合があります。

このアプローチでは:

1. 認証エンドポイントから受け取ったセキュリティ トークンを処理する "共有" リダイレクト URI をアプリケーション別に作成します。
1. アプリケーションでは、状態パラメーターで、アプリケーション固有のパラメーター (ユーザーの起点になったサブドメイン URL やブランド情報のような任意のもの) を送信できます。 状態パラメーターの使用時、CSRF 対策をしてください。仕様は[セクション 10.12 of RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12) にあります。
1. アプリケーション固有のパラメーターには、ユーザーにとって適切なエクスペリエンスをアプリケーションから与えるために、つまり、アプリケーションの適切な状態を構築するために必要な情報がすべて含まれます。 Azure AD 認証エンドポイントにより状態パラメーターから HTML が取り除かれます。そのため、このパラメーターでは HTML コンテンツを渡さないようにしてください。
1. Azure AD から "共有" リダイレクト URI に応答を送信するとき、状態パラメーターがアプリケーションに返されます。
1. その後、ユーザーをさらに送信する宛先となる URL を決定する目的で、状態パラメーターの値をアプリケーションで利用できます。 CSRF 対策の有効性を確認してください。

> [!WARNING]
> この手法では、セキュリティを侵害されたクライアントが状態パラメーターで送信された追加パラメーターを変更し、ユーザーを別の URL にリダイレクトすることを許します。これは RFC 6819 に説明がある[オープン リダイレクターの脅威](https://tools.ietf.org/html/rfc6819#section-4.2.4)です。 そのため、クライアントは状態を暗号化するか、リダイレクト URI に含まれるドメイン名をトークンと比べて検証するなど、何か他の手段で状態を検証することによって、これらのパラメーターを保護する必要があります。

## <a name="next-steps"></a>次のステップ

アプリの登録の[アプリケーション マニフェスト](reference-app-manifest.md)について学習します。
