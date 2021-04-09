---
title: Azure Active Directory の条件付きアクセスについての開発者ガイド
titleSuffix: Microsoft identity platform
description: Azure AD 条件付きアクセスと Microsoft ID プラットフォームの開発者ガイドとシナリオ。
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: e4021f0ca2c1c9ca9434744a4aebb4b7938315f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584232"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory の条件付きアクセスについての開発者ガイド

Azure Active Directory (Azure AD) の条件付きアクセス機能では、アプリをセキュリティで保護し、サービスを保護するために使用できる方法の 1 つを提供します。 条件付きアクセスを使用することで、開発者やエンタープライズのお客様は、次のようなさまざまな方法でサービスを保護できます。

* [多要素認証](../authentication/concept-mfa-howitworks.md)
* Intune 登録されているデバイスのみに特定のサービスへのアクセスを許可します。
* ユーザーの場所と IP 範囲を制限します。

条件付きアクセスのすべての機能の詳細については、記事「[条件付きアクセスとは](../conditional-access/overview.md)」を参照してください。

Azure AD のアプリをビルドしている開発者のために、この記事では、条件付きアクセスを使用する方法について示し、条件付きアクセス ポリシーが適用される可能性のある制御不能なリソースにアクセスした場合の影響についても学習します。 この記事では、On-Behalf-Of フロー、Web アプリ、Microsoft Graph へのアクセス、API の呼び出しに対し条件付きアクセスが与える影響についても説明します。

[シングル](quickstart-register-app.md)および[マルチテナント](howto-convert-app-to-be-multi-tenant.md) アプリおよび[一般的な認証パターン](./authentication-vs-authorization.md)の知識を持っているユーザーを対象とします。

> [!NOTE]
> この機能を使用するには、Azure AD Premium P1 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。
> [Microsoft 365 Business ライセンス](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。

## <a name="how-does-conditional-access-impact-an-app"></a>条件付きアクセスはどのようにアプリに影響を与えますか?

### <a name="app-types-impacted"></a>アプリの種類が影響を受ける

最も一般的なケースは、条件付きアクセスによってアプリの動作が変更されない、または開発者からの変更を必要としない場合です。 アプリが間接的、またはサイレントでサービスに対するトークンを要求する特定の場合のみ、アプリで条件付きアクセス チャレンジを処理するためにコードを変更する必要があります。  これは、対話型のサインインを要求するだけで実行できる場合があります。

具体的には、次のシナリオでは、条件付きアクセス チャレンジを処理するために、コードが必要になります。

* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* MSAL.js を使用するシングルページ アプリ
* リソースを呼び出す Web Apps

条件付きアクセス ポリシーは、アプリに適用できますが、アプリがアクセスする Web API にも適用できます。 条件付きアクセス ポリシーを構成する方法の詳細については、「[クイック スタート: Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](../authentication/tutorial-enable-azure-mfa.md)」を参照してください。

シナリオによっては、エンタープライズのお客様は、条件付きアクセス ポリシーをいつでも適用および削除することができます。 新しいポリシーが適用されたときにアプリが引き続き機能するように、チャレンジ処理を実装します。 チャレンジ処理の例は次のとおりです。

### <a name="conditional-access-examples"></a>条件付きアクセスの例

シナリオによっては、条件付きアクセスを処理するためにコードの変更が必要なものと、不要なものがあります。 その違いに分析情報を提供する、条件付きアクセスを使用して多要素認証を行うシナリオを少し次に示します。

* シングル テナントの iOS アプリをビルドして、条件付きアクセス ポリシーを適用するとします。 アプリがユーザーのサインインを処理し、API へのアクセスは要求されません。 ユーザーがサインインすると、ポリシーが自動的に呼び出され、ユーザーは、多要素認証 (MFA) を実行する必要があります。
* 中間層サービスを使用して、ダウンストリーム API にアクセスするネイティブ アプリを作成するとします。 このアプリを使用する会社のお客様は、ダウンストリーム API にポリシーを適用します。 エンドユーザーがサインインすると、ネイティブ アプリケーションは中間層へのアクセスを要求し、トークンを送信します。 中間層では On-Behalf-Of フローが実行され、ダウンストリーム API へのアクセスが要求されます。 この時点では、"チャレンジ" 要求は、中間層に提示されます。 中間層では、条件付きアクセス ポリシーに準拠する必要があるネイティブのアプリにチャレンジを送信します。

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph では、条件付きアクセスの環境でアプリを構築する場合に、特別な考慮事項があります。 通常、条件付きアクセスのメカニズムは同様に動作しますが、ユーザーに表示されるポリシーは、アプリがグラフから要求している基本データに基づくものとなります。

具体的に言うと、Microsoft Graph のスコープはすべて、ポリシーを個別に適用できる、いくつかのデータセットを表します。 条件付きアクセスのポリシーには特定のデータセットが割り当てられるため、Azure AD では、Graph 自体ではなく、Graph の背後にあるデータに基づいた条件付きアクセス ポリシーが適用されます。

たとえば、アプリが次の Microsoft Graph スコープを要求したとします。

```
scopes="Bookings.Read.All Mail.Read"
```

この場合アプリでは、ユーザーが Bookings と Exchange で設定されたすべてのポリシーを満たすことを想定できます。 一部のスコープは、複数のデータセットにマップされる場合があります (アクセスを許可する場合)。

### <a name="complying-with-a-conditional-access-policy"></a>条件付きアクセス ポリシーへの準拠

いくつかの異なるアプリ トポロジでは、セッションが確立されたときに、条件付きアクセス ポリシーが評価されます。 条件付きアクセス ポリシーは、アプリやサービスの粒度に従って動作するため、実行しようとしているシナリオによって呼び出されるポイントが大きく異なります。

アプリが条件付きアクセス ポリシーを使用してサービスにアクセスしようとすると、条件付きアクセスのチャレンジが発生する可能性があります。 このチャレンジは、Azure AD からの応答に含まれる `claims` パラメーターにエンコードされています。 このチャレンジ パラメーターの例を次に示します。

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

開発者は、このチャレンジを取得して、Azure AD への新しい要求に追加できます。 この状態を渡すと、エンド ユーザーに、条件付きアクセス ポリシーに準拠するために必要な操作を実行することを求めるメッセージが表示されます。 次のシナリオでは、エラーおよびパラメーターを抽出する方法について説明します。

## <a name="scenarios"></a>シナリオ

### <a name="prerequisites"></a>前提条件

Azure AD の条件付きアクセスは、[Azure AD Premium](../fundamentals/active-directory-whatis.md) に含まれている機能です。 [Microsoft 365 Business ライセンス](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。

### <a name="considerations-for-specific-scenarios"></a>特定のシナリオの考慮事項

次の情報は、これらの条件付きアクセス シナリオでのみ適用されます。

* On-Behalf-Of フローを実行するアプリ
* 複数のサービスとリソースにアクセスするアプリ
* MSAL.js を使用するシングルページ アプリ

以下のセクションでは、より複雑な一般的なシナリオについて説明します。 主要な運用原則では、条件付きアクセス ポリシーは、条件付きアクセス ポリシーが適用されるサービスに対してトークンが要求されたときに評価されます。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>シナリオ:On-Behalf-Of フローを実行するアプリ

このシナリオでは、ネイティブ アプリが Web サービス/API を呼び出す場合について説明します。 呼び出されたサービスは、On-Behalf-Of フローでダウンストリーム サービスを呼び出します。 ここでは、ダウンストリーム サービス (Web API 2) に、条件付きアクセス ポリシーを適用し、サーバー/デーモン アプリではなく、ネイティブ アプリを使用しています。

![On-Behalf-Of フローを実行するアプリのフロー ダイアグラム](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 の最初のトークン要求では、Web API 1 がダウンストリーム API にアクセスしない場合もあるため、エンドユーザーに多要素認証は求められません。 Web API 1 で Web API 2 のユーザーの On-Behalf-Of トークンが要求されると、ユーザーは多要素認証されていないために、要求が失敗します。

Azure AD は、いくつかの興味深いデータを HTTP 応答で返します。

> [!NOTE]
> この場合は、多要素認証エラーの説明ですが、条件付きアクセスに関連するさまざまな `interaction_required` である可能性があります。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Web API 1 では、エラー `error=interaction_required` をキャッチし、`claims` チャレンジをデスクトップ アプリケーションに返送します。 この時点では、デスクトップ アプリケーションは新しい `acquireToken()` 呼び出しを行い、追加のクエリ文字列パラメーターとして `claims` チャレンジを追加できます。 この新しい要求では、ユーザーは多要素認証を実行し、この新しいトークンを Web API 1 に送信し、On-Behalf-Of フローを完了する必要があります。

このシナリオを試すには、[.NET コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-)を参照してください。 これは、Web API 1 から返された要求チャレンジをネイティブ アプリケーションに渡し、クライアント アプリケーション内で新しい要求を作成する方法を示しています。

## <a name="scenario-app-accessing-multiple-services"></a>シナリオ:複数のサービスにアクセスするアプリ

このシナリオでは、Web アプリが、いずれかに条件付きアクセス ポリシーが割り当てられている 2 つのサービスにアクセスする場合について説明します。 アプリケーション ロジックによっては、Web アプリが両方の Web サービスにアクセスする必要のないパスが存在する場合があります。 このシナリオでは、トークンを要求する順序が、エンド ユーザー エクスペリエンスに重要な役割を果たします。

A と B の Web サービスがあり、Web サービス B に条件付きアクセス ポリシーが適用されているとします。 最初の対話型の認証要求では、両方のサービスの同意が必要ですが、条件付きアクセス ポリシーがすべての場合に必要なわけではありません。 アプリが Web サービス B のトークンを要求すると、ポリシーが呼び出され、Web サービス A に対する後続の要求も成功します。

![複数のサービスにアクセスするアプリのフロー ダイアグラム](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

また、最初にアプリで Web サービス A に対するトークンを要求している場合、エンド ユーザーは条件付きアクセス ポリシーを呼び出しません。 これにより、アプリ開発者は、エンドユーザー エクスペリエンスを制御しながらも、条件付きアクセス ポリシーを常に強制的に呼び出す必要がなくなります。 アプリが後で Web サービス B のトークンを要求すると状況は少し複雑になります。この時点で、エンド ユーザーは、条件付きアクセス ポリシーに準拠する必要があります。 アプリが `acquireToken` しようとすると、次のエラー (次の図参照) が発生する可能性があります。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![新しいトークンを要求する複数のサービスにアクセスするアプリ](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

アプリが MSAL ライブラリを使用しており、トークンの取得に失敗した場合、常に対話形式で再試行されます。 この対話型の要求が発生すると、エンド ユーザーには、条件付きアクセスに準拠する機会が与えられます。 これは、要求が `AcquireTokenSilentAsync` または `PromptBehavior.Never` でない限り該当し、この場合、アプリは対話型の ```AcquireToken``` 要求を実行し、エンドユーザーはポリシーに準拠する機会が与えられます。

## <a name="scenario-single-page-app-spa-using-msaljs"></a>シナリオ:MSAL.js を使用するシングルページ アプリ (SPA)

このシナリオでは、条件付きアクセスで保護されている Web API を呼び出すための MSAL.js がシングルページ アプリ (SPA) において使用される場合について説明します。 これは、シンプルなアーキテクチャですが、条件付きアクセスの周辺を開発するときに考慮する必要がある点がいくつかあります。

MSAL.js では、`loginPopup()`、`acquireTokenSilent(...)`、`acquireTokenPopup(…)`、および `acquireTokenRedirect(…)` トークンを取得する関数があります。

* `loginPopup()` では対話型サインイン要求を通じて ID トークンを取得しますが、(条件付きアクセスで保護されている Web API を含む) サービスへのアクセス トークンは取得しません。
* その後、アクセス トークンのサイレント取得に `acquireTokenSilent(…)` が使用されます。この場合、どのような状況でも UI は表示されません。
* `acquireTokenPopup(…)` と `acquireTokenRedirect(…)` の両方を対話形式でリソースのトークンを要求するために使用され、この場合、常にサインイン UI が表示されます。

Web API を呼び出すためにアクセス トークンが必要な場合は、アプリは `acquireTokenSilent(…)` を試行します。 トークンのセッションが期限切れか、あるいは条件付きアクセス ポリシーに準拠する必要がある場合は、*acquireToken* 関数が失敗して、アプリでは `acquireTokenPopup()` または `acquireTokenRedirect()` が使用されます。

![MSAL を使用するシングルページ アプリケーションのフロー ダイアグラム](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

条件付きアクセスのシナリオでの例を見てみましょう。 エンドユーザーがサイトに到着し、セッションは開始されていません。 `loginPopup()` を呼び出し、多要素認証なしで ID トークンを取得します。 ユーザーがボタンを押し、これにより、アプリは Web API からデータを要求する必要があります。 アプリは `acquireTokenSilent()` の呼び出しを試行しますが、ユーザーがまだ多要素認証を実行しておらず、条件付きアクセス ポリシーに準拠する必要があるため、失敗します。

Azure AD は、次の HTTP 応答を返信します。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

アプリは `error=interaction_required` をキャッチする必要があります。 アプリは、同じソースの `acquireTokenPopup()` または `acquireTokenRedirect()` を使用できます。 ユーザーは多要素認証の実行を求められます。 ユーザーが多要素認証を完了すると、アプリに、要求されたリソースの新しいアクセス トークンが発行されます。

このシナリオを試すには、[JS SPA On-Behalf- コード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md)を参照してください。 このコード サンプルでは、条件付きアクセス ポリシーと、このシナリオを説明するために、上記で JS SPA に登録された Web API が使用されます。 クレーム チャレンジを正しく処理し、Web API で使用できるアクセス トークンを取得する方法を示します。 または、Angular SPA については、一般的な [Angular.js コード サンプル](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2)を参照してください。

## <a name="see-also"></a>関連項目

* 機能について詳しくは、「[Azure Active Directory の条件付きアクセス](../conditional-access/overview.md)」をご覧ください。
* その他の Azure AD コード サンプルについては、[サンプル](sample-v2-code.md)のページを参照してください。
* MSAL SDK の詳細とリファレンス ドキュメントへのアクセスについては、[Microsoft Authentication Library の概要](msal-overview.md)に関するページを参照してください。
* マルチテナント シナリオの詳細については、[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)を参照してください。
* [「条件付きアクセス」と「IoT アプリへのアクセスのセキュリティ保護」](/azure/architecture/example-scenario/iot-aad/iot-aad)に関する詳細を学習します。