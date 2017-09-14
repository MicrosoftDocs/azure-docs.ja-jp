---
title: "Azure Active Directory の条件付きアクセスについての開発者ガイド | Microsoft Docs"
description: "開発者ガイドと Azure AD の条件付きアクセスのシナリオ"
services: active-directory
keywords: 
author: danieldobalian
manager: mbaldwin
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.assetid: 115bdab2-e1fd-4403-ac15-d4195e24ac95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: b8fac1b258535fd668b45acbe2c1c8580fb8a340
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory の条件付きアクセスについての開発者ガイド

Azure Active Directory (AD) では、アプリをセキュリティ保護し、サービスを守るための方法が複数提供されています。  これらの独自機能の 1 つとして、条件付きアクセスがあります。  条件付きアクセスを使用することで、開発者や企業のお客様は、次のようなさまざまな方法でサービスを保護できます。

* 多要素認証
* Intune 登録されているデバイスのみに特定のサービスへのアクセスを許可します。
* ユーザーの場所と IP 範囲を制限します。

条件付きアクセスのすべての機能の詳細については、「[Azure クラシック ポータルの条件付きアクセス](../active-directory-conditional-access.md)」を参照してください。 

この記事では、Azure AD のアプリを構築する開発者にとって条件付きアクセス機能が持つ意味について説明します。  この記事は、[シングル](active-directory-integrating-applications.md)および[マルチテナント](active-directory-devhowto-multi-tenant-overview.md) アプリおよび[一般的な認証のパターン](active-directory-authentication-scenarios.md)の知識を持っているユーザーを対象とします。

条件付きアクセス ポリシーが適用され制御できないリソースへのアクセスの影響について説明します。  さらに、On-Behalf-Of フロー、Web アプリ、Microsoft Graph へのアクセス、API の呼び出しに対し条件付きアクセスが与える影響について説明します。

## <a name="how-does-conditional-access-impact-an-app"></a>条件付きアクセスがアプリに与える影響

### <a name="app-topologies-impacted"></a>アプリケーション トポロジが影響を受ける

最も一般的なケースは、条件付きアクセスによってアプリの動作が変更されない、または開発者からの変更を必要としない場合です。  アプリが間接的、またはサイレントでサービスのトークンを要求する特定の場合のみ、アプリが条件付きアクセス "チャレンジ" を処理するためにコードを変更する必要があります。  これは、対話型のサインインを要求するだけで実行できる場合があります。 

具体的には、次のシナリオでは、条件付きアクセス "問題" に対応するためコードが必要になります。 

* Microsoft Graph にアクセスするアプリ
* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* ADAL.js を使用するシングル ページ アプリケーション

条件付きアクセス ポリシーは、アプリに適用できますが、アプリがアクセスする Web API にも適用できます。 条件付きアクセス ポリシーの構成方法については、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azuread-connected-apps.md#configure-per-application-access-rules)」を参照してください。

シナリオによっては、企業のお客様は、条件付きアクセス ポリシーをいつでも適用/削除できます。  新しいポリシーが適用されたときにアプリの機能を継続するためには、"チャレンジ" 処理を実装する必要があります。 チャレンジ処理の例は次のとおりです。 

### <a name="conditional-access-examples"></a>条件付きアクセスの例

シナリオによっては、条件付きアクセスを処理するためにコードの変更が必要なものと、不要なものがあります。  これは、それぞれの違いをわかりやすく説明する、条件付きアクセスを使用した多要素認証のシナリオです。

* シングル テナントの iOS アプリを構築して、条件付きアクセス ポリシーを適用するとします。  アプリがユーザーのサインインを処理し、API へのアクセスは要求されません。  ユーザーがサインインすると、ポリシーが自動的に呼び出され、ユーザーは、多要素認証 (MFA) を実行する必要があります。 
* Microsoft Graph を使用して Exchange などのサービスにアクセスするマルチテナント Web アプリを作成します。  このアプリを採用する企業のお客様は、SharePoint Online のポリシーを設定します。  Web アプリが Microsoft Graph のためのトークンを要求すると、Microsoft Service 上 (具体的には Graph からアクセスできるサービス) のポリシーが適用されます。  このエンドユーザーには、MFA が要求されます。 この場合、有効なトークンでエンドユーザーがサインインすると、Web アプリに "チャレンジ" 要求が返されます。  
* 中間層サービスを使用して Microsoft Graph にアクセスするネイティブ アプリケーションを構築しているとします。  このアプリを使用する会社のお客様は、Exchange Online にポリシーを適用ます。  エンドユーザーがサインインすると、ネイティブ アプリケーションは中間層へのアクセスを要求し、トークンを送信します。  中間層では On-Behalf-Of フローが実行され、MS Graph へのアクセスが要求されます。  この時点では、"チャレンジ" 要求は、中間層に提示されます。 中間層では、条件付きアクセス ポリシーに準拠する必要があるネイティブのアプリケーションにチャレンジを送信します。

### <a name="complying-with-a-conditional-access-policy"></a>条件付きアクセス ポリシーへの準拠

その他のアプリケーション トポロジでは、セッションが確立されたときに条件付きアクセス ポリシーが評価されます。  条件付きアクセス ポリシーは、アプリやサービスの粒度に従って動作するため、実行しようとしているシナリオによって呼び出される点が大きく異なります。

アプリが条件付きアクセス ポリシーを使用してサービスにアクセスしようとすると、条件付きアクセスのチャレンジが発生する可能性があります。  このチャレンジは、Azure AD または Microsoft Graphからの応答に含まれる `claims` パラメーターにエンコードされています。  このチャレンジ パラメーターの例を次に示します。 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開発者は、このチャレンジを取得して、Azure AD への新しい要求に追加できます。  この状態を渡すと、条件付きアクセス ポリシーに準拠するために必要な操作を実行するよう、エンドユーザーに対して求められます。 次のシナリオでは、エラーおよびパラメーターを抽出する方法について説明します。 

## <a name="scenarios"></a>シナリオ

### <a name="prerequisites"></a>前提条件

Azure AD の条件付きアクセスは、[Azure AD Premium](../active-directory-whatis.md#choose-an-edition) に含まれている機能です。  ライセンス要件の詳細については、[ライセンスのないユーザーのレポート](../active-directory-conditional-access-unlicensed-usage-report.md)に関するページを参照してください。  開発者は、Azure AD Premium を含む Enterprise Mobility Suite に無料でサブスクリプションできる [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) に参加できます。

### <a name="considerations-for-specific-scenarios"></a>特定のシナリオの考慮事項

次の情報は、これらの条件付きアクセス シナリオでのみ適用されます。

* Microsoft Graph にアクセスするアプリ
* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* ADAL.js を使用するシングル ページ アプリケーション

次のセクションでは、もう少し複雑な一般的なシナリオについて説明します。  基本的な運用原則では、条件付きアクセス ポリシーは、Microsoft Graph を通してアクセスされた場合を除いて、条件付きアクセス ポリシーが適用されるサービスのトークンが要求されたときに評価されます。

### <a name="scenario-app-accessing-the-microsoft-graph"></a>シナリオ: Microsoft Graph にアクセスするアプリ

このシナリオでは、Web アプリによって Microsoft Graph へのアクセスを要求する場合について説明します。 この場合、条件付きアクセス ポリシーは、SharePoint、Exchange、または Microsoft Graph を通じてワークロードとしてアクセスされるその他のサービスに割り当てられます。  この例では、Sharepoint Online に条件付きアクセス ポリシーがあると仮定します。

![Microsoft Graph にアクセスするアプリのフロー ダイアグラム](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

アプリは、まず条件付きアクセスを使用せず、ダウンストリーム ワークロードにアクセスする必要がある Microsoft Graph に承認を要求します。  ポリシーが呼び出されることなく要求は成功し、アプリは Microsoft Graph のトークンを受信します。  この時点では、アプリは、要求されたエンドポイントに対して、ベアラー要求でアクセス トークンを使用できます。 ここで、アプリは、たとえば Microsoft Graph の Sharepoint Online のエンドポイントにアクセスする必要があります。`https://graph.microsoft.com/v1.0/me/mySite`

アプリは Microsoft Graph に有効なトークンを既に取得しているため、新しいトークンが発行されなくても新しい要求を実行できます。 この要求は失敗し、```WWW-Authenticate``` チャレンジ HTTP 403 アクセス禁止の形式で 要求チャレンジが Microsoft Graph から発行されます。
応答の例を次に示します。 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

要求チャレンジは ```WWW-Authenticate``` ヘッダーに含まれ、次の要求の要求パラメーターを抽出するために解析できます。  要求チャレンジを新しい要求に追加すると、Azure AD でユーザーがサインインしたときに条件付きアクセス ポリシーが評価され、アプリは条件付きアクセス ポリシーに準拠します。  Sharepoint Online のエンドポイントに同じ要求を繰り返しても成功します。

要求チャレンジを処理するコード サンプルについては、ADAL .NET 用 [.NET デスクトップのコード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop)または、ADAL .NET 用 [On-Behalf-Of コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。

### <a name="scenario-app-performing-the-on-behalf-of-flow"></a>シナリオ: On-Behalf-Of フローを実行するアプリ

このシナリオでは、ネイティブ アプリが Web サービス/API を呼び出す場合について説明します。  呼び出されたサービスは、[On-Behalf-Of フロー](active-directory-authentication-scenarios.md#application-types-and-scenarios)でダウンストリーム サービスを呼び出します。  ここでは、ダウンストリーム サービス (Web API 2) に、条件付きアクセス ポリシーを適用し、サーバー/デーモン アプリケーションではなく、ネイティブ アプリケーションを使用しています。 

![On-Behalf-Of フローを実行するアプリのフロー ダイアグラム](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Web API 1 の最初のトークン要求では、Web API 1 がダウンストリーム API にアクセスしない場合もあるため、エンドユーザーに多要素認証は求められません。  Web API 1 で Web API 2 のユーザーの On-Behalf-Of トークンが要求されると、ユーザーは多要素認証されていないために、要求が失敗します。

Azure AD は、いくつかの興味深いデータを HTTP 応答で返します。 

> [!NOTE]
> この場合は、多要素認証エラーの説明ですが、条件付きアクセスに関連するさまざまな `interaction_required` である可能性があります。  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

この Web API 1 では、エラー `error=interaction_required` をキャッチし、`claims` チャレンジをデスクトップ アプリケーションに返送します。  この時点では、デスクトップ アプリケーションは新しい `acquireToken()` 呼び出しを行い、追加のクエリ文字列パラメーターとして `claims` チャレンジを追加できます。  この新しい要求では、ユーザーは多要素認証を実行し、この新しいトークンを Web API 1 に送信し、On-Behalf-Of フローを完了する必要があります。

このシナリオを試すには、[.NET コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。  これは、Web API 1 から返された要求チャレンジをネイティブ アプリケーションに渡し、クライアント アプリケーション内で新しい要求を作成する方法を示しています。 

### <a name="scenario-app-accessing-multiple-services"></a>シナリオ: 複数のサービスにアクセスするアプリ

このシナリオでは、Web アプリが、1 つに条件付きアクセス ポリシーが割り当てられている 2 つのサービスにアクセスする場合について説明します。  アプリケーション ロジックによっては、Web アプリが両方の Web サービスにアクセスする必要のないパスが存在する場合があります。  このシナリオでは、トークンを要求する順序が、エンド ユーザー エクスペリエンスに重要な役割を果たします。

A と B の Web サービスがあり、Web サービス B に条件付きアクセス ポリシーが適用されているとします。  最初の対話型の認証要求では、両方のサービスの同意が必要ですが、条件付きアクセス ポリシーは必要ない場合もあります。  アプリが Web サービス B のトークンを要求すると、ポリシーが呼び出され、Web サービス A に対する後続の要求も成功します。

![複数のサービスにアクセスするアプリのフロー ダイアグラム](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

また、最初にアプリが Web サービス A のトークンを要求している場合、エンドユーザーは条件付きアクセス ポリシーを呼び出しません。  これにより、アプリケーション開発者は、エンド ユーザー エクスペリエンスを制御しながらも、条件付きアクセス ポリシーを常に強制的に呼び出す必要がなくなります。 アプリが後で Web サービス B のトークンを要求すると状況は少し複雑になります。この時点で、エンドユーザーは、条件付きアクセス ポリシーに準拠する必要があります。  アプリが `acquireToken` しようとすると、次のエラー (次の図参照) が発生する可能性があります。 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![新しいトークンを要求する複数のサービスにアクセスするアプリ](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

アプリが ADAL ライブラリを使用しており、トークンの取得に失敗した場合、常に対話形式で再試行されます。  この対話型の要求が発生すると、エンドユーザーには、条件付きアクセスに準拠する機会が与えられます。  これは、要求が `AcquireTokenSilentAsync` または `PromptBehavior.Never` でない限り該当し、この場合、アプリは対話型の ```AcquireToken``` 要求を実行し、エンドユーザーはポリシーに準拠する機会が与えられます。 

### <a name="scenario-single-page-app-spa-using-adaljs"></a>シナリオ: シングル ページ アプリケーション (SPA) ADAL.js を使用する

このシナリオでは、ADAL.js を使用して条件付きアクセスで保護されている Web API を呼び出すシングル ページ アプリケーション (SPA) について説明します。  これは、単純なアーキテクチャですが、条件付きアクセスを開発するときに考慮すべき点がいくつかあります。

ADAL.js では、`login()`、`acquireToken(...)`、`acquireTokenPopup(…)`、および `acquireTokenRedirect(…)` トークンを取得する関数があります。 

* `login()` は対話型サイン イン要求を通じて ID トークンを取得しますが、(条件付きアクセスで保護されている Web API を含む) サービスへのアクセス トークンは取得しません。  
* その後、アクセス トークンのサイレント取得に `acquireToken(…)` が使用されます。この場合、どのような状況でも UI は表示されません。  
* `acquireTokenPopup(…)` と `acquireTokenRedirect(…)` の両方を対話形式でリソースのトークンを要求するために使用され、この場合、常にサインイン UI が表示されます。

Web API を呼び出すためにアクセス トークンが必要な場合は、アプリは `acquireToken(…)` を試行します。  トークンのセッションが期限切れか、あるいは条件付きアクセス ポリシーに準拠する必要がある場合は、*acquireToken* 関数が失敗してアプリは `acquireTokenPopup()` または `acquireTokenRedirect()` を使用します。

![ADAL を使用するシングル ページ アプリケーションのフロー ダイアグラム](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

条件付きアクセスのシナリオで紹介した例を見てみましょう。  エンドユーザーがサイトに到着し、セッションは開始されていません。  `login()` を呼び出し、多要素認証なしで ID トークンを取得します。  ユーザーがボタンを押し、これにより、アプリは Web API からデータを要求する必要があります。  アプリは `acquireToken()` の呼び出しを試行しますが、ユーザーがまだ多要素認証を実行しておらず、条件付きアクセス ポリシーに準拠する必要があるため、失敗します。

Azure AD は、次の HTTP 応答を返信します。 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

アプリは `error=interaction_required` をキャッチする必要があります。  アプリは、同じソースの `acquireTokenPopup()` または `acquireTokenRedirect()` を使用できます。  ユーザーは多要素認証の実行を求められます。 ユーザーが多要素認証を完了すると、アプリに、要求されたリソースの新しいアクセス トークンが発行されます。

このシナリオを試すには、[JS SPA On-Behalf- コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)を参照してください。  このコード サンプルでは、条件付きアクセス ポリシーと、このシナリオを説明するために、上記で JS SPA に登録された Web API が使用されます。 クレーム チャレンジを正しく処理し、Web API で使用できるアクセス トークンを取得する方法を示します。 または、Angular SPA については、一般的な [Angular.js コード サンプル](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)を参照してください。


## <a name="see-also"></a>関連項目

* 機能の詳細については、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access.md)」を参照してください。
* Azure AD コード サンプルについては、[Github リポジトリのコード サンプル](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)を参照してください。 
* ADAL SDK の詳細情報およびリファレンス ドキュメントにアクセスするには、[ライブラリ ガイド](active-directory-authentication-libraries.md)を参照してください。
* マルチテナント シナリオの詳細については、[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](active-directory-devhowto-multi-tenant-overview.md)を参照してください。

