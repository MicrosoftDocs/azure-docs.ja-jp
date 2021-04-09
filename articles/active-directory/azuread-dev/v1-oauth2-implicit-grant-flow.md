---
title: Azure AD での OAuth2 の暗黙的な許可フローについて | Microsoft Docs
description: OAuth2 の暗黙的な許可フローの Azure Active Directory の実装の詳細と、これが適切なアプリケーションについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154510"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Azure Active Directory (AD) での OAuth2 の暗黙的な許可フローについて

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth2 の暗黙的な許可は、OAuth2 仕様のセキュリティ問題を最も多く含むアクセス許可であることで知られています。 それでも、ADAL JS によって実装されるアプローチであり、SPA アプリケーションを作成するときにお勧めするアプローチでもあります。 何のためでしょう。 これはすべてトレードオフの問題です。結局のところ、暗黙的な許可が、ブラウザーで JavaScript を使用して Web API を使用するアプリケーションのために行うことができる最善のアプローチだからです。

## <a name="what-is-the-oauth2-implicit-grant"></a>OAuth2 の暗黙的な許可とは

典型的な [OAuth2 認証コード付与](https://tools.ietf.org/html/rfc6749#section-1.3.1) は、2 つの別個のエンドポイントを使用する認証付与です。 承認エンドポイントはユーザーによる操作の段階で使用され、その結果、承認コードが生成されます。 続いて、このコードをアクセス トークン (多くの場合は更新トークンも) と交換するために、トークン エンドポイントがクライアントによって使用されます。 Web アプリケーションは、承認サーバーがクライアントを認証できるように、トークン エンドポイントに独自のアプリケーション資格情報を示す必要があります。

[OAuth2 の暗黙的な許可](https://tools.ietf.org/html/rfc6749#section-1.3.2)は、他の認証付与のバリアントです。 トークン エンドポイントにアクセスしたり、クライアントを認証したりすることなく、クライアントがアクセス トークン ([OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html) を使用する場合は id_token) を承認エンドポイントから直接取得できるようにします。 このバリアントは、Web ブラウザーで実行される JavaScript ベースのアプリケーションを想定したものです。元の OAuth2 仕様では、トークンは URI フラグメントとして返されます。 これにより、クライアントの JavaScript コードでトークン ビットを使用できるようになりますが、サーバーへのリダイレクトには含まれないことが保証されます。 OAuth2 の暗黙的な許可では、承認エンドポイントで以前に指定されたリダイレクト URI を使用し、クライアントに直接アクセス トークンを発行します。 これには、クロス オリジン呼び出しに関する要件がなくなるという利点もあります。クロス オリジン呼び出しは、JavaScript アプリケーションがトークン エンドポイントにアクセスしなければならない場合に必要になるものです。

OAuth2 の暗黙的な許可の重要な特性は、このフローでクライアントに更新トークンが返されないという事実です。 これが必要ではなく、実際にはセキュリティ上の問題になりうることを次のセクションで説明します。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 の暗黙的な許可の適切なシナリオ

OAuth2 の仕様で明らかにされているとおり、暗黙的な許可はユーザー エージェント アプリケーション (つまり、ブラウザー内で実行される JavaScript アプリケーション) を実現にするために考案されました。 このようなアプリケーションに特徴的なのは、サーバー リソース (通常は Web API) にアクセスするため、そしてアプリケーション ユーザー エクスペリエンスを適宜更新するために、JavaScript コードが使用されるという点です。 Gmail や Outlook Web Access のようなアプリケーションを考えてみてください。受信トレイからメッセージを選択したときに、メッセージ視覚化パネルのみが変更されて新しい選択内容が表示され、ページの残りの部分は変更されません。 この特性は、ユーザー操作ごとにページ全体がポストバックされ、新しいサーバー応答によりページ全体がレンダリングされる、従来のリダイレクト ベースの Web アプリとは対照的です。

JavaScript ベースのアプローチを最大限に活用するアプリケーションは、シングルページ アプリケーション (SPA) と呼ばれます。 これらのアプリケーションは最初の HTML ページと関連する JavaScript のみを提供し、後続のすべてのやりとりは JavaScript を介して実行される Web API 呼び出しによって行われるという考えです。 ただし、ハイブリッド アプローチも珍しくはありません。このアプローチでは、アプリケーションはほとんどの場合ポストバックに基づきますが、JS 呼び出しを実行することもあります。暗黙的フローの使用方法に関する議論は、ハイブリッド アプローチにも当てはまります。

リダイレクト ベースのアプリケーションでは通常、Cookie を使用して要求をセキュリティ保護します。ただし、このアプローチは JavaScript アプリケーションではうまくいきません。 Cookie はその生成元のドメインに対してのみ機能しますが、JavaScript 呼び出しは他のドメインにリダイレクトされる可能性があるためです。 これは実際、多くの場合に発生します。Microsoft Graph API、Office API、Azure API を呼び出すアプリケーションを考えてみてください。これらの API はいずれもアプリケーションが動作するドメインの外部にあります。 JavaScript アプリケーションでは、バックエンドを一切持たず、サード パーティの Web API に 100% 依存してビジネス機能を実装する傾向が強まっています。

現時点で推奨される Web API 呼び出しの保護方法は、OAuth2 べアラー トークンのアプローチを使用する方法です。このアプローチでは、すべての呼び出しに OAuth2 のアクセス トークンが付随します。 Web API は受信したアクセス トークンを調べ、アクセス トークン内に必要なスコープが見つかった場合に、要求された操作へのアクセス許可を与えます。 暗黙的フローは、JavaScript アプリケーションが Web API 用のアクセス トークンを取得するための便利なメカニズムを提供するものであり、Cookie に関してさまざまなメリットがあります。

* クロス オリジン呼び出しをしなくても、トークンを確実に取得できます。トークンが返されるリダイレクト URI の登録が必須であるため、トークンが置換されないことが保証されます。
* JavaScript アプリケーションは、ドメインの制限なしに、対象とする Web API の数だけ、必要な数のアクセス トークンを取得できます。
* セッションやローカル ストレージなどの HTML5 機能ではトークンのキャッシュと有効期間管理にフル コントロールを許可しますが、Cookie の管理はアプリにとって非透過的です。
* アクセス トークンはクロスサイト リクエスト フォージェリ (CSRF) 攻撃をあまり受けません

暗黙的な許可フローでは、主にセキュリティ上の理由から、更新トークンを発行しません。 更新トークンはアクセス トークンほどスコープが狭義ではないため、多くの権限を付与すると、リークされた場合のダメージが大きくなります。暗黙的フローでは、トークンは URL の形式で配信されるため、傍受されるリスクが認証コード付与よりも高くなります。

ただし、JavaScript アプリケーションには、アクセス トークンを更新するために廃棄する際、ユーザーに資格情報を繰り返し求めない別のメカニズムがあります。 アプリケーションは非表示の iframe を使用して、Azure AD の承認エンドポイントに対して新しいトークン要求を実行します。ブラウザーが Azure AD ドメインに対してアクティブなセッション (読み取り: セッション Cookie あり) を持っている限り、この認証要求は正常に行われ、ユーザーの操作は必要ありません。

このモデルにより、JavaScript アプリケーションは独立してアクセス トークンを更新できるようになるほか、(ユーザーが事前に同意している場合は) 新しい API の新しいアクセス トークンを取得することさえ可能になります。 これにより、更新トークンなどの高価値のアーティファクトを取得、管理、保護する負担を増やさずに済みます。 サイレント更新を可能にするアーティファクト、Azure AD のセッション Cookie は、アプリケーションの外部で管理されます。 このアプローチのもう 1 つの利点は、いずれかのブラウザー タブで実行されている、Azure AD にサインインしているいずれかのアプリケーションを使用して、ユーザーが Azure AD からサインアウトできることです。 サインアウトすると、Azure AD のセッション Cookie が削除され、JavaScript アプリケーションは自動的に、サインアウトしたユーザーのトークンを更新できなくなります。

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>暗黙的な許可に適切なアプリ

暗黙的な付与は他の付与よりリスクが大きくなります。また、注意を払うべき領域が詳しく記録されます (たとえば、「[Misuse of Access Token to Impersonate Resource Owner in Implicit Flow (暗黙的フローでの偽装リソース所有者に対するアクセス トークンの誤用)][OAuth2-Spec-Implicit-Misuse]」や「[OAuth 2.0 Threat Model and Security Considerations (OAuth 2.0 の脅威モデルとセキュリティの考慮事項)][OAuth2-Threat-Model-And-Security-Implications]」)。 ただし、よりリスクが高いプロファイルは、多くの場合、リモート リソースによってブラウザーに対して処理されるアクティブ コードを実行するアプリケーションを有効にしなければならないという事実に起因します。 SPA アーキテクチャを計画していて、バックエンド コンポーネントがない場合、または JavaScript を使用して Web API を呼び出そうとしている場合は、トークンの取得に暗黙的フローを使用することをお勧めします。

アプリケーションがネイティブ クライアントの場合は、暗黙的フローはあまり向いていません。 ネイティブ クライアントのコンテキストには Azure AD のセッション Cookie がないので、アプリケーションには存続期間の長いセッションを維持する手段がありません。 つまり、アプリケーションは新しいリソースのアクセス トークンを取得する場合、繰り返しユーザーに求めることになります。

バックエンドを含む Web アプリケーションを開発しており、そのバックエンド コードから API を使用する場合も、暗黙的フローはあまり向いていません。 他の方法の方がはるかに便利です。 たとえば、OAuth2 クライアント資格情報付与では、ユーザー委任とは対照的に、アプリケーション自体に割り当てられているアクセス許可を反映したトークンを取得できます。 これは、ユーザーがセッションにアクティブに関与していない場合などでも、クライアントがプログラムによるリソース アクセスを維持できることを意味します。 メリットはそれだけにとどまりません。このような付与では、セキュリティ保証が強化されます。 たとえば、アクセス トークンがユーザーのブラウザーを通過せず、ブラウザーの履歴に保存されるなどのリスクがありません。 また、クライアント アプリケーションは、トークンの要求時に強力な認証を実行できます。

## <a name="next-steps"></a>次のステップ

* アプリケーションの統合プロセスの詳細については、[アプリケーションを Azure AD と統合する方法][ACOM-How-To-Integrate]についてのページを参照してください。

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
