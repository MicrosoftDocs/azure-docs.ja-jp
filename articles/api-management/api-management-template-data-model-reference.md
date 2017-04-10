---
title: "Azure API Management テンプレート データ モデル リファレンス | Microsoft Docs"
description: "Azure API Management の開発者ポータル テンプレートのデータ モデルで使用される一般的な項目のエンティティと型表現について説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 080ac72246e62948c3defe329028f27751e8c78d
ms.openlocfilehash: 5f8dd5c6a106e5dc523624d8930e6297dbdc368f

---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management テンプレート データ モデル リファレンス
このトピックでは、Azure API Management の開発者ポータル テンプレートのデータ モデルで使用される一般的な項目のエンティティと型表現について説明します。  
  
 テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)に関するページを参照してください。  
  
-   [API](#API)  
-   [API summary](#APISummary)  
-   [アプリケーション](#Application)  
-   [Attachment](#Attachment)  
-   [Code sample](#Sample)  
-   [Comment](#Comment)  
-   [Filtering](#Filtering)  
-   [ヘッダー](#Header)  
-   [HTTP 要求](#HTTPRequest)  
-   [HTTP 応答](#HTTPResponse)  
-   [問題](#Issue)  
-   [操作](#Operation)  
-   [Operation menu](#Menu)  
-   [Operation menu item](#MenuItem)  
-   [Paging](#Paging)  
-   [パラメーター](#Parameter)  
-   [成果物](#Product)  
-   [プロバイダー](#Provider)  
-   [Representation](#Representation)  
-   [サブスクリプション](#Subscription)  
-   [Subscription summary](#SubscriptionSummary)  
-   [User account info](#UserAccountInfo)  
-   [User sign in](#UseSignIn)  
-   [User sign up](#UserSignUp)  
  
##  <a name="a-nameapia-api"></a><a name="API"></a> API  
 `API` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|id|string|リソース識別子。 現在の API Management サービス インスタンス内の API を一意に識別します。 値は `apis/{id}` 形式の有効な相対 URL です。ここで、`{id}` は API 識別子です。 このプロパティは読み取り専用です。|  
|name|string|API の名前。 空にしないでください。 最大長は 100 文字です。|  
|description|string|API の説明。 空にしないでください。 HTML 書式設定タグを含めることができます。 最大長は 1,000 文字です。|  
|serviceUrl|string|この API を実装するバックエンド サービスの絶対 URL。|  
|パス|string|API Management サービス インスタンス内のこの API とそのすべてのリソース パスを一意に識別する相対 URL。 この API のパブリック URL を作成するために、サービス インスタンスの作成時に指定された API エンドポイントのベース URL に追加されます。|  
|protocols|番号の配列|この API の操作を呼び出すことができるプロトコルを記述します。 使用できる値は、`1 - http`、`2 - https`、またはその両方です。|  
|authenticationSettings|[承認サーバーの認証設定](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|この API に含まれる認証設定のコレクション。|  
|subscriptionKeyParameterNames|オブジェクト|サブスクリプション キーを含むクエリまたはヘッダー パラメーターのカスタム名を指定するために使用できる、省略可能なプロパティ。 このプロパティが存在する場合は、次の&2; つのプロパティのうち少なくとも&1; つが含まれている必要があります。<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="a-nameapisummarya-api-summary"></a><a name="APISummary"></a> API summary  
 `API summary` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|id|string|リソース識別子。 現在の API Management サービス インスタンス内の API を一意に識別します。 値は `apis/{id}` 形式の有効な相対 URL です。ここで、`{id}` は API 識別子です。 このプロパティは読み取り専用です。|  
|name|string|API の名前。 空にしないでください。 最大長は 100 文字です。|  
|description|string|API の説明。 空にしないでください。 HTML 書式設定タグを含めることができます。 最大長は 1,000 文字です。|  
  
##  <a name="a-nameapplicationa-application"></a><a name="Application"></a> Application  
 `application` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|アプリケーションの一意の識別子。|  
|タイトル|string|アプリケーションのタイトル。|  
|Description|string|アプリケーションの説明。|  
|Url|URI|アプリケーションの URI。|  
|バージョン|string|アプリケーションのバージョン情報。|  
|必要条件|string|アプリケーションの要件の説明。|  
|状態|number|アプリケーションの現在の状態。<br /><br /> - 0 - 登録済み<br /><br /> - 1 - 送信済み<br /><br /> - 2 - 発行済み<br /><br /> - 3 - 拒否<br /><br /> - 4 - 未発行|  
|RegistrationDate|DateTime|アプリケーションが登録された日時。|  
|CategoryId|number|アプリケーションのカテゴリ (財務、エンターテイメントなど)|  
|DeveloperId|string|アプリケーションを送信した開発者の一意の識別子。|  
|[添付ファイル]|[Attachment](#Attachment) エンティティのコレクション。|アプリケーションの添付ファイル (スクリーンショット、アイコンなど)。|  
|アイコン|[Attachment](#Attachment)|アプリケーションのアイコン。|  
  
##  <a name="a-nameattachmenta-attachment"></a><a name="Attachment"></a> Attachment  
 `attachment` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|説明|  
|--------------|----------|-----------------|  
|UniqueId|string|添付ファイルの一意の識別子。|  
|Url|string|リソースの URL。|  
|型|string|添付ファイルの種類。|  
|ContentType|string|添付ファイルのメディアの種類。|  
  
##  <a name="a-namesamplea-code-sample"></a><a name="Sample"></a> Code sample  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|title|string|操作の名前。|  
|snippet|string|このプロパティは廃止されました。使用しないでください。|  
|brush|string|コード サンプルを表示するときに使用されるコード構文の色分けテンプレート。 使用できる値は、`plain`、`php`、`java`、`xml`、`objc`、`python`、`ruby`、`csharp` です。|  
|template|string|このコード サンプル テンプレートの名前。|  
|body|string|スニペットのコード サンプル部分のプレースホルダー。|  
|静的メソッド|string|操作の HTTP メソッド。|  
|scheme|string|操作要求に使用するプロトコル。|  
|パス|string|操作のパス。|  
|query|string|パラメーターが定義されたクエリ文字列の例。|  
|host|string|この操作を含む API の API Management サービス ゲートウェイの URL。|  
|ヘッダー|[Header](#Header) エンティティのコレクション。|この操作のヘッダー。|  
|parameters|[Parameter](#Parameter) エンティティのコレクション。|この操作に対して定義されているパラメーター。|  
  
##  <a name="a-namecommenta-comment"></a><a name="Comment"></a> Comment  
 `API` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|number|コメントの ID。|  
|CommentText|string|コメントの本文。 HTML を含めることができます。|  
|DeveloperCompany|string|開発者の会社名。|  
|PostedOn|DateTime|コメントが投稿された日時。|  
  
##  <a name="a-nameissuea-issue"></a><a name="Issue"></a> Issue  
 `issue` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|問題の一意の識別子。|  
|ApiID|string|この問題が報告された API の ID。|  
|タイトル|string|問題のタイトル。|  
|説明|string|問題の説明。|  
|SubscriptionDeveloperName|string|問題を報告した開発者の名。|  
|IssueState|string|問題の現在の状態。 使用できる値は、Proposed、Opened、および Closed です。|  
|ReportedOn|DateTime|問題が報告された日時。|  
|説明|[Comment](#Comment) エンティティのコレクション。|この問題に関するコメント。|  
|[添付ファイル]|[Attachment](api-management-template-data-model-reference.md#Attachment) エンティティのコレクション。|問題に対する添付ファイル。|  
|サービス|[API](#API) エンティティのコレクション。|問題を提出したユーザーがサブスクライブしている API。|  
  
##  <a name="a-namefilteringa-filtering"></a><a name="Filtering"></a> Filtering  
 `filtering` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|説明|  
|--------------|----------|-----------------|  
|パターン|string|現在の検索語句。検索語句がない場合は `null`。|  
|Placeholder|string|検索語句が指定されていないときに検索ボックスに表示するテキスト。|  
  
##  <a name="a-nameheadera-header"></a><a name="Header"></a> Header  
 このセクションでは、`parameter` 表現を記述します。  
  
|プロパティ|説明|型|  
|--------------|-----------------|----------|  
|name|string|パラメーター名。|  
|description|string|パラメーターの説明。|  
|値|string|ヘッダー値。|  
|typeName|string|ヘッダー値のデータ型。|  
|options|string|オプション。|  
|必須|boolean|ヘッダーが必要かどうか。|  
|readOnly|boolean|ヘッダーが読み取り専用かどうか。|  
  
##  <a name="a-namehttprequesta-http-request"></a><a name="HTTPRequest"></a> HTTP Request  
 このセクションでは、`request` 表現を記述します。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|description|string|操作要求の説明。|  
|ヘッダー|[Header](#Header) エンティティの配列。|要求ヘッダー。|  
|parameters|[Parameter](#Parameter) の配列|操作要求パラメーターのコレクション。|  
|representations|[Representation](#Representation) の配列|操作要求表現のコレクション。|  
  
##  <a name="a-namehttpresponsea-http-response"></a><a name="HTTPResponse"></a> HTTP Response  
 このセクションでは、`response` 表現を記述します。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|StatusCode|正の整数|操作応答の状態コード。|  
|description|string|操作応答の説明。|  
|representations|[Representation](#Representation) の配列|操作応答表現のコレクション。|  
  
##  <a name="a-nameoperationa-operation"></a><a name="Operation"></a> Operation  
 `operation` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|id|string|リソース識別子。 現在の API Management サービス インスタンス内の操作を一意に識別します。 値は `apis/{aid}/operations/{id}` 形式の有効な相対 URL です。ここで、`{aid}` は API 識別子、`{id}` は操作識別子です。 このプロパティは読み取り専用です。|  
|name|string|操作の名前。 空にしないでください。 最大長は 100 文字です。|  
|description|string|操作の説明。 空にしないでください。 HTML 書式設定タグを含めることができます。 最大長は 1,000 文字です。|  
|scheme|string|この API の操作を呼び出すことができるプロトコルを記述します。 使用できる値は、`http`、`https`、または `http` と `https` の両方です。|  
|uriTemplate|string|この操作のターゲット リソースを識別する相対 URL テンプレート。 パラメーターを含めることができます。 例: `customers/{cid}/orders/{oid}/?date={date}`|  
|host|string|API をホストする API Management ゲートウェイの URL。|  
|httpMethod|string|操作の HTTP メソッド。|  
|request|[HTTP 要求](#HTTPRequest)|要求の詳細を含むエンティティ。|  
|responses|[HTTP Response](#HTTPResponse) の配列|操作の [HTTP Response](#HTTPResponse) エンティティの配列。|  
  
##  <a name="a-namemenua-operation-menu"></a><a name="Menu"></a> Operation menu  
 `operation menu` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|説明|  
|--------------|----------|-----------------|  
|ApiId|string|現在の API の ID。|  
|CurrentOperationId|string|現在の操作の ID。|  
|アクション|string|メニューの種類。|  
|MenuItems|[Operation menu item](#MenuItem) エンティティのコレクション。|現在の API の操作。|  
  
##  <a name="a-namemenuitema-operation-menu-item"></a><a name="MenuItem"></a> Operation menu item  
 `operation menu item` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|操作の ID。|  
|タイトル|string|操作の説明。|  
|HttpMethod|string|操作の HTTP メソッド。|  
  
##  <a name="a-namepaginga-paging"></a><a name="Paging"></a> Paging  
 `paging` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ページ|number|現在のページ番号。|  
|PageSize|number|1 ページに表示される結果の最大数。|  
|TotalItemCount|number|表示する項目の数。|  
|ShowAll|boolean|すべての結果を&1; ページに表示するかどうか。|  
|PageCount|number|結果のページ数。|  
  
##  <a name="a-nameparametera-parameter"></a><a name="Parameter"></a> Parameter  
 このセクションでは、`parameter` 表現を記述します。  
  
|プロパティ|説明|型|  
|--------------|-----------------|----------|  
|name|string|パラメーター名。|  
|description|string|パラメーターの説明。|  
|値|string|パラメーター値。|  
|options|文字列の配列|クエリ パラメーター値に対して定義されている値。|  
|必須|boolean|パラメーターが必要かどうかを指定します。|  
|kind|number|このパラメーターがパス パラメーター (1) とクエリ文字列パラメーター (2) のいずれであるか。|  
|typeName|string|パラメーターの型。|  
  
##  <a name="a-nameproducta-product"></a><a name="Product"></a> Product  
 `product` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|リソース識別子。 現在の API Management サービス インスタンス内の製品を一意に識別します。 値は `products/{pid}` 形式の有効な相対 URL です。ここで、`{pid}` は製品識別子です。 このプロパティは読み取り専用です。|  
|タイトル|string|製品の名前。 空にしないでください。 最大長は 100 文字です。|  
|説明|string|製品の説明。 空にしないでください。 HTML 書式設定タグを含めることができます。 最大長は 1,000 文字です。|  
|用語|string|製品の使用条件。 製品をサブスクライブしようとする開発者に提示される条件です。開発者は、サブスクリプション プロセスを完了する前にこれらの条件に同意する必要があります。|  
|ProductState|number|製品が発行されているかどうかを指定します。 発行されている製品は、開発者ポータルで開発者に表示されます。 発行されていない製品は、管理者のみに表示されます。<br /><br /> 製品状態に使用できる値は次のとおりです。<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|boolean|1 人のユーザーがこの製品に対して複数のサブスクリプションを同時に持つことができるかどうかを指定します。|  
|MultipleSubscriptionsCount|number|現在のユーザーがこの製品に対して持つサブスクリプションの数。|  
  
##  <a name="a-nameprovidera-provider"></a><a name="Provider"></a> Provider  
 `provider` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|プロパティ|文字列辞書|この認証プロバイダーのプロパティ。|  
|AuthenticationType|string|プロバイダーの種類  (Azure Active Directory、Facebook ログイン、Google アカウント、Microsoft アカウント、Twitter)。|  
|Caption|string|プロバイダーの表示名。|  
  
##  <a name="a-namerepresentationa-representation"></a><a name="Representation"></a> Representation  
 このセクションでは、`representation` を記述します。  
  
|プロパティ|型|説明|  
|--------------|----------|-----------------|  
|contentType|string|この表現の登録済みコンテンツ タイプまたはカスタム コンテンツ タイプを指定します (例: `application/xml`)。|  
|サンプル|string|表現の例。|  
  
##  <a name="a-namesubscriptiona-subscription"></a><a name="Subscription"></a> Subscription  
 `subscription` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|リソース識別子。 現在の API Management サービス インスタンス内のサブスクリプションを一意に識別します。 値は `subscriptions/{sid}` 形式の有効な相対 URL です。ここで、`{sid}` はサブスクリプション識別子です。 このプロパティは読み取り専用です。|  
|ProductId|string|サブスクライブされている製品の製品リソース識別子。 値は `products/{pid}` 形式の有効な相対 URL です。ここで、`{pid}` は製品識別子です。|  
|ProductTitle|string|製品の名前。 空にしないでください。 最大長は 100 文字です。|  
|ProductDescription|string|製品の説明。 空にしないでください。 HTML 書式設定タグを含めることができます。 最大長は 1,000 文字です。|  
|ProductDetailsUrl|string|製品詳細への相対 URL。|  
|state|string|サブスクリプションの状態。 次の状態があります。<br /><br /> - `0 - suspended` – サブスクリプションがブロックされています。サブスクライバーは製品の API を呼び出すことができません。<br /><br /> - `1 - active` – サブスクリプションがアクティブです。<br /><br /> - `2 - expired` – サブスクリプションの期限になり、サブスクリプションが非アクティブ化されました。<br /><br /> - `3 - submitted` – サブスクリプション要求が開発者によって行われましたが、まだ承認または拒否されていません。<br /><br /> - `4 - rejected` – サブスクリプション要求が管理者によって拒否されました。<br /><br /> - `5 - cancelled` – サブスクリプションが開発者または管理者によって取り消されました。|  
|DisplayName|string|サブスクリプションの表示名。|  
|CreatedDate|dateTime|ISO 8601 形式のサブスクリプションが作成された日付: `2014-06-24T16:25:00Z`。|  
|CanBeCancelled|boolean|現在のユーザーがサブスクリプションを取り消すことができるかどうか。|  
|IsAwaitingApproval|boolean|サブスクリプションが承認を待っているかどうか。|  
|StartDate|dateTime|ISO 8601 形式のサブスクリプションの開始日: `2014-06-24T16:25:00Z`。|  
|ExpirationDate|dateTime|ISO 8601 形式のサブスクリプションの有効期限: `2014-06-24T16:25:00Z`。|  
|NotificationDate|dateTime|ISO 8601 形式のサブスクリプションの通知日: `2014-06-24T16:25:00Z`。|  
|primaryKey|string|プライマリ サブスクリプション キー。 最大長は 256 文字です。|  
|secondaryKey|string|セカンダリ サブスクリプション キー。 最大長は 256 文字です。|  
|CanBeRenewed|boolean|現在のユーザーがサブスクリプションを更新できるかどうか。|  
|HasExpired|boolean|サブスクリプションが期限切れになっているかどうか。|  
|IsRejected|boolean|サブスクリプション要求が拒否されたかどうか。|  
|CancelUrl|string|サブスクリプションを取り消すための相対 URL。|  
|RenewUrl|string|サブスクリプションを更新するための相対 URL。|  
  
##  <a name="a-namesubscriptionsummarya-subscription-summary"></a><a name="SubscriptionSummary"></a> Subscription summary  
 `subscription summary` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|ID|string|リソース識別子。 現在の API Management サービス インスタンス内のサブスクリプションを一意に識別します。 値は `subscriptions/{sid}` 形式の有効な相対 URL です。ここで、`{sid}` はサブスクリプション識別子です。 このプロパティは読み取り専用です。|  
|DisplayName|string|サブスクリプションの表示名|  
  
##  <a name="a-nameuseraccountinfoa-user-account-info"></a><a name="UserAccountInfo"></a> User account info  
 `user account info` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|説明|  
|--------------|----------|-----------------|  
|FirstName|string|名。 空にしないでください。 最大長は 100 文字です。|  
|LastName|string|姓。 空にしないでください。 最大長は 100 文字です。|  
|電子メール|string|電子メール アドレス。 空にしないでください。値は、サービス インスタンス内で一意である必要があります。 最大長は 254 文字です。|  
|パスワード|string|ユーザー アカウントのパスワード。|  
|NameIdentifier|string|アカウント識別子。ユーザーの電子メールと同じです。|  
|ProviderName|string|認証プロバイダーの名前。|  
|IsBasicAccount|boolean|このアカウントが電子メールとパスワードを使用して登録されている場合は true、このアカウントがプロバイダーを使用して登録されている場合は false です。|  
  
##  <a name="a-nameusesignina-user-sign-in"></a><a name="UseSignIn"></a> User sign in  
 `user sign in` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|電子メール|string|電子メール アドレス。 空にしないでください。値は、サービス インスタンス内で一意である必要があります。 最大長は 254 文字です。|  
|パスワード|string|ユーザー アカウントのパスワード。|  
|ReturnUrl|string|ユーザーがサインインをクリックしたページの URL。|  
|RememberMe|boolean|現在のユーザーの情報を保存するかどうか。|  
|RegistrationEnabled|boolean|登録が有効になっているかどうか。|  
|DelegationEnabled|boolean|委任されたサインインが有効になっているかどうか。|  
|DelegationUrl|string|委任されたサインイン URL (有効な場合)。|  
|SsoSignUpUrl|string|ユーザーのシングル サインオン URL (存在する場合)。|  
|AuxServiceUrl|string|現在のユーザーが管理者である場合、これは、Azure クラシック ポータルのサービス インスタンスへのリンクを示します。|  
|プロバイダー|[Provider](#Provider) エンティティのコレクション|このユーザーの認証プロバイダー。|  
|UserRegistrationTerms|string|ユーザーがサインインする前に同意する必要がある条件。|  
|UserRegistrationTermsEnabled|boolean|条件が有効になっているかどうか。|  
  
##  <a name="a-nameusersignupa-user-sign-up"></a><a name="UserSignUp"></a> User sign up  
 `user sign up` エンティティには、次のプロパティがあります。  
  
|プロパティ|型|Description|  
|--------------|----------|-----------------|  
|PasswordConfirm|boolean|[sign-up](api-management-page-controls.md#sign-up) コントロールで使用される値。|  
|パスワード|string|ユーザー アカウントのパスワード。|  
|PasswordVerdictLevel|number|[sign-up](api-management-page-controls.md#sign-up) コントロールで使用される値。|  
|UserRegistrationTerms|string|ユーザーがサインインする前に同意する必要がある条件。|  
|UserRegistrationTermsOptions|number|[sign-up](api-management-page-controls.md#sign-up) コントロールで使用される値。|  
|ConsentAccepted|boolean|[sign-up](api-management-page-controls.md#sign-up) コントロールで使用される値。|  
|電子メール|string|電子メール アドレス。 空にしないでください。値は、サービス インスタンス内で一意である必要があります。 最大長は 254 文字です。|  
|FirstName|string|名。 空にしないでください。 最大長は 100 文字です。|  
|LastName|string|姓。 空にしないでください。 最大長は 100 文字です。|  
|UserData|string|[sign-up](api-management-page-controls.md#sign-up) コントロールで使われる値。|  
|NameIdentifier|string|[sign-up](api-management-page-controls.md#sign-up) コントロールで使用される値。|  
|ProviderName|string|認証プロバイダーの名前。|

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。



<!--HONumber=Feb17_HO2-->


