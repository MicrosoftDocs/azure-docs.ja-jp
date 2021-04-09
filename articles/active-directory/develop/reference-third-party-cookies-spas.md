---
title: Safari で Intelligent Tracking Protection (ITP) を扱う方法 | Azure
titleSuffix: Microsoft identity platform
description: サードパーティの Cookie が許可されなくなった場合のシングルページ アプリ (SPA) 認証。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753970"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>サードパーティの Cookie がブロックされている Safari やその他のブラウザーで ITP を扱う

現在、多くのブラウザーでサードパーティの Cookie (ブラウザー バーに表示されるものとは異なるドメインに対する要求の Cookie) がブロックされています。 これにより、暗黙的なフローが中断され、ユーザーを正常にサインインさせるための新しい認証パターンが必要になります。 Microsoft ID プラットフォームでは、サードパーティの Cookie がブロックされている場合にユーザーのサインインを維持するために、PKCE と更新トークンで承認フローを使用します。

## <a name="what-is-intelligent-tracking-protection-itp"></a>Intelligent Tracking Protection (ITP) とは

Apple Safari では、[Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/) (*ITP*) と呼ばれるプライバシー保護機能が既定でオンになっています。 ITP では、"サードパーティ" の Cookie (複数のドメインにまたがる要求の Cookie) がブロックされます。

一般的な形式のユーザー追跡は、バックグラウンドで iframe をサードパーティ サイトに読み込み、Cookie を使用してインターネット経由でユーザーを関連付けることによって行われます。 残念ながら、このパターンは、シングルページ アプリ (SPA) で[暗黙的なフロー](v2-oauth2-implicit-grant-flow.md)を実装するための標準的な方法でもあります。 ユーザー追跡を防ぐためにブラウザーでサードパーティの Cookie をブロックすると、SPA も中断されます。

ユーザーのプライバシーを強化するためにサードパーティの Cookie をブロックするのは、Safari だけではありません。 Brave では既定でサードパーティの Cookie がブロックされており、Chromium (Google Chrome と Microsoft Edge の背後にあるプラットフォーム) では、今後サードパーティの Cookie のサポートを停止した方がよいと発表しました。

この記事で概説するソリューションは、これらすべてのブラウザー、またはサードパーティの Cookie がブロックされるあらゆる場所で機能します。

## <a name="overview-of-the-solution"></a>ソリューションの概要

SPA でユーザーの認証を続行するには、アプリ開発者が[認可コード フロー](v2-oauth2-auth-code-flow.md)を使用する必要があります。 認可コード フローでは、ID プロバイダーによってコードが発行され、SPA でアクセス トークンと更新トークンのコードが引き換えられます。 アプリで追加のトークンが必要な場合は、[更新トークン フロー](v2-oauth2-auth-code-flow.md#refresh-the-access-token)を使用して新しいトークンを取得できます。 SPA 用の Microsoft ID プラットフォーム ライブラリである MSAL.js 2.0 では、SPA の認可コード フローを実装し、マイナー更新を行って、MSAL.js 1.x の代わりとして、そのまま使用することができます。

Microsoft ID プラットフォームの場合、SPA とネイティブ クライアントでは以下の同様のプロトコル ガイダンスに従います。

* [PKCE コード チャレンジ](https://tools.ietf.org/html/rfc7636)を使用する
    * Microsoft ID プラットフォーム上の SPA では、PKCE が "*必須*" となります。 ネイティブおよび Confidential クライアントでは、PKCE が "*推奨*" されます。
* クライアント シークレットを使用しない

SPA にはさらに 2 つの制約があります。

* ログイン エンドポイントで CORS を有効にするには、[リダイレクト URI が `spa` という種類としてマークされている必要があります](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps)。
* `spa` リダイレクト URI に認可コード フローを通じて発行される更新トークンには、90 日の有効期間ではなく、24 時間の有効期間があります。

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="シングル ページ アプリとセキュリティ トークン サービス エンドポイントの間の OAuth 2 認証コード フローを示す図。" border="false":::

## <a name="performance-and-ux-implications"></a>パフォーマンスと UX への影響

暗黙的なフローを使用する一部のアプリケーションでは、`prompt=none` を使ってログイン iframe を開くことで、リダイレクトすることなくサインインが試行されます。 ほとんどのブラウザーでは、この要求で、現在サインインしているユーザーのトークンを使用して応答します (既に同意が得られていることが前提)。 このパターンは、アプリケーションでユーザーをサインインさせるためのフル ページ リダイレクトが不要であり、パフォーマンスとユーザー エクスペリエンスが向上することを意味していました。この場合、ユーザーは Web ページにアクセスし、既にサインインしています。 サードパーティの Cookie がブロックされている場合、iframe の `prompt=none` はオプションではなくなるため、アプリケーションでは、最上位フレームのログイン ページにアクセスして認可コードを発行する必要があります。

サインインを実現するには、次の 2 つの方法があります。

* **フル ページ リダイレクト**
    * SPA の最初の読み込み時に、セッションがまだ存在しない場合 (またはセッションの有効期限が切れている場合) に、ユーザーをサインイン ページにリダイレクトします。 ユーザーのブラウザーではログイン ページにアクセスし、ユーザー セッションを含む Cookie を提示してから、フラグメントのコードとトークンを使用してアプリケーションに再びリダイレクトします。
    * このリダイレクトによって、SPA が 2 回読み込まれることになります。 SPA のキャッシュのベスト プラクティスに従って、アプリが完全に 2 回ダウンロードされないようにします。
    * アプリで JavaScript ペイロードを完全にアンパックして実行する前に、ログイン セッションを確認し、ログイン ページにリダイレクトするアプリではプリロード シーケンスを使用することを検討してください。
* **ポップアップ**
    * フル ページ リダイレクトのユーザー エクスペリエンス (UX) がアプリケーションで機能しない場合は、ポップアップを使用して認証を扱うことを検討してください。
    * 認証後にポップアップでアプリケーションへのリダイレクトが完了すると、リダイレクト ハンドラーのコードによって、アプリケーションで使用するためにローカル ストレージにコードとトークンが格納されます。 MSAL.js では、ほとんどのライブラリの場合と同様に、認証用のポップアップがサポートされています。
    * ブラウザーではポップアップのサポートが減少しているため、最も信頼性の高いオプションではない場合があります。 ブラウザーの要件を満たすには、ポップアップを作成する前にユーザーと SPA とのやり取りが必要になる場合があります。

>[!NOTE]
> Apple では、元のウィンドウからサードパーティの Cookie にアクセスできるようにするための一時的な互換性修正プログラムとして、[ポップアップ手法について説明](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)しています。 Apple では今後、このアクセス許可の譲渡をなくす可能性がありますが、このガイダンスには影響しません。 ここでは、ログイン ページへのファースト パーティのナビゲーションとしてポップアップが使用されているため、セッションが検出され、認可コードを提供できます。 そのため、今後も引き続きご利用いただけるはずです。

### <a name="a-note-on-iframe-apps"></a>iframe アプリに関する注意事項

Web アプリの一般的なパターンでは、iframe を使用して、あるアプリを別のものに埋め込みます。 最上位フレームではユーザーの認証が扱われ、iframe でホストされているアプリケーションでは、ユーザーがサインインしていることを信頼でき、トークンは暗黙的なフローを使用してサイレントにフェッチされます。 サードパーティの Cookie がブロックされている場合、サイレント トークンの取得は機能しなくなりました。つまり、iframe に埋め込まれたアプリケーションでは、ログイン ページに移動できないため、ポップアップを使用してユーザーのセッションにアクセスするように切り替える必要があります。

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>ブラウザーでの更新トークンのセキュリティへの影響

更新トークンをブラウザーに発行することは、セキュリティ上の問題と見なされます。 クロスサイト スクリプティング (XSS) 攻撃または JS パッケージの侵害により、更新トークンが盗まれ、有効期限が切れるか失効するまでリモートで使用される可能性があります。 盗まれた更新トークンのリスクを最小限に抑えるために、SPA には 24 時間のみ有効なトークンが発行されます。 24 時間後、アプリでは、ログイン ページにアクセスするために最上位フレームを介して新しい認証コードを取得する必要があります。

この有効期間が制限された更新トークンのパターンは、セキュリティと低下した UX のバランスを取るために選ばれました。 更新トークンやサードパーティの Cookie がないと、新しいトークンまたは追加のトークンが必要になったときに、認可コード フロー ([OAuth セキュリティのベスト プラクティス ドラフト](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)で推奨) が煩雑になります。 トークンが期限切れになるたび (Microsoft ID プラットフォームのトークンの場合、通常は 1 時間ごと) に、すべてのシングル トークンでフル ページ リダイレクトまたはポップアップが必要になります。

## <a name="next-steps"></a>次のステップ

[認可コード フロー](v2-oauth2-auth-code-flow.md)について、さらに学習します。

[MSAL.js 2.0 クイックスタート](quickstart-v2-javascript-auth-code.md)で認可コード フローを試してみます。
