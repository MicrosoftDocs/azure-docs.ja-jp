---
title: "Azure API Management ポリシー式 | Microsoft Docs"
description: "Azure API Management 内のポリシー式について説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 3152a1306f2c3eeb42dd3b21cff62b696ed01e5d
ms.openlocfilehash: 75ea0486a1b5abc71df3b7d9e8385717954b89f4
ms.lasthandoff: 03/01/2017

---
# <a name="api-management-policy-expressions"></a>API Management ポリシー式
ポリシー式の構文は C# 6.0 です。 それぞれの式は、暗黙的に指定された[コンテキスト](api-management-policy-expressions.md#ContextVariables)変数と、許可されている .NET Framework の型の[サブセット](api-management-policy-expressions.md#CLRTypes)にアクセスできます。  
  
> [!NOTE]
>  ポリシー式の詳細については、[ポリシー式](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/)のビデオを参照してください。  
>   
>  ポリシー式を使用したポリシー構成のデモを、「[Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」 (Cloud Cover エピソード 177: Vlad Vinogradsky と学ぶ API Management 機能) でご覧いただくことができます。 このビデオには、以下のポリシー式のデモが含まれています。  
>   
>  -   10:30 - [クエリ文字列パラメーターを設定する](api-management-transformation-policies.md#SetQueryStringParameter)ポリシーと [HTTP ヘッダーを設定する](api-management-transformation-policies.md#SetHTTPheader)ポリシーを使用して、API レベルでポリシーを適用してバックエンド サービスにコンテキスト情報を提供する方法を示します。 12:10 で、開発者ポータルで操作を呼び出すデモが示されており、これらのポリシーの動作を確認できます。  
> -   13:50 - [JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、トークン クレームに基づいて操作へのアクセスを事前に承認する方法を示します。 15:00 まで早送りし、ポリシー エディターで構成されるポリシーをご覧いただいた後、開発者ポータルから操作を呼び出す方法について、必要な認証トークンを使用した場合と使用しない場合の両方のデモを 18:50 からご覧ください。  
> -   21:00 - [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) トレースによってポリシーの評価方法と評価結果を確認する方法を示します。  
> -   25:25 - [キャッシュから取得する](api-management-caching-policies.md#GetFromCache)ポリシーと[ストアにキャッシュする](api-management-caching-policies.md#StoreToCache)ポリシーとを使用したポリシー式によって、API Management 応答のキャッシュ時間を、バックエンド サービスの `Cache-Control` ディレクティブによって指定されたバックエンド サービスの応答キャッシュ時間と一致するように構成する方法を示します。  
> -   34:30 - バックエンド サービスから受信した応答で[制御フロー](api-management-advanced-policies.md#choose) ポリシーおよび[本文設定](api-management-transformation-policies.md#SetBody)ポリシーが使用されている場合に、データ要素を削除してコンテンツ フィルタリングを行う方法を示します。 このデモで使用されている [Dark Sky Forecast API](https://developer.forecast.io/) の概要について確認する場合は、31:50 から再生してください。  
> -   このビデオで使用されているポリシー ステートメントをダウンロードするには、 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github リポジトリをご覧ください。  
  
  
##  <a name="Syntax"></a>構文  
 単一ステートメントの式は `@(expression)` の形式で囲みます。`expression` は正しい C# 式ステートメントです。  
  
 複数ステートメントの式は `@{expression}` の形式で囲みます。 複数ステートメントの式内のすべてのコード パスは `return` ステートメントで終了している必要があります。  
  
##  <a name="PolicyExpressionsExamples"></a>例  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a>使用法  
 式は、ポリシー参照で特に指定されていない限り、任意の API Management [ポリシー](api-management-policies.md)で属性値またはテキスト値として使用できます。  
  
> [!IMPORTANT]
>  ポリシー式を使用する場合には、ポリシーを定義したときにポリシー式を検証する方法は限られているので、ご注意ください。 式は、ゲートウェイによって受信または送信パイプラインの実行時に実行されるため、ポリシー式によって実行時例外が生成されると、必ず API 呼び出しの実行時エラーが発生します。  
  
##  ポリシー式で使用できる <a name="CLRTypes"></a>.NET framework の型  
 次の表は、ポリシー式で使用できる .NET Framework の型とメンバーの一覧です。  
  
|CLR 型|サポートされるメソッド|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|すべてのメソッド|  
|Newtonsoft.Json.Linq.JArray|すべてのメソッド|  
|Newtonsoft.Json.Linq.JConstructor|すべてのメソッド|  
|Newtonsoft.Json.Linq.JContainer|すべてのメソッド|  
|Newtonsoft.Json.Linq.JObject|すべてのメソッド|  
|Newtonsoft.Json.Linq.JProperty|すべてのメソッド|  
|Newtonsoft.Json.Linq.JRaw|すべてのメソッド|  
|Newtonsoft.Json.Linq.JToken|すべてのメソッド|  
|Newtonsoft.Json.Linq.JTokenType|すべてのメソッド|  
|Newtonsoft.Json.Linq.JValue|すべてのメソッド|  
|System.Collections.Generic.IReadOnlyCollection<T\>|すべて|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|すべて|  
|System.Collections.Generic.ISet<TKey, TValue>|すべて|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key、Value|  
|System.Collections.Generic.List<TKey, TValue>|すべて|  
|System.Collections.Generic.Queue<TKey, TValue>|すべて|  
|System.Collections.Generic.Stack<TKey, TValue>|すべて|  
|System.Convert|すべて|  
|System.DateTime|すべて|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|すべて|  
|System.Decimal|すべて|  
|System.Double|すべて|  
|System.Guid|すべて|  
|System.IEnumerable<T\>|すべて|  
|System.IEnumerator<T\>|すべて|  
|System.Int16|すべて|  
|System.Int32|すべて|  
|System.Int64|すべて|  
|System.Linq.Enumerable<T\>|すべてのメソッド|  
|System.Math|すべて|  
|System.MidpointRounding|すべて|  
|System.Nullable<T\>|すべて|  
|System.Random|すべて|  
|System.SByte|すべて|  
|System.Security.Cryptography. HMACSHA384|すべて|  
|System.Security.Cryptography. HMACSHA512|すべて|  
|System.Security.Cryptography.HashAlgorithm|すべて|  
|System.Security.Cryptography.HMAC|すべて|  
|System.Security.Cryptography.HMACMD5|すべて|  
|System.Security.Cryptography.HMACSHA1|すべて|  
|System.Security.Cryptography.HMACSHA256|すべて|  
|System.Security.Cryptography.KeyedHashAlgorithm|すべて|  
|System.Security.Cryptography.MD5|すべて|  
|System.Security.Cryptography.RNGCryptoServiceProvider|すべて|  
|System.Security.Cryptography.SHA1|すべて|  
|System.Security.Cryptography.SHA1Managed|すべて|  
|System.Security.Cryptography.SHA256|すべて|  
|System.Security.Cryptography.SHA256Managed|すべて|  
|System.Security.Cryptography.SHA384|すべて|  
|System.Security.Cryptography.SHA384Managed|すべて|  
|System.Security.Cryptography.SHA512|すべて|  
|System.Security.Cryptography.SHA512Managed|すべて|  
|System.Single|すべて|  
|System.String|すべて|  
|System.StringSplitOptions|すべて|  
|System.Text.Encoding|すべて|  
|System.Text.RegularExpressions.Capture|Index、Length、Value|  
|System.Text.RegularExpressions.CaptureCollection|Count、Item|  
|System.Text.RegularExpressions.Group|Captures、Success|  
|System.Text.RegularExpressions.GroupCollection|Count、Item|  
|System.Text.RegularExpressions.Match|Empty、Groups、Result|  
|System.Text.RegularExpressions.Regex|.ctor、IsMatch、Match、Matches、Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled、IgnoreCase、IgnorePatternWhitespace、Multiline、None、RightToLeft、Singleline|  
|System.TimeSpan|すべて|  
|System.Tuple|すべて|  
|System.UInt16|すべて|  
|System.UInt32|すべて|  
|System.UInt64|すべて|  
|System.Uri|すべて|  
|System.Xml.Linq.Extensions|すべてのメソッド|  
|System.Xml.Linq.XAttribute|すべてのメソッド|  
|System.Xml.Linq.XCData|すべてのメソッド|  
|System.Xml.Linq.XComment|すべてのメソッド|  
|System.Xml.Linq.XContainer|すべてのメソッド|  
|System.Xml.Linq.XDeclaration|すべてのメソッド|  
|System.Xml.Linq.XDocument|すべてのメソッド|  
|System.Xml.Linq.XDocumentType|すべてのメソッド|  
|System.Xml.Linq.XElement|すべてのメソッド|  
|System.Xml.Linq.XName|すべてのメソッド|  
|System.Xml.Linq.XNamespace|すべてのメソッド|  
|System.Xml.Linq.XNode|すべてのメソッド|  
|System.Xml.Linq.XNodeDocumentOrderComparer|すべてのメソッド|  
|System.Xml.Linq.XNodeEqualityComparer|すべてのメソッド|  
|System.Xml.Linq.XObject|すべてのメソッド|  
|System.Xml.Linq.XProcessingInstruction|すべてのメソッド|  
|System.Xml.Linq.XText|すべてのメソッド|  
|System.Xml.XmlNodeType|すべて|  
  
##  <a name="ContextVariables"></a>コンテキスト変数  
 `context` という名前の変数は、暗黙的にすべてのポリシー[式](api-management-policy-expressions.md#Syntax)で使用できます。 そのメンバーは `\request`に関連する情報を提供します。 すべての `context` メンバーは読み取り専用です。  
  
|コンテキスト変数|使用可能なメソッド、プロパティ、パラメーターの値|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> デプロイ<br /><br /> LastError<br /><br /> 操作<br /><br /> 製品<br /><br /> 要求<br /><br /> RequestId: 文字列<br /><br /> 応答<br /><br /> [サブスクリプション]<br /><br /> トレース: bool<br /><br /> ユーザー<br /><br /> 変数: IReadOnlyDictionary<string, object><br /><br /> void Trace(message: 文字列)|  
|context.Api|Id: 文字列<br /><br /> Name: 文字列<br /><br /> Path: 文字列<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: 文字列<br /><br /> ServiceName: 文字列|  
|context.LastError|Source: 文字列<br /><br /> Reason: 文字列<br /><br /> Message: 文字列<br /><br /> Scope: 文字列<br /><br /> Section: 文字列<br /><br /> Path: 文字列<br /><br /> PolicyId: 文字列<br /><br /> context.LastError の詳細については、[エラー処理](api-management-error-handling-policies.md)に関する記事を参照してください。|  
|context.Operation|Id: 文字列<br /><br /> Method: 文字列<br /><br /> Name: 文字列<br /><br /> UrlTemplate: 文字列|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: ブール値<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: 文字列<br /><br /> Name: 文字列<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: ブール値|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<文字列, 文字列[]><br /><br /> IpAddress: 文字列<br /><br /> MatchedParameters: IReadOnlyDictionary<文字列, 文字列><br /><br /> Method: 文字列<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの要求ヘッダー値、またはヘッダーが見つからない場合は `defaultValue` を返します。|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<文字列, 文字列[]><br /><br /> StatusCode: 整数<br /><br /> StatusReason: 文字列|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの応答ヘッダー値、またはヘッダーが見つからない場合は `defaultValue` を返します。|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: 文字列<br /><br /> Key: 文字列<br /><br /> Name: 文字列<br /><br /> PrimaryKey: 文字列<br /><br /> SecondaryKey: 文字列<br /><br /> StartDate: DateTime?|  
|context.User|Email: 文字列<br /><br /> FirstName: 文字列<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: 文字列<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: 文字列<br /><br /> Note: 文字列<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: 文字列<br /><br /> Name: 文字列<br /><br /> Path: 文字列<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: 文字列<br /><br /> Name: 文字列|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>` メソッドと `context.Request.Body.As<T>` メソッドは、指定した書式のいずれかで要求または応答のメッセージ本文を読み取るのに使用します。 既定では、メッセージ本文にアクセスした場合に元のメッセージ本文は失われるため、この本文を読み込んだ式で本文を戻して設定する必要があります。 本文の内容を保持するには、メッセージへのアクセス時に `preserveContent` パラメーターを `true` に設定します。 `preserveContent` の既定値は `false` です。 `preserveContent` を `true` に設定している場合に式から本文が返されると、返された本文が使用されます。 詳細については、[Set body](api-management-transformation-policies.md#SetBody) ポリシーを参照してください。|  
|IUrl|Host: 文字列<br /><br /> Path: 文字列<br /><br /> Port: 整数<br /><br /> Query: IReadOnlyDictionary<文字列, 文字列[]><br /><br /> QueryString: 文字列<br /><br /> Scheme: 文字列|  
|IUserIdentity|Id: 文字列<br /><br /> Provider: 文字列|  
|ISubscriptionKeyParameterNames|Header: 文字列<br /><br /> Query: 文字列|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りのクエリ パラメーターの値、またはパラメーターが見つからない場合は `defaultValue` を返します。|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: 文字列<br /><br /> defaultValue: T<br /><br /> `T` 型へのキャスト変数値、または変数が見つからない場合は `defaultValue` を返します。<br /><br /> このメソッドは、指定した型が返される変数の実際の型と一致しない場合に、例外をスローします。|  
|BasicAuthCredentials AsBasic(input: this string)|input: 文字列<br /><br /> 入力パラメーターに有効な HTTP 基本認証の承認要求ヘッダー値が含まれている場合、メソッドは `BasicAuthCredentials` 型のオブジェクトを返します。それ以外の場合、メソッドは null を返します。|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: 文字列<br /><br /> result: out BasicAuthCredentials<br /><br /> 入力パラメーターに有効な HTTP 基本認証の承認要求ヘッダー値が含まれている場合、メソッドは `true` を返し、結果パラメータには `BasicAuthCredentials` 型の値が含まれます。それ以外の場合、メソッドは `false` を返します。|  
|BasicAuthCredentials|Password: 文字列<br /><br /> UserId: 文字列|  
|Jwt AsJwt(input: this string)|input: 文字列<br /><br /> 入力パラメーターに有効な JWT トークン値が含まれている場合、メソッドは `Jwt` 型のオブジェクトを返します。それ以外の場合、メソッドは `null` を返します。|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: 文字列<br /><br /> result: out Jwt<br /><br /> 入力パラメーターに有効な JWT トークン値が含まれている場合、メソッドは `true` を返し、結果パラメーターには `Jwt` 型の値が含まれます。それ以外の場合、メソッドは `false` を返します。|  
|Jwt|Algorithm: 文字列<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<文字列, 文字列[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: 文字列<br /><br /> Issuer: 文字列<br /><br /> NotBefore: DateTime?<br /><br /> Subject: 文字列<br /><br /> Type: 文字列|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの要求値、またはヘッダーが見つからない場合は `defaultValue` を返します。|

## <a name="next-steps"></a>次のステップ
ポリシーを使用する方法の詳細については、「[Azure API Management のポリシー](api-management-howto-policies.md)」を参照してください。  

