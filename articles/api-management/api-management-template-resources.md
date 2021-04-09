---
title: Azure API Management テンプレート リソース | Microsoft Docs
description: Azure API Management の開発者ポータル テンプレートで使用できるリソースの種類について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84689989"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management テンプレート リソース
Azure API Management には、開発者ポータル テンプレート用に次の種類のリソースが用意されています。  
  
-   [文字列リソース](#strings)  
  
-   [グリフ リソース](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> 文字列リソース  
 API Management には、開発者ポータルで使用するための包括的な文字列リソースのセットが用意されています。 これらのリソースは、API Management でサポートされているすべての言語にローカライズされます。 既定のテンプレート セットでは、開発者ポータルに表示されるページのヘッダー、ラベル、定数文字列にこれらのリソースを使用します。 テンプレートで文字列リソースを使用するには、次の例に示すように、リソース文字列のプレフィックスに続いて文字列名を指定します。  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 次の例は、製品リスト テンプレートの一部です。ページの先頭に "**製品**" と表示します。  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
次のローカライズ オプションがサポートされています。

| Locale    | Language               |
|-----------|------------------------|
| "en"      | "English"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "it"      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 開発者ポータル テンプレートで使用できる文字列リソースについては、次の表を参照してください。 表の名前は、その表内の文字列リソースのプレフィックスとして使用します。  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [ドキュメント](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SigninStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|名前|テキスト|  
|----------|----------|  
|PageTitleApis|API|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebApplicationsDetailsTitle|Application preview (アプリケーションのプレビュー)|  
|WebApplicationsRequirementsHeader|要件|  
|WebApplicationsScreenshotAlt|Screenshot|  
|WebApplicationsScreenshotsHeader|Screenshots (スクリーンショット)|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Are you sure that you want to remove application? (アプリケーションを削除しますか?)|  
|WebDevelopersAppNotPublished|Not published (未発行)|  
|WebDevelopersAppNotSubmitted|Not submitted (未申請)|  
|WebDevelopersAppTableCategoryHeader|カテゴリ|  
|WebDevelopersAppTableNameHeader|名前|  
|WebDevelopersAppTableStateHeader|State|  
|WebDevelopersEditLink|[編集]|  
|WebDevelopersRegisterAppLink|アプリケーションを登録する|  
|WebDevelopersRemoveLink|削除|  
|WebDevelopersSubmitLink|送信|  
|WebDevelopersYourApplicationsHeader|ご利用のアプリケーションへの応用|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebApplicationsHeader|アプリケーション|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|名前|テキスト|  
|----------|----------|  
|NoItemsToDisplay|該当するものが見つかりません。|  
|GeneralExceptionMessage|Something is not right. (問題があります。) It could be a temporary glitch or a bug. (一時的な障害またはバグである可能性があります。) Please, try again. (もう一度実行してください。)|  
|GeneralJsonExceptionMessage|Something is not right. (問題があります。) It could be a temporary glitch or a bug. (一時的な障害またはバグである可能性があります。) Please, reload the page and try again. (ページを再読み込みして、もう一度やり直してください。)|  
|ConfirmationMessageUnsavedChanges|There are some unsaved changes. (保存されていない変更がいくつかあります。) Are you sure you want to cancel and discard the changes? (変更を取り消して破棄しますか?)|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body too large. (HTTP 要求の本文が大きすぎます。)|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|名前|テキスト|  
|----------|----------|  
|ButtonLabelCancel|キャンセル|  
|ButtonLabelSave|保存|  
|GeneralExceptionMessage|Something is not right. (問題があります。) It could be a temporary glitch or a bug. (一時的な障害またはバグである可能性があります。) Please, try again. (もう一度実行してください。)|  
|NoItemsToDisplay|There are no items to display. (表示する項目がありません。)|  
|PagerButtonLabelFirst|First|  
|PagerButtonLabelLast|末尾|  
|PagerButtonLabelNext| ボタンをクリックします。|  
|PagerButtonLabelPrevious|Prev (前へ)|  
|PagerLabelPageNOfM|{0}/{1} ページ|  
|PasswordTooShort|The Password is too short (パスワードが短すぎます)|  
|EmailAsPassword|Do not use your email as your password (メール アドレスをパスワードとして使用しないでください)|  
|PasswordSameAsUserName|Your password cannot contain your username (パスワードにユーザー名を含めることはできません)|  
|PasswordTwoCharacterClasses|Use different character classes (別の文字クラスを使用してください)|  
|PasswordTooManyRepetitions|Too many repetitions (繰り返しが多すぎます)|  
|PasswordSequenceFound|Your password contains sequences (パスワードにシーケンスが含まれています)|  
|PagerLabelPageSize|Page size (ページ サイズ)|  
|CurtainLabelLoading|読み込んでいます...|  
|TablePlaceholderNothingToDisplay|There is no data for the selected period and scope (選択した期間とスコープのデータがありません)|  
|ButtonLabelClose|閉じる|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Documentation  
  
|名前|テキスト|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Invalid header '{0}' (ヘッダー '{0}' が無効です)|  
|WebDocumentationInvalidRequestErrorMessage|Invalid Request URL (要求 URL が無効です)|  
|TextboxLabelAccessToken|Access token * (アクセス トークン)|  
|DropdownOptionPrimaryKeyFormat|Primary-{0} (プライマリ -{0})|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0} (セカンダリ -{0})|  
|WebDocumentationSubscriptionKeyText|Your subscription key (サブスクリプション キー)|  
|WebDocumentationTemplatesAddHeaders|Add required HTTP headers (必要な HTTP ヘッダーを追加する)|  
|WebDocumentationTemplatesBasicAuthSample|Basic Authorization Sample (基本認証のサンプル)|  
|WebDocumentationTemplatesCurlForBasicAuth|for Basic Authorization use: --user {username}:{password} (基本認証の使用について: --ユーザー {username}:{password})|  
|WebDocumentationTemplatesCurlValuesForPath|Specify values for path parameters (shown as {...}), your subscription key and values for query parameters (パス パラメーターの値 ({...} として表示)、サブスクリプション キー、およびクエリ パラメーターの値を指定します)|  
|WebDocumentationTemplatesDeveloperKey|Specify your subscription key (サブスクリプション キーを指定してください)|  
|WebDocumentationTemplatesJavaApache|This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/) (このサンプルでは、HTTP Components の Apache HTTP クライアントを使用しています)|  
|WebDocumentationTemplatesOptionalParams|Specify values for optional parameters, as needed (必要に応じて省略可能なパラメーターの値を指定します)|  
|WebDocumentationTemplatesPhpPackage|This sample uses the HTTP_Request2 package. (このサンプルでは、HTTP_Request2 パッケージを使用します。) (詳細: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Specify values for path parameters (shown as {...}) and request body if needed (必要に応じて、パス パラメーターの値 ({...} として表示) と要求本文を指定します)|  
|WebDocumentationTemplatesRequestBody|Specify request body (要求本文を指定します)|  
|WebDocumentationTemplatesRequiredParams|Specify values for the following required parameters (次の必要なパラメーターの値を指定します)|  
|WebDocumentationTemplatesValuesForPath|Specify values for path parameters (shown as {...}) (パス パラメーターの値 ({...} として表示) を指定します)|  
|OAuth2AuthorizationEndpointDescription|The authorization endpoint is used to interact with the resource owner and obtain an authorization grant. (承認エンドポイントは、リソース所有者との対話や承認付与の取得に使用されます。)|  
|OAuth2AuthorizationEndpointName|Authorization endpoint (承認エンドポイント)|  
|OAuth2TokenEndpointDescription|The token endpoint is used by the client to obtain an access token by presenting its authorization grant or refresh token. (トークン エンドポイントは、クライアントが承認付与または更新トークンを提示してアクセス トークンを取得する際に使用されます。)|  
|OAuth2TokenEndpointName|Token endpoint (トークン エンドポイント)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\>         The client initiates the flow by directing the resource owner's         user-agent to the authorization endpoint. (クライアントは、リソース所有者のユーザー エージェントを承認エンドポイントに導くことで、フローを開始します。)  The client includes         its client identifier, requested scope, local state, and a         redirection URI to which the authorization server will send the         user-agent back once access is granted (or denied). (クライアントには、クライアント識別子、要求されたスコープ、ローカルの状態に加え、アクセスが許可 (または拒否) されると承認サーバーによってユーザー エージェントが再度送信されるリダイレクト URI が含まれます。)     </p\>     <p\>         The authorization server authenticates the resource owner (via         the user-agent) and establishes whether the resource owner         grants or denies the client's access request. (承認サーバーによってリソースの所有者が認証され (ユーザー エージェントを使用)、リソースの所有者がクライアントのアクセス要求を許可するか、否定するかが設定されます。)     </p\>     <p\>         Assuming the resource owner grants access, the authorization         server redirects the user-agent back to the client using the         redirection URI provided earlier (in the request or during         client registration). (リソース所有者がアクセスを許可した場合、承認サーバーは以前 (要求時、またはクライアント登録中) に提供されたリダイレクト URI を使用してユーザー エージェントをクライアントにリダイレクトします。)  The redirection URI includes an         authorization code and any local state provided by the client         earlier. (リダイレクト URI には認証コードと、以前にクライアントから提供されたローカル状態が含まれています。)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\>If the user denies the access request of if the request is invalid, the client will be informed using the following parameters added on to the redirect: </p\> (ユーザーがアクセス要求を拒否した場合または要求が無効な場合、クライアントには、リダイレクトに追加された次のパラメーターを使用して通知されます:)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Authorization request (承認要求)|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>         The client app must send the user to the authorization endpoint in order to initiate the OAuth process. (クライアント アプリは、OAuth プロセスを開始するために、ユーザーを承認エンドポイントに送信する必要があります。)          At the authorization endpoint, the user authenticates and then grants or denies access to the app. (承認エンドポイントでは、ユーザーが認証された後、アプリへのアクセスが許可または拒否されます。)     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     Assuming the resource owner grants access, authorization server     redirects the user-agent back to the client using the     redirection URI provided earlier (in the request or during     client registration). (リソース所有者がアクセスを許可した場合、承認サーバーは以前 (要求時、またはクライアント登録中) に提供されたリダイレクト URI を使用して、ユーザー エージェントをクライアントにリダイレクトします。)  The redirection URI includes an     authorization code and any local state provided by the client     earlier. (リダイレクト URI には認証コードと、以前にクライアントから提供されたローカル状態が含まれています。) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\>  The client requests an access token from the authorization     server''s token endpoint by including the authorization code     received in the previous step. (クライアントは、前の手順で受け取った承認コードを含めることで、承認サーバーのトークン エンドポイントにアクセス トークンを要求します。)  When making the request, the     client authenticates with the authorization server. (クライアントは、要求を行う際に、承認サーバーで認証を行います。)  The client     includes the redirection URI used to obtain the authorization     code for verification. (クライアントには、検証用の承認コードを取得するためのリダイレクト URI が含まれています。) </p\> <p\>     The authorization server authenticates the client, validates the     authorization code, and ensures that the redirection URI     received matches the URI used to redirect the client in     step (C). (承認サーバーはクライアントを認証し、承認コードを確認して、受け取ったリダイレクト URI が、手順 (C) でクライアントのリダイレクトに使用された URI と一致するかどうかを確認します。)  If valid, the authorization server responds back with     an access token and, optionally, a refresh token. (有効な場合、承認サーバーはアクセス トークン、およびオプションで更新トークンを使用して応答を返します。) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\>If the request client authentication failed or is invalid, the authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters with the response. (要求のクライアント認証が失敗した場合または無効な場合、承認サーバーは、(別途指定がない限り) HTTP 400 (無効な要求) 状態コードで応答を返し、応答に次のパラメーターを含めます。) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\>The client makes a request to the token endpoint by sending the following parameters using the "application/x-www-form-urlencoded" format with a character encoding of UTF-8 in the HTTP request entity-body. (クライアントは、HTTP 要求のエンティティ ボディで文字エンコードが UTF-8 に指定された "application/x-www-form-urlencoded" 形式を使用して次のパラメーターを送信することで、トークン エンドポイントに対して要求を行います。) </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\>The authorization server issues an access token and optional refresh token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code.(承認サーバーは、アクセス トークンとオプションの更新トークンを発行し、200 (OK) 状態コードを含む HTTP 応答のエンティティ ボディに次のパラメーターを追加することで応答を作成します。) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\>  The client authenticates with the authorization server and     requests an access token from the token endpoint. (クライアントは、承認サーバーで認証を行い、トークン エンドポイントからアクセス トークンを要求します。) </p\> <p\>  The authorization server authenticates the client, and if valid,     issues an access token. (承認サーバーはクライアントを認証し、有効な場合はアクセス トークンを発行します。) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>     If the request failed client authentication or is invalid   the authorization server responds with an HTTP 400 (Bad Request)     status code (unless specified otherwise) and includes the following     parameters with the response. (要求がクライアント認証に失敗した場合または要求が無効な場合は、承認サーバーは、HTTP 400 (Bad Request) 状態コード (別途指定がある場合を除く) を含む応答を返し、応答には次のパラメーターを含めます。) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>   The client makes a request to the token endpoint by adding the     following parameters using the "application/x-www-form-urlencoded"     format with a character encoding of UTF-8 in the HTTP     request entity-body. (クライアントは、HTTP 要求のエンティティ ボディの文字エンコードが UTF-8 の "application/x-www-form-urlencoded" 形式を使用して次のパラメーターを追加することで、トークン エンドポイントに対して要求を行います。) </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>  If the access token request is valid and authorized, the     authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters     to the entity-body of the HTTP response with a 200 (OK) status code. (アクセス トークン要求が有効で、承認されると、承認サーバーはアクセス トークンとオプションの更新トークンを発行し、200 (OK) 状態コードを含む HTTP 応答のエンティティ ボディに次のパラメーターを追加することで応答を作成します。) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\>   The client initiates the flow by directing the resource owner''s     user-agent to the authorization endpoint. (クライアントは、リソース所有者のユーザー エージェントを承認エンドポイントに導くことで、フローを開始します。)  The client includes     its client identifier, requested scope, local state, and a     redirection URI to which the authorization server will send the     user-agent back once access is granted (or denied). (クライアントには、クライアント識別子、要求されたスコープ、ローカルの状態に加え、アクセスが許可 (または拒否) されると承認サーバーによってユーザー エージェントが再度送信されるリダイレクト URI が含まれます。) </p\> <p\>        The authorization server authenticates the resource owner (via     the user-agent) and establishes whether the resource owner     grants or denies the client''s access request. (承認サーバーによってリソースの所有者が認証され (ユーザー エージェントを使用)、リソースの所有者がクライアントのアクセス要求を許可するか、否定するかが設定されます。) </p\> <p\>        Assuming the resource owner grants access, the authorization   server redirects the user-agent back to the client using the   redirection URI provided earlier. (リソース所有者がアクセスを許可したと想定した場合、承認サーバーは、前に指定されたリダイレクト URI を使用してユーザー エージェントをクライアントにリダイレクトします。)  The redirection URI includes     the access token in the URI fragment. (リダイレクト URI は、URI フラグメントにアクセス トークンを含めます。) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\>     If the resource owner denies the access request or if the request     fails for reasons other than a missing or invalid redirection URI,     the authorization server informs the client by adding the following     parameters to the fragment component of the redirection URI using the     "application/x-www-form-urlencoded" format. (リソース所有者がアクセス要求を拒否した場合、またはリダイレクト URI が存在しないか無効であるという理由以外で要求が失敗した場合は、承認サーバーは、"application/x-www-form-urlencoded" 形式でリダイレクト URI のフラグメント コンポーネントに次のパラメーターを追加することで、クライアントに通知します。) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>     The client app must send the user to the authorization endpoint in order to initiate the OAuth process. (クライアント アプリは、OAuth プロセスを開始するために、ユーザーを承認エンドポイントに送信する必要があります。)      At the authorization endpoint, the user authenticates and then grants or denies access to the app. (承認エンドポイントでは、ユーザーが認証された後、アプリへのアクセスが許可または拒否されます。) </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     If the resource owner grants the access request, the authorization     server issues an access token and delivers it to the client by adding     the following parameters to the fragment component of the redirection     URI using the "application/x-www-form-urlencoded" format. (リソース所有者がアクセス要求を許可した場合、承認サーバーは、"application/x-www-form-urlencoded" 形式でリダイレクト URI のフラグメント コンポーネントに次のパラメーターを追加することで、アクセス トークンを発行し、それをクライアントに配布します。) </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Authorization code flow is optimized for clients capable of maintaining the confidentiality of their credentials (e.g., web server applications implemented using  PHP, Java, Python, Ruby, ASP.NET, etc.). (承認コードフローは、資格情報の機密性を保持できるクライアントに最適です (例: PHP、Java、Python、Ruby、ASP.NET などを使用して実装された Web サーバー アプリケーション)。)|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Authorization Code grant (承認コードの付与)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Client credentials flow is suitable in cases where the client (your application) is requesting access to the protected resources under its control. (クライアントの資格情報フローは、クライアント (アプリケーション) がその管理下で保護されているリソースへのアクセスを要求している場合に適しています。) The client is considered as a resource owner, so no end-user interaction is required. (クライアントはリソース所有者と見なされるため、エンドユーザーによる操作が必要ありません。)|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Client Credentials grant (クライアント資格情報の付与)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit flow is optimized for clients incapable of maintaining the confidentiality of their credentials known to operate a particular redirection URI. (暗黙的なフローは、特定のリダイレクト URI を操作することがわかっている資格情報の機密性を保持できないクライアントに適しています。) These clients are typically implemented in a browser using a scripting language such as JavaScript. (これらのクライアントは、通常、JavaScript などのスクリプト言語を使用してブラウザーで実装されます。)|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit grant (暗黙的な付与)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Resource owner password credentials flow is suitable in cases where the resource owner has a trust relationship with the client (your application), such as the device operating system or a highly privileged application. (リソース所有者のパスワード資格情報のフローは、リソース所有者が、デバイスのオペレーティング システムや高い特権を持つアプリケーションなどのクライアント (アプリケーション) と信頼関係にある場合に適しています。) This flow is suitable for clients capable of obtaining the resource owner's credentials (username and password, typically using an interactive form). (このフローは、リソース所有者の資格情報 (ユーザー名とパスワード、通常は対話形式を使用) を取得できるクライアントに適しています。)|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Resource Owner Password Credentials grant (リソース所有者のパスワード資格情報の付与)|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\>   The resource owner provides the client with its username and     password. (リソース所有者は、そのユーザー名とパスワードをクライアントに提供します。) </p\> <p\> The client requests an access token from the authorization     server''s token endpoint by including the credentials received     from the resource owner. (クライアントは、リソース所有者から受け取った資格情報を含めることで、承認サーバーのトークン エンドポイントにアクセス トークンを要求します。)  When making the request, the client     authenticates with the authorization server. (クライアントは、要求を行う際に、承認サーバーで認証を行います。) </p\> <p\>     The authorization server authenticates the client and validates     the resource owner credentials, and if valid, issues an access     token. (承認サーバーはクライアントを認証し、リソース所有者の資格情報を検証して、有効な場合はアクセス トークンを発行します。) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>If the request failed client authentication or is invalid the authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters with the response. (要求によってクライアント認証が失敗した場合または要求が無効な場合、承認サーバーは、(別途指定がない限り) HTTP 400 (無効な要求) 状態コードで応答を返し、応答に次のパラメーターを含めます。) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>The client makes a request to the token endpoint by adding the following parameters using the "application/x-www-form-urlencoded" format with a character encoding of UTF-8 in the HTTP request entity-body. (クライアントは、HTTP 要求のエンティティ ボディで文字エンコードが UTF-8 に指定された "application/x-www-form-urlencoded" 形式を使用して次のパラメーターを追加することで、トークン エンドポイントに対して要求を行います。) </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>   If the access token request is valid and authorized, the     authorization server issues an access token and optional refresh     token, and constructs the response by adding the following parameters     to the entity-body of the HTTP response with a 200 (OK) status code. (アクセス トークン要求が有効で承認されると、承認サーバーは、アクセス トークンとオプションの更新トークンを発行し、200 (OK) 状態コードを含む HTTP 応答のエンティティ ボディに次のパラメーターを追加することで応答を作成します。) </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Access token request (アクセス トークン要求)|  
|OAuth2Step_AuthorizationRequest_Name|Authorization request (承認要求)|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|REQUIRED. (必須。) The access token issued by the authorization server. (承認サーバーによって発行されたアクセス トークン。)|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|REQUIRED. (必須。) The access token issued by the authorization server. (承認サーバーによって発行されたアクセス トークン。)|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|REQUIRED. (必須。) The access token issued by the authorization server. (承認サーバーによって発行されたアクセス トークン。)|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUIRED. (必須。) The access token issued by the authorization server. (承認サーバーによって発行されたアクセス トークン。)|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|REQUIRED. (必須。) Client identifier. (クライアント識別子。)|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|REQUIRED if the client is not authenticating with the authorization server. (クライアントが承認サーバーで認証されていない場合は必須。)|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|REQUIRED. (必須。) The client identifier. (クライアント識別子。)|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|REQUIRED. (必須。) The authorization code generated by the authorization server. (承認サーバーによって生成された承認コード。)|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|REQUIRED. (必須。) The authorization code received from the authorization server. (承認サーバーから受け取った承認コード。承認サーバーから受け取った承認コード。)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|省略可能。 Human-readable ASCII text providing additional information. (追加情報を提供する、人間が判読できる ASCII テキスト。)|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|省略可能。 Human-readable ASCII text providing additional information. (追加情報を提供する、人間が判読できる ASCII テキスト。)|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|省略可能。 Human-readable ASCII text providing additional information. (追加情報を提供する、人間が判読できる ASCII テキスト。)|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|省略可能。 Human-readable ASCII text providing additional information. (追加情報を提供する、人間が判読できる ASCII テキスト。)|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|省略可能。 Human-readable ASCII text providing additional information. (追加情報を提供する、人間が判読できる ASCII テキスト。)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|省略可能。 A URI identifying a human-readable web page with information about the error. (エラーに関する情報を含む、人間が判読できる Web ページを識別する URI。)|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|省略可能。 A URI identifying a human-readable web page with information about the error. (エラーに関する情報を含む、人間が判読できる Web ページを識別する URI。)|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|省略可能。 A URI identifying a human-readable web page with information about the error. (エラーに関する情報を含む、人間が判読できる Web ページを識別する URI。)|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|省略可能。 A URI identifying a human-readable web page with information about the error. (エラーに関する情報を含む、人間が判読できる Web ページを識別する URI。)|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|省略可能。 A URI identifying a human-readable web page with information about the error. (エラーに関する情報を含む、人間が判読できる Web ページを識別する URI。)|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUIRED. (必須。) A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (次のうち 1 つの ASCII エラー コード: invalid_request、unauthorized_client、access_denied、unsupported_response_type、invalid_scope、server_error、temporarily_unavailable。)|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|REQUIRED. (必須。) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (次のうち 1 つの ASCII エラー コード: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。)|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|REQUIRED. (必須。) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (次のうち 1 つの ASCII エラー コード: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。)|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|REQUIRED. (必須。) A single ASCII error code from the following: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable. (次のうち 1 つの ASCII エラー コード: invalid_request、unauthorized_client、access_denied、unsupported_response_type、invalid_scope、server_error、temporarily_unavailable。)|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|REQUIRED. (必須。) A single ASCII error code from the following: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope. (次のうち 1 つの ASCII エラー コード: invalid_request、invalid_client、invalid_grant、unauthorized_client、unsupported_grant_type、invalid_scope。)|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMMENDED. (推奨。) The lifetime in seconds of the access token. (アクセス トークンの有効期間 (秒)。)|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMMENDED. (推奨。) The lifetime in seconds of the access token. (アクセス トークンの有効期間 (秒)。)|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMMENDED. (推奨。) The lifetime in seconds of the access token. (アクセス トークンの有効期間 (秒)。)|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMMENDED. (推奨。) The lifetime in seconds of the access token. (アクセス トークンの有効期間 (秒)。)|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|REQUIRED. (必須。) Value MUST be set to "authorization_code". (値は "authorization_code" に設定する必要があります。)|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|REQUIRED. (必須。) Value MUST be set to "client_credentials". (値は "client_credentials" に設定する必要があります。)|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (必須。) Value MUST be set to "password". (値は "password" に設定する必要があります。)|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (必須。) The resource owner password. (リソース所有者のパスワード。)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|省略可能。 The redirection endpoint URI must be an absolute URI. (リダイレクトのエンドポイント URI は、絶対 URI にする必要があります。)|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|REQUIRED if the "redirect_uri" parameter was included in the authorization request, and their values MUST be identical. ("redirect_uri" パラメーターが承認要求に含まれていた場合は必須。これらの値は同一である必要があります。)|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|省略可能。 The redirection endpoint URI must be an absolute URI. (リダイレクトのエンドポイント URI は、絶対 URI にする必要があります。)|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|省略可能。 The refresh token, which can be used to obtain new access tokens. (新しいアクセス トークンを取得するために使用できる更新トークン。)|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|省略可能。 The refresh token, which can be used to obtain new access tokens. (新しいアクセス トークンを取得するために使用できる更新トークン。)|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|省略可能。 The refresh token, which can be used to obtain new access tokens. (新しいアクセス トークンを取得するために使用できる更新トークン。)|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|REQUIRED. (必須。) Value MUST be set to "code". (値は "code" に設定する必要があります。)|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|REQUIRED. (必須。) Value MUST be set to "token". (値は "token" に設定する必要があります。)|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|省略可能。 The scope of the access request. (アクセス要求のスコープ。)|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (クライアントによって要求されたスコープと同じ場合は省略可能、それ以外の場合は必須。)|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|省略可能。 The scope of the access request. (アクセス要求のスコープ。)|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. (クライアントによって要求されたスコープと同じ場合は省略可能、それ以外の場合は必須。)|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|省略可能。 The scope of the access request. (アクセス要求のスコープ。)|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPTIONAL if identical to the scope requested by the client; otherwise, REQUIRED. (クライアントによって要求されたスコープと同じ場合は省略可能、それ以外の場合は必須。)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|省略可能。 The scope of the access request. (アクセス要求のスコープ。)|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED. (クライアントによって要求されたスコープと同じ場合は省略可能、それ以外の場合は必須。)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. ("state" パラメーターがクライアント承認要求に存在する場合は必須。)  The exact value received from the client. (クライアントから受け取った正確な値。)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMMENDED. (推奨。) An opaque value used by the client to maintain state between the request and callback. (クライアントが要求とコールバックの間で状態を維持するために使用する非透過的な値。)  The authorization server includes this value when redirecting the user-agent back to the client. (承認サーバーは、ユーザー エージェントをクライアントにリダイレクトするときにこの値を含めます。)  The parameter SHOULD be used for preventing cross-site request forgery. (クロスサイト リクエスト フォージェリを防ぐために、このパラメーターを使用してください。)|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. ("state" パラメーターがクライアント承認要求に存在する場合は必須。)  The exact value received from the client. (クライアントから受け取った正確な値。)|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|REQUIRED if the "state" parameter was present in the client authorization request. ("state" パラメーターがクライアント承認要求に存在する場合は必須。)  The exact value received from the client. (クライアントから受け取った正確な値。)|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMMENDED. (推奨。) An opaque value used by the client to maintain state between the request and callback. (クライアントが要求とコールバックの間で状態を維持するために使用する非透過的な値。)  The authorization server includes this value when redirecting the user-agent back to the client. (承認サーバーは、ユーザー エージェントをクライアントにリダイレクトするときにこの値を含めます。)  The parameter SHOULD be used for preventing cross-site request forgery. (クロスサイト リクエスト フォージェリを防ぐために、このパラメーターを使用してください。)|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|REQUIRED if the "state" parameter was present in the client authorization request. ("state" パラメーターがクライアント承認要求に存在する場合は必須。)  The exact value received from the client. (クライアントから受け取った正確な値。)|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|REQUIRED. (必須。) The type of the token issued. (発行されたトークンの種類。)|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|REQUIRED. (必須。) The type of the token issued. (発行されたトークンの種類。)|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|REQUIRED. (必須。) The type of the token issued. (発行されたトークンの種類。)|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|REQUIRED. (必須。) The type of the token issued. (発行されたトークンの種類。)|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|REQUIRED. (必須。) The resource owner username. (リソース所有者のユーザー名。)|  
|OAuth2UnsupportedTokenType|Token type '{0}' is not supporetd. (トークンの種類 '{0}' はサポートされていません。)|  
|OAuth2InvalidState|Invalid response from authorization server (承認サーバーからの応答が無効です)|  
|OAuth2GrantType_AuthorizationCode|Authorization code (承認コード)|  
|OAuth2GrantType_Implicit|暗黙|  
|OAuth2GrantType_ClientCredentials|クライアント資格情報|  
|OAuth2GrantType_ResourceOwnerPassword|Resource owner password (リソース所有者のパスワード)|  
|WebDocumentation302Code|302 Found (302 検出)|  
|WebDocumentation400Code|400 (Bad request) (400 (無効な要求))|  
|OAuth2SendingMethod_AuthHeader|Authorization header (Authorization ヘッダー)|  
|OAuth2SendingMethod_QueryParam|Query parameter (クエリ パラメーター)|  
|OAuth2AuthorizationServerGeneralException|An error has occurred while authorizing access via {0} ({0} を介したアクセスの承認中にエラーが発生しました)|  
|OAuth2AuthorizationServerCommunicationException|An HTTP connection to authorization server could not be established or it has been unexpectedly closed. (承認サーバーへの HTTP 接続は確立できなかったか、予期せず切断されました。)|  
|WebDocumentationOAuth2GeneralErrorMessage|Unexpected error occurred. (予期しないエラーが発生しました。)|  
|AuthorizationServerCommunicationException|Authorization server communication exception has happened. (承認サーバーの通信の例外が発生しました。) Please contact administrator. (管理者に問い合わせてください。)|  
|TextblockSubscriptionKeyHeaderDescription|Subscription key which provides access to this API. (この API へのアクセスを提供するサブスクリプション キー。) Found in your <a href='/developer'\>Profile</a\>. (<a href='/developer'\>Profile</a\> にあります。)|  
|TextblockOAuthHeaderDescription|OAuth 2.0 access token obtained from <i\>{0}</i\>. (OAuth 2.0 アクセス トークンを <i>{0}</i> から取得しました。) Supported grant types: <i\>{1}</i\>. (サポートされている付与タイプ: <i>{0}</i>。)|  
|TextblockContentTypeHeaderDescription|Media type of the body sent to the API. (この API に送信される本文のメディアの種類。)|  
|ErrorMessageApiNotAccessible|The API you are trying to call is not accessible at this time. (呼び出そうとしている API に現在アクセスできません。) Please contact the API publisher <a href="/issues"\>here</a\>. (<a href="/issues"\>ここ</a\>から API 発行者に問い合わせてください 。)|  
|ErrorMessageApiTimedout|The API you are trying to call is taking longer than normal to get response back. (呼び出そうとしている API は、応答を取得するのに通常より時間がかかっています。) Please contact the API publisher <a href="/issues"\>here</a\>. (<a href="/issues"\>ここ</a\>から API 発行者に問い合わせてください 。)|  
|BadRequestParameterExpected|"'{0}' parameter is expected" ("'{0}' パラメーターが必要です")|  
|TooltipTextDoubleClickToSelectAll|Double click to select all. (ダブルクリックしてすべて選択します。)|  
|TooltipTextHideRevealSecret|Show/Hide (表示/非表示)|  
|ButtonLinkOpenConsole|[試用版]|  
|SectionHeadingRequestBody|要求本文|  
|SectionHeadingRequestParameters|要求パラメーター|  
|SectionHeadingRequestUrl|要求 URL|  
|SectionHeadingResponse|応答|  
|SectionHeadingRequestHeaders|要求ヘッダー|  
|FormLabelSubtextOptional|オプション|  
|SectionHeadingCodeSamples|コード サンプル|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect ID token obtained from <i\>{0}</i\>. (OpenID Connect ID トークンを <i>{0}</i> から取得しました。) Supported grant types: <i\>{1}</i\>. (サポートされている付与タイプ: <i>{0}</i>。)|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|名前|テキスト|  
|----------|----------|  
|LinkLabelBack|戻る|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Send us an e-mail (電子メールを送信する)|  
|PageTitleError|Sorry, there was a problem serving the requested page (要求されたページを提供中に問題が発生しました)|  
|TextblockPotentialCauseIntermittentIssue|This may be an intermittent data access issue that is already gone. (これは、既に解決している断続的なデータ アクセスの問題である可能性があります。)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (クリックしたリンクが古く、現在は正しい場所を指していない可能性があります。)|  
|TextblockPotentialCauseTechnicalProblem|There may be a technical problem on our end. (Microsoft 側で技術的な問題が発生している可能性があります。)|  
|TextblockPotentialSolutionRefresh|Try refreshing the page. (ページを更新してみてください。)|  
|TextblockPotentialSolutionStartOver|Start over from our {0}. ({0} からやり直してください。)|  
|TextblockPotentialSolutionTryAgain|Go {0} and try the action you performed again. ({0} に移動し、実行したアクションをもう一度試してください。)|  
|TextReportProblem|{0} describing what went wrong and we will look at it as soon as we can. ({0} は問題を示しています。Microsoft では、できるだけ早くこれを調査する予定です。)|  
|TitlePotentialCause|Potential cause (潜在的な原因)|  
|TitlePotentialSolution|It's possibly just a temporary issue, a few things to try (単に一時的な問題である可能性があります。次を実行してみてください)|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebIssuesIndexTitle|発行|  
|WebIssuesNoActiveSubscriptions|You have no active subscriptions. (アクティブなサブスクリプションがありません。) You need to subscribe for a product to report an issue. (問題を報告するには、製品をサブスクライブする必要があります。)|  
|WebIssuesNotSignin|You're not signed in. (サインインしていません。) Please {0} to report an issue or post a comment. (問題の報告またはコメントの投稿には {0} を実行してください。)|  
|WebIssuesReportIssueButton|Report Issue (問題の報告)|  
|WebIssuesSignIn|sign in|  
|WebIssuesStatusReportedBy|Status: {0} &#124; Reported by {1} (状態: {0} &#124; 報告者: {0})|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|名前|テキスト|  
|----------|----------|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Send us an e-mail (電子メールを送信する)|  
|PageTitleNotFound|Sorry, we can’t find the page you are looking for (お探しのページが見つかりません)|  
|TextblockPotentialCauseMisspelledUrl|You may have misspelled the URL if you typed it in. (URL を入力した場合にそのスペルが間違っていた可能性があります。)|  
|TextblockPotentialCauseOldLink|The link you have clicked on may be old and not point to the correct location anymore. (クリックしたリンクが古く、現在は正しい場所を指していない可能性があります。)|  
|TextblockPotentialSolutionRetype|Try retyping the URL. (URL をもう一度入力してください。)|  
|TextblockPotentialSolutionStartOver|Start over from our {0}. ({0} からやり直してください。)|  
|TextReportProblem|{0} describing what went wrong and we will look at it as soon as we can. ({0} は問題を示しています。Microsoft では、できるだけ早くこれを調査する予定です。)|  
|TitlePotentialCause|Potential cause (潜在的な原因)|  
|TitlePotentialSolution|Potential solution (可能性のある解決方法)|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebProductsAgreement|By subscribing to {0} Product, I agree to the `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`. ({0} 製品をサブスクライブすることで、使用条件に同意します。)|  
|WebProductsLegalTermsLink|利用条件|  
|WebProductsSubscribeButton|購読|  
|WebProductsUsageLimitsHeader|使用制限|  
|WebProductsYouAreNotSubscribed|You are subscribed to this product. (この製品をサブスクライブしています。)|  
|WebProductsYouRequestedSubscription|You requested subscription to this product. (この製品に対するサブスクリプションを要求しました。)|  
|ErrorYouNeedToAgreeWithLegalTerms|You must agree to the Terms of Use before you can proceed. (続行する前に、使用条件に同意する必要があります。)|  
|ButtonLabelAddSubscription|サブスクリプションを追加する|  
|LinkLabelChangeSubscriptionName|change (変更)|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|You have {0} subscriptions to this product: (この製品のサブスクリプションが {0} 個があります:)|  
|TextblockSingleSubscriptionsCount|You have {0} subscription to this product: (この製品のサブスクリプションが {0} 個があります:)|  
|TextblockSingleApisCount|This product contains {0} API: (この製品には {0} 個の API が含まれています:)|  
|TextblockMultipleApisCount|This product contains {0} APIs: (この製品には {0} 個の API が含まれています:)|  
|TextblockHeaderSubscribe|Subscribe to product (製品をサブスクライブする)|  
|TextblockSubscriptionDescription|A new subscription will be created as follows: (新しいサブスクリプションは次のように作成されます:)|  
|TextblockSubscriptionLimitReached|Subscriptions limit reached. (サブスクリプション数の制限に達しました。)|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|名前|テキスト|  
|----------|----------|  
|PageTitleProducts|製品|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|名前|テキスト|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Sign in is disabled by the administrators at the moment. (サインインは、現在、管理者によって無効になっています。)|  
|TextboxExternalIdentitiesSigninInvitation|Alternatively, sign in with (次のアカウントでサインインすることもできます)|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Sign in with: (次のアカウントでサインイン:)|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|名前|テキスト|  
|----------|----------|  
|PrincipalNotFound|Principal is not found or signature is invalid (プリンシパルが見つからないか、署名が無効です)|  
|ErrorSsoAuthenticationFailed|SSO authentication failed (SSO 認証に失敗しました)|  
|ErrorSsoAuthenticationFailedDetailed|Invalid token provided or signature cannot be verified. (無効なトークンが指定されたか、署名を検証できません。)|  
|ErrorSsoTokenInvalid|SSO token is invalid (SSO トークンが無効です)|  
|ValidationErrorSpecificEmailAlreadyExists|Email '{0}' already registered (電子メール '{0}' は既に登録されています)|  
|ValidationErrorSpecificEmailInvalid|Email '{0}' is invalid (電子メールは '{0}' 無効です)|  
|ValidationErrorPasswordInvalid|Password is invalid. (パスワードが無効です。) Please correct the errors and try again. (エラーを修正してやり直してください。)|  
|PropertyTooShort|{0} is too short ({0} が短すぎます)|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|電子メール アドレスが無効です。|  
|ValidationMessageNewPasswordConfirmationRequired|新しいパスワードの確認入力|  
|ValidationErrorPasswordConfirmationRequired|Confirm password is empty (確認入力パスワードが空です)|  
|WebAuthenticationEmailChangeNotice|Change confirmation email is on the way to {0}. (変更の確認メールは {0} に送信中です。) Please follow instructions within it to confirm your new email address. (メールに記載されている説明に従って、新しいメール アドレスを確認してください。) If the email does not arrive to your inbox in the next few minutes, please check your junk email folder. (数分経過しても受信箱にメールが届かない場合、迷惑メールのフォルダーを確認してください。)|  
|WebAuthenticationEmailChangeNoticeHeader|Your email change request was successfully processed (メール アドレスの変更要求が正常に処理されました)|  
|WebAuthenticationEmailChangeNoticeTitle|Email change requested (メール アドレスの変更が要求されました)|  
|WebAuthenticationEmailHasBeenRevertedNotice|You email already exist. (メール アドレスは既に存在します。) Request has been reverted (要求は戻されました)|  
|ValidationErrorEmailAlreadyExists|Email already exist (メール アドレスは既に存在します)|  
|ValidationErrorEmailInvalid|Invalid e-mail address (メール アドレスが無効です)|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|電子メール アドレスは必須です。|  
|WebAuthenticationErrorNoticeHeader|エラー|  
|WebAuthenticationFieldLengthErrorMessage|{0} must be a maximum length of {1} ({0} は最大長 {0} である必要があります)|  
|TextboxLabelEmailFirstName|名|  
|ValidationErrorFirstNameRequired|First name is required. (名が必要です。)|  
|ValidationErrorFirstNameInvalid|Invalid first name (名が無効です)|  
|NoticeInvalidInvitationToken|Please note that confirmation links are valid for only 48 hours. (確認用のリンクの有効期限は 48 時間であることに注意してください。) If you are still within this timeframe, please make sure your link is correct. (まだこの時間内の場合は、リンクが正しいことを確認してください。) If your link has expired, then please repeat the action you're trying to confirm. (リンクの有効期限が切れている場合は、確認しようとしている操作をもう一度実行してみてください。)|  
|NoticeHeaderInvalidInvitationToken|Invalid invitation token (招待トークンが無効です)|  
|NoticeTitleInvalidInvitationToken|Confirmation error (確認エラー)|  
|WebAuthenticationLastNameInvalidErrorMessage|Invalid last name (姓が無効です)|  
|TextboxLabelEmailLastName|姓|  
|ValidationErrorLastNameRequired|Last name is required. (姓が必要です。)|  
|WebAuthenticationLinkExpiredNotice|Confirmation link sent to you has expired. (送信された確認用のリンクの有効期限が切れました。) `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Your password reset link is invalid or expired. (パスワードのリセット用のリンクが無効か、有効期限が切れています。)|  
|WebAuthenticationLinkExpiredNoticeTitle|Link sent (送信されたリンク)|  
|WebAuthenticationNewPasswordLabel|新しいパスワード|  
|ValidationMessageNewPasswordRequired|New password is required. (新しいパスワードが必要です。)|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (通知の差出人アドレス)|  
|TextboxLabelOrganizationName|組織名|  
|WebAuthenticationOrganizationRequiredErrorMessage|Organization name is empty (組織名が空です)|  
|WebAuthenticationPasswordChangedNotice|Your password was successfully updated (パスワードは正常に更新されました)|  
|WebAuthenticationPasswordChangedNoticeTitle|Password updated (パスワードが更新されました)|  
|WebAuthenticationPasswordCompareErrorMessage|Passwords don't match (パスワードが一致しません)|  
|WebAuthenticationPasswordConfirmLabel|パスワードの確認|  
|ValidationErrorPasswordInvalidDetailed|Password is too weak. (パスワードが脆弱すぎます。)|  
|WebAuthenticationPasswordLabel|Password|  
|ValidationErrorPasswordRequired|パスワードが必要です。|  
|WebAuthenticationPasswordResetSendNotice|Change password confirmation email is on the way to {0}. (パスワードの変更確認メールは {0} に送信中です。) Please follow the instructions within the email to continue your password change process. (メールに記載されている指示に従って、パスワードの変更プロセスを続行してください。)|  
|WebAuthenticationPasswordResetSendNoticeHeader|Your password reset request was successfully processed (パスワードのリセット要求が正常に処理されました)|  
|WebAuthenticationPasswordResetSendNoticeTitle|Password reset requested (パスワードのリセットが要求されました)|  
|WebAuthenticationRequestNotFoundNotice|Request not found (要求が見つかりません)|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Notifications sender email is empty (通知の差出人アドレスが空です)|  
|WebAuthenticationSigninPasswordLabel|Please confirm the change by entering a password (パスワードを入力して、変更を確認してください)|  
|WebAuthenticationSignupConfirmNotice|Registration confirmation email is on its way to {0}.<br /\> Please follow instructions within the email to activate your account.<br /\> If the email does not arrive in your inbox within the next few minutes, please check your junk email folder. (登録の確認メールは {0} に送信中です。メールに記載されている指示に従って、新しいアカウントをアクティブ化してください。数分たっても受信トレイにメールが届かない場合は、迷惑メール フォルダーを確認してください。)|  
|WebAuthenticationSignupConfirmNoticeHeader|Your account was successfully created (アカウントが正常に作成されました)|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Registration confirmation email was sent again (登録の確認メールが再送信されました)|  
|WebAuthenticationSignupConfirmNoticeTitle|作成されるアカウント|  
|WebAuthenticationTokenRequiredErrorMessage|Token is empty (トークンが空です)|  
|WebAuthenticationUserAlreadyRegisteredNotice|It seems a user with this email is already registered in the system. (このメール アドレスを持つユーザーは、既にシステムに登録されているようです。) If you forgot your password, please try to restore it or contact our support team. (パスワードを忘れた場合、復元を試みるか、サポート チームにお問い合わせください。)|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|User already registered (ユーザーは既に登録されています)|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Already registered (登録済み)|  
|ButtonLabelChangePassword|パスワードの変更|  
|ButtonLabelChangeAccountInfo|Change account information (アカウント情報の変更)|  
|ButtonLabelCloseAccount|Close account (アカウントの削除)|  
|WebAuthenticationInvalidCaptchaErrorMessage|Text entered doesn't match text on the picture. (入力されたテキストが、画像のテキストと一致しません。) もう一度やり直してください。|  
|ValidationErrorCredentialsInvalid|Email or password is invalid. (メール アドレスまたはパスワードが無効です。) Please correct the errors and try again. (エラーを修正してやり直してください。)|  
|WebAuthenticationRequestIsNotValid|Request is not valid (要求は無効です)|  
|WebAuthenticationUserIsNotConfirm|Please confirm your registration before attempting to sign in. (サインインする前に登録を確認してください。)|  
|WebAuthenticationInvalidEmailFormated|Email is invalid: {0} (電子メールが無効です: {0})|  
|WebAuthenticationUserNotFound|ユーザーが見つかりません|  
|WebAuthenticationTenantNotRegistered|Your account belongs to a Azure Active Directory tenant which is not authorized to access this portal. (アカウントが属している Azure Active Directory テナントは、このポータルへのアクセスが許可されていません。)|  
|WebAuthenticationAuthenticationFailed|Authentication has failed. (認証に失敗しました。)|  
|WebAuthenticationGooglePlusNotEnabled|Authentication has failed. (認証に失敗しました。) If you authorized the application then please contact the admin to make sure that Google authentication is configured correctly. (アプリケーションを承認したら、管理者に問い合わせて、Google 認証が正しく構成されているかどうかを確認してください。)|  
|ValidationErrorAllowedTenantIsRequired|Allowed Tenant is required (許可されるテナントが必要です)|  
|ValidationErrorTenantIsNotValid|The Azure Active Directory tenant '{0}' is not valid. (Azure Active Directory テナント '{0}' が無効です。)|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Log in using your {0} account ({0} アカウントを使用してログイン)|  
|WebAuthenticationUserLimitNotice|This service has reached the maximum number of allowed users. (このサービスでは、許可されたユーザーの最大数に達しました。) Please `<a href="mailto:{0}"\>contact the administrator</a\>` to upgrade their service and re-enable user registration. (`<a href="mailto:{0}"\>contact the administrator</a\>` で管理者に連絡しサービスをアップグレードし、ユーザー登録をもう一度有効にしてください。)|  
|WebAuthenticationUserLimitNoticeHeader|User registration disabled (ユーザー登録が無効です)|  
|WebAuthenticationUserLimitNoticeTitle|User registration disabled (ユーザー登録が無効です)|  
|WebAuthenticationUserRegistrationDisabledNotice|Registration of users has been disabled by the administrator. (ユーザーの登録は管理者によって無効に設定されています。) Please login with external identity provider. (外部 ID プロバイダーでログインしてください。)|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|User registration disabled (ユーザー登録が無効です)|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|User registration disabled (ユーザー登録が無効です)|  
|WebAuthenticationSignupPendingConfirmationNotice|Before we can complete the creation of your account we need to verify your e-mail address. (アカウントの作成を完了するには、メール アドレスを確認する必要があります。) We’ve sent an e-mail to {0}. ({0} にメールを送信しました。) Please follow the instructions inside the e-mail to activate your account. (メールに記載されている説明に従い、アカウントをアクティブ化してください。) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder. (数分経過してもメールが届かない場合、迷惑メールのフォルダーを確認してください。)|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|We found an unconfirmed account for the e-mail address {0}. (メール アドレス {0} の未確認アカウントが見つかりました。) To complete the creation of your account we need to verify your e-mail address. (アカウントの作成を完了するには、メール アドレスを確認する必要があります。) We’ve sent an e-mail to {0}. ({0} にメールを送信しました。) Please follow the instructions inside the e-mail to activate your account. (メールに記載されている説明に従い、アカウントをアクティブ化してください。) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder. (数分経過してもメールが届かない場合、迷惑メールのフォルダーを確認してください。)|  
|WebAuthenticationSignupConfirmationAlmostDone|Almost Done (もう少しで完了します)|  
|WebAuthenticationSignupConfirmationEmailSent|We’ve sent an e-mail to {0}. ({0} にメールを送信しました。) Please follow the instructions inside the e-mail to activate your account. (メールに記載されている説明に従い、アカウントをアクティブ化してください。) If the e-mail doesn’t arrive within the next few minutes, please check your junk email folder. (数分経過してもメールが届かない場合、迷惑メールのフォルダーを確認してください。)|  
|WebAuthenticationEmailSentNotificationMessage|Email sent successfully to {0} ({0} に電子メールが正常に送信されました)|  
|WebAuthenticationNoAadTenantConfigured|No Azure Active Directory tenant configured for the service. (Azure Active Directory テナントはサービス用に構成されていません。)|  
|CheckboxLabelUserRegistrationTermsConsentRequired|I agree to the `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`. (使用条件に同意します。)|  
|TextblockUserRegistrationTermsProvided|Please review `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>` (使用条件を確認してください。)|  
|DialogHeadingTermsOfUse|利用条件|  
|ValidationMessageConsentNotAccepted|You must agree to the Terms of Use before you can proceed. (続行する前に、使用条件に同意する必要があります。)|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|名前|テキスト|  
|----------|----------|  
|WebAuthenticationForgotPassword|パスワードを忘れた場合|  
|WebAuthenticationIfAdministrator|If you are an Administrator you must sign in `<a href="{0}"\>here</a\>`. (管理者である場合は、ここでサインインする必要があります。)|  
|WebAuthenticationNotAMember|Not a member yet? (まだメンバーではない場合) `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Remember me on this computer (このコンピューターにユーザー名を記憶する)|  
|WebAuthenticationSigininWithPassword|Sign in with your username and password (ユーザー名とパスワードを使用してサインインする)|  
|WebAuthenticationSigninTitle|サインイン|  
|WebAuthenticationSignUpNow|今すぐサインアップ|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|名前|テキスト|  
|----------|----------|  
|PageTitleSignup|サインアップ|  
|WebAuthenticationAlreadyAMember|Already a member? (既にメンバーの場合)|  
|WebAuthenticationCreateNewAccount|Create a new API Management account (新しい API Management アカウントの作成)|  
|WebAuthenticationSigninNow|Sign in now (今すぐサインイン)|  
|ButtonLabelSignup|サインアップ|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|名前|テキスト|  
|----------|----------|  
|SubscriptionCancelConfirmation|Are you sure that you want to cancel this subscription? (このサブスクリプションを取り消しますか?)|  
|SubscriptionRenewConfirmation|Are you sure that you want to renew this subscription? (このサブスクリプションを更新しますか?)|  
|WebDevelopersManageSubscriptions|サブスクリプションの管理|  
|WebDevelopersPrimaryKey|Primary key (プライマリ キー)|  
|WebDevelopersRegenerateLink|再生成|  
|WebDevelopersSecondaryKey|セカンダリ キー|  
|ButtonLabelShowKey|表示|  
|ButtonLabelRenewSubscription|更新|  
|WebDevelopersSubscriptionRequested|Requested on {0} (要求日時: {0})|  
|WebDevelopersSubscriptionRequestedState|Requested (要求済み)|  
|WebDevelopersSubscriptionTableNameHeader|名前|  
|WebDevelopersSubscriptionTableStateHeader|State|  
|WebDevelopersUsageStatisticsLink|Analytics reports (分析レポート)|  
|WebDevelopersYourSubscriptions|Your subscriptions (サブスクリプション)|  
|SubscriptionPropertyLabelRequestedDate|要求日時|  
|SubscriptionPropertyLabelStartedDate|開始日|  
|PageTitleRenameSubscription|Rename subscription (サブスクリプションの名前変更)|  
|SubscriptionPropertyLabelName|サブスクリプション名|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|名前|テキスト|  
|----------|----------|  
|SectionHeadingCloseAccount|Looking to close your account? (アカウントを削除する場合)|  
|PageTitleDeveloperProfile|[プロファイル]|  
|ButtonLabelHideKey|非表示|  
|ButtonLabelRegenerateKey|再生成|  
|InformationMessageKeyWasRegenerated|Are you sure that you want to regenerate this key? (このキーを再生成しますか?)|  
|ButtonLabelShowKey|表示|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|名前|テキスト|  
|----------|----------|  
|ButtonLabelUpdateProfile|プロファイルの更新|  
|PageTitleUpdateProfile|Update account information (アカウント情報の更新)|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> UserProfile  
  
|名前|テキスト|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Change account information (アカウント情報の変更)|  
|ButtonLabelChangePassword|パスワードの変更|  
|ButtonLabelCloseAccount|Close account (アカウントの削除)|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|名|  
|TextboxLabelEmailLastName|姓|  
|TextboxLabelNotificationsSenderEmail|Notifications sender email (通知の差出人アドレス)|  
|TextboxLabelOrganizationName|組織名|  
|SubscriptionStateActive|アクティブ|  
|SubscriptionStateCancelled|キャンセル|  
|SubscriptionStateExpired|有効期限切れ|  
|SubscriptionStateRejected|拒否|  
|SubscriptionStateRequested|Requested (要求済み)|  
|SubscriptionStateSuspended|Suspended|  
|DefaultSubscriptionNameTemplate|{0} (既定値)|  
|SubscriptionNameTemplate|Developer access #{0} (開発者のアクセス番号 {0})|  
|TextboxLabelSubscriptionName|サブスクリプション名|  
|ValidationMessageSubscriptionNameRequired|サブスクリプション名を空にすることはできません。|  
|ApiManagementUserLimitReached|This service has reached the maximum number of allowed users. (このサービスでは、許可されたユーザーの最大数に達しました。) Please upgrade to a higher pricing tier. (より上位の価格レベルにアップグレードしてください。)|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> グリフ リソース  
 API Management 開発者ポータル テンプレートでは、[Bootstrap の glyphicon](https://getbootstrap.com/components/#glyphicons) のページからグリフを使用できます。 このグリフのセットには、[Glyphicon](https://glyphicons.com/) の Halflings セットのフォント形式の 250 個を超えるグリフが含まれています。 このセットからグリフを使用するには、次の構文を使用してください。  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 グリフの完全な一覧については、[Bootstrap の Glyphicon](https://getbootstrap.com/components/#glyphicons)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。
