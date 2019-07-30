---
title: Azure API Management ポリシー式 | Microsoft Docs
description: Azure API Management 内のポリシー式について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 22be5509a93d0713b8113ba17debfda3cf576006
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508962"
---
# <a name="api-management-policy-expressions"></a>API Management ポリシー式
この記事で説明するポリシー式の構文は C# 7 です。 それぞれの式は、暗黙的に指定された[コンテキスト](api-management-policy-expressions.md#ContextVariables)変数と、許可されている .NET Framework の型の[サブセット](api-management-policy-expressions.md#CLRTypes)にアクセスできます。

詳細:

- バックエンド サービスにコンテキスト情報を指定する方法をご覧ください。 この情報を指定するには、[クエリ文字列パラメーターの設定](api-management-transformation-policies.md#SetQueryStringParameter)ポリシーおよび [HTTP ヘッダーの設定](api-management-transformation-policies.md#SetHTTPheader)ポリシーを使用します。
- [JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、トークン クレームに基づいて操作へのアクセスを事前に承認する方法を示します。
- [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) トレースによってポリシーの評価方法と評価結果を確認する方法を示します。
- [キャッシュから取得](api-management-caching-policies.md#GetFromCache)ポリシーおよび[キャッシュに格納](api-management-caching-policies.md#StoreToCache)ポリシーの式を使用して、API Management 応答のキャッシュを構成する方法をご覧ください。 バックエンド サービスの `Cache-Control` ディレクティブによって指定されたバックエンド サービスの応答キャッシュ時間と一致するように設定しています。
- コンテンツのフィルター処理を実行する方法をご覧ください。 [制御フロー](api-management-advanced-policies.md#choose) ポリシーおよび[本文設定](api-management-transformation-policies.md#SetBody)ポリシーを使用して、バックエンドから受信した応答からデータ要素を削除しています。
- ポリシー ステートメントをダウンロードするには、[api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub リポジトリをご覧ください。


## <a name="Syntax"></a>構文
単一ステートメントの式は `@(expression)` の形式で囲みます。`expression` は正しい C# 式ステートメントです。

複数ステートメントの式は `@{expression}` の形式で囲みます。 複数ステートメントの式内のすべてのコード パスは `return` ステートメントで終了している必要があります。

## <a name="PolicyExpressionsExamples"></a>例

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

## <a name="PolicyExpressionsUsage"></a>使用状況
式は (ポリシー参照で特に指定されていない限り)、任意の API Management [ポリシー](api-management-policies.md)で属性値またはテキスト値として使用できます。

> [!IMPORTANT]
> ポリシー式を使用する場合、ポリシーを定義したときのポリシー式の検証は限られています。 式は、実行時にゲートウェイによって実行されます。ポリシー式によって生成されたすべての例外はランタイム エラーになります。

## ポリシー式で使用できる <a name="CLRTypes"></a>.NET framework の型
次の表は、ポリシー式で使用できる .NET Framework の型とメンバーの一覧です。

|Type|サポートされているメンバー|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|All|
|Newtonsoft.Json.JsonConvert|SerializeObject、DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|All|
|Newtonsoft.Json.Linq.JArray|All|
|Newtonsoft.Json.Linq.JConstructor|All|
|Newtonsoft.Json.Linq.JContainer|All|
|Newtonsoft.Json.Linq.JObject|All|
|Newtonsoft.Json.Linq.JProperty|All|
|Newtonsoft.Json.Linq.JRaw|All|
|Newtonsoft.Json.Linq.JToken|All|
|Newtonsoft.Json.Linq.JTokenType|All|
|Newtonsoft.Json.Linq.JValue|All|
|System.Array|All|
|System.BitConverter|All|
|System.Boolean|All|
|System.Byte|All|
|System.Char|All|
|System.Collections.Generic.Dictionary<TKey, TValue>|All|
|System.Collections.Generic.HashSet\<T>|All|
|System.Collections.Generic.ICollection\<T>|All|
|System.Collections.Generic.IDictionary<TKey, TValue>|All|
|System.Collections.Generic.IEnumerable\<T>|All|
|System.Collections.Generic.IEnumerator\<T>|All|
|System.Collections.Generic.IList\<T>|All|
|System.Collections.Generic.IReadOnlyCollection\<T>|All|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|All|
|System.Collections.Generic.ISet\<T>|All|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|All|
|System.Collections.Generic.List\<T>|All|
|System.Collections.Generic.Queue\<T>|All|
|System.Collections.Generic.Stack\<T>|All|
|System.Convert|All|
|System.DateTime|(コンストラクター)、Add、AddDays、AddHours、AddMilliseconds、AddMinutes、AddMonths、AddSeconds、AddTicks、AddYears、Date、Day、DayOfWeek、DayOfYear、DaysInMonth、Hour、IsDaylightSavingTime、IsLeapYear、MaxValue、Millisecond、Minute、MinValue、Month、Now、Parse、Second、Subtract、Ticks、TimeOfDay、Today、ToString、UtcNow、Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|All|
|System.Decimal|All|
|System.Double|All|
|System.Exception|All|
|System.Guid|All|
|System.Int16|All|
|System.Int32|All|
|System.Int64|All|
|System.IO.StringReader|All|
|System.IO.StringWriter|All|
|System.Linq.Enumerable|All|
|System.Math|All|
|System.MidpointRounding|All|
|System.Net.WebUtility|All|
|System.Nullable|All|
|System.Random|All|
|System.SByte|All|
|System.Security.Cryptography.AsymmetricAlgorithm|All|
|System.Security.Cryptography.CipherMode|All|
|System.Security.Cryptography.HashAlgorithm|All|
|System.Security.Cryptography.HashAlgorithmName|All|
|System.Security.Cryptography.HMAC|All|
|System.Security.Cryptography.HMACMD5|All|
|System.Security.Cryptography.HMACSHA1|All|
|System.Security.Cryptography.HMACSHA256|All|
|System.Security.Cryptography.HMACSHA384|All|
|System.Security.Cryptography.HMACSHA512|All|
|System.Security.Cryptography.KeyedHashAlgorithm|All|
|System.Security.Cryptography.MD5|All|
|System.Security.Cryptography.Oid|All|
|System.Security.Cryptography.PaddingMode|All|
|System.Security.Cryptography.RNGCryptoServiceProvider|All|
|System.Security.Cryptography.RSA|All|
|System.Security.Cryptography.RSAEncryptionPadding|All|
|System.Security.Cryptography.RSASignaturePadding|All|
|System.Security.Cryptography.SHA1|All|
|System.Security.Cryptography.SHA1Managed|All|
|System.Security.Cryptography.SHA256|All|
|System.Security.Cryptography.SHA256Managed|All|
|System.Security.Cryptography.SHA384|All|
|System.Security.Cryptography.SHA384Managed|All|
|System.Security.Cryptography.SHA512|All|
|System.Security.Cryptography.SHA512Managed|All|
|System.Security.Cryptography.SymmetricAlgorithm|All|
|System.Security.Cryptography.X509Certificates.PublicKey|All|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|All|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificates.X509Certificate|All|
|System.Security.Cryptography.X509Certificates.X509Certificate2|All|
|System.Security.Cryptography.X509Certificates.X509ContentType|All|
|System.Security.Cryptography.X509Certificates.X509NameType|All|
|System.Single|All|
|System.String|All|
|System.StringComparer|All|
|System.StringComparison|All|
|System.StringSplitOptions|All|
|System.Text.Encoding|All|
|System.Text.RegularExpressions.Capture|Index、Length、Value|
|System.Text.RegularExpressions.CaptureCollection|Count、Item|
|System.Text.RegularExpressions.Group|Captures、Success|
|System.Text.RegularExpressions.GroupCollection|Count、Item|
|System.Text.RegularExpressions.Match|Empty、Groups、Result|
|System.Text.RegularExpressions.Regex|(コンストラクター)、IsMatch、Match、Matches、Replace、Unescape、Split|
|System.Text.RegularExpressions.RegexOptions|All|
|System.Text.StringBuilder|All|
|System.TimeSpan|All|
|System.TimeZone|All|
|System.TimeZoneInfo.AdjustmentRule|All|
|System.TimeZoneInfo.TransitionTime|All|
|System.TimeZoneInfo|All|
|System.Tuple|All|
|System.UInt16|All|
|System.UInt32|All|
|System.UInt64|All|
|System.Uri|All|
|System.UriPartial|All|
|System.Xml.Linq.Extensions|All|
|System.Xml.Linq.XAttribute|All|
|System.Xml.Linq.XCData|All|
|System.Xml.Linq.XComment|All|
|System.Xml.Linq.XContainer|All|
|System.Xml.Linq.XDeclaration|All|
|System.Xml.Linq.XDocument|All、例外: Load|
|System.Xml.Linq.XDocumentType|All|
|System.Xml.Linq.XElement|All|
|System.Xml.Linq.XName|All|
|System.Xml.Linq.XNamespace|All|
|System.Xml.Linq.XNode|All|
|System.Xml.Linq.XNodeDocumentOrderComparer|All|
|System.Xml.Linq.XNodeEqualityComparer|All|
|System.Xml.Linq.XObject|All|
|System.Xml.Linq.XProcessingInstruction|All|
|System.Xml.Linq.XText|All|
|System.Xml.XmlNodeType|All|

## <a name="ContextVariables"></a>コンテキスト変数
`context` という名前の変数は、暗黙的にすべてのポリシー[式](api-management-policy-expressions.md#Syntax)で使用できます。 そのメンバーは `\request`に関連する情報を提供します。 すべての `context` メンバーは読み取り専用です。

|コンテキスト変数|使用可能なメソッド、プロパティ、パラメーターの値|
|----------------------|-------------------------------------------------------|
|context|[Api](#ref-context-api):[IApi](#ref-iapi)<br /><br /> [Deployment](#ref-context-deployment)<br /><br /> Elapsed:TimeSpan - Timestamp の値と現在時刻の間の時間間隔<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [操作](#ref-context-operation)<br /><br /> [成果物](#ref-context-product)<br /><br /> [要求](#ref-context-request)<br /><br /> RequestId:Guid - 一意の要求識別子<br /><br /> [応答](#ref-context-response)<br /><br /> [サブスクリプション](#ref-context-subscription)<br /><br /> タイムスタンプ:DateTime - 要求を受信した時点<br /><br /> Tracing: bool - トレースがオンかオフかを示します <br /><br /> [User](#ref-context-user)<br /><br /> [変数](#ref-context-variables): IReadOnlyDictionary<string, object><br /><br /> void Trace(message: 文字列)|
|<a id="ref-context-api"></a>context.Api|Id: 文字列<br /><br /> IsCurrentRevision: ブール値<br /><br />  Name: 文字列<br /><br /> Path: 文字列<br /><br /> Revision: 文字列<br /><br /> ServiceUrl:[IUrl](#ref-iurl)<br /><br /> Version: 文字列 |
|<a id="ref-context-deployment"></a>context.Deployment|Region: 文字列<br /><br /> ServiceName: 文字列<br /><br /> Certificates:IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: 文字列<br /><br /> Reason: 文字列<br /><br /> Message: 文字列<br /><br /> Scope: 文字列<br /><br /> Section: 文字列<br /><br /> Path: 文字列<br /><br /> PolicyId: 文字列<br /><br /> context.LastError の詳細については、[エラー処理](api-management-error-handling-policies.md)に関する記事を参照してください。|
|<a id="ref-context-operation"></a>context.Operation|Id: 文字列<br /><br /> Method: 文字列<br /><br /> Name: 文字列<br /><br /> UrlTemplate: 文字列|
|<a id="ref-context-product"></a>context.Product|Apis:IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: ブール値<br /><br /> Groups:IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: 文字列<br /><br /> Name: 文字列<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: ブール値|
|<a id="ref-context-request"></a>context.Request|本文は次のようになります。[IMessageBody](#ref-imessagebody) または `null` (要求に本文がない場合)。<br /><br /> Certificate:System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Headers](#ref-context-request-headers):IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: 文字列<br /><br /> MatchedParameters:IReadOnlyDictionary<string, string><br /><br /> Method: 文字列<br /><br /> OriginalUrl:[IUrl](#ref-iurl)<br /><br /> Url:[IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの要求ヘッダー値、またはヘッダーが見つからない場合は `defaultValue` を返します。|
|<a id="ref-context-response"></a>context.Response|本文は次のようになります。[IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers):IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: 整数<br /><br /> StatusReason: 文字列|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの応答ヘッダー値、またはヘッダーが見つからない場合は `defaultValue` を返します。|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime:DateTime<br /><br /> EndDate:DateTime?<br /><br /> Id: 文字列<br /><br /> Key: 文字列<br /><br /> Name: 文字列<br /><br /> PrimaryKey: 文字列<br /><br /> SecondaryKey: 文字列<br /><br /> StartDate:DateTime?|
|<a id="ref-context-user"></a>context.User|Email: 文字列<br /><br /> FirstName: 文字列<br /><br /> Groups:IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: 文字列<br /><br /> Identities:IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: 文字列<br /><br /> Note: 文字列<br /><br /> RegistrationDate:DateTime|
|<a id="ref-iapi"></a>IApi|Id: 文字列<br /><br /> Name: 文字列<br /><br /> Path: 文字列<br /><br /> Protocols:IEnumerable<string\><br /><br /> ServiceUrl:[IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames:[ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: 文字列<br /><br /> Name: 文字列|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false):T の値: string、JObject、JToken、JArray、XNode、XElement、XDocument<br /><br /> `context.Request.Body.As<T>` メソッドと `context.Response.Body.As<T>` メソッドは、指定した型 `T` で要求および応答のメッセージ本文を読み取るのに使用します。 メソッドでは既定で元のメッセージ本文のストリームが使用され、制御が戻ると使用不可になります。 本文ストリームのコピーでメソッドを実行することでこれを回避するには、`preserveContent` パラメーターを `true` に設定します。 例については、[こちら](api-management-transformation-policies.md#SetBody)を参照してください。|
|<a id="ref-iurl"></a>IUrl|Host: 文字列<br /><br /> Path: 文字列<br /><br /> Port: 整数<br /><br /> [Query](#ref-iurl-query): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: 文字列<br /><br /> Scheme: 文字列|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: 文字列<br /><br /> Provider: 文字列|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: 文字列<br /><br /> Query: 文字列|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りのクエリ パラメーターの値、またはパラメーターが見つからない場合は `defaultValue` を返します。|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue:T)|variableName: 文字列<br /><br /> defaultValue:T<br /><br /> `T` 型へのキャスト変数値、または変数が見つからない場合は `defaultValue` を返します。<br /><br /> このメソッドは、指定した型が返される変数の実際の型と一致しない場合に、例外をスローします。|
|BasicAuthCredentials AsBasic(input: this string)|input: 文字列<br /><br /> 入力パラメーターに有効な HTTP 基本認証の承認要求ヘッダー値が含まれている場合、メソッドは `BasicAuthCredentials` 型のオブジェクトを返します。それ以外の場合、メソッドは null を返します。|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: 文字列<br /><br /> result: out BasicAuthCredentials<br /><br /> 入力パラメーターに要求ヘッダーの有効な HTTP 基本認証の承認値が含まれている場合、メソッドは `true` を返し、結果パラメーターには `BasicAuthCredentials` 型の値が含まれます。それ以外の場合、メソッドは `false` を返します。|
|BasicAuthCredentials|Password: 文字列<br /><br /> UserId: 文字列|
|Jwt AsJwt(input: this string)|input: 文字列<br /><br /> 入力パラメーターに有効な JWT トークン値が含まれている場合、メソッドは `Jwt` 型のオブジェクトを返します。それ以外の場合、メソッドは `null` を返します。|
|bool TryParseJwt(input: this string, result: out Jwt)|input: 文字列<br /><br /> result: out Jwt<br /><br /> 入力パラメーターに有効な JWT トークン値が含まれている場合、メソッドは `true` を返し、結果パラメーターには `Jwt` 型の値が含まれます。それ以外の場合、メソッドは `false` を返します。|
|Jwt|Algorithm: 文字列<br /><br /> Audience:IEnumerable<string\><br /><br /> Claims:IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime:DateTime?<br /><br /> Id: 文字列<br /><br /> Issuer: 文字列<br /><br /> IssuedAt:DateTime?<br /><br /> NotBefore:DateTime?<br /><br /> Subject: 文字列<br /><br /> Type: 文字列|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: 文字列<br /><br /> defaultValue: 文字列<br /><br /> コンマ区切りの要求値、またはヘッダーが見つからない場合は `defaultValue` を返します。|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 暗号化対象のプレーンテキスト<br /><br />alg - 対称暗号化アルゴリズムの名前<br /><br />key - 暗号化キー<br /><br />iv - 初期化ベクター<br /><br />暗号化されたプレーンテキストを返します。|
|byte[] Encrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm)|input - 暗号化対象のプレーンテキスト<br /><br />alg - 暗号化アルゴリズム<br /><br />暗号化されたプレーンテキストを返します。|
|byte[] Encrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 暗号化対象のプレーンテキスト<br /><br />alg - 暗号化アルゴリズム<br /><br />key - 暗号化キー<br /><br />iv - 初期化ベクター<br /><br />暗号化されたプレーンテキストを返します。|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 暗号化解除対象の暗号化テキスト<br /><br />alg - 対称暗号化アルゴリズムの名前<br /><br />key - 暗号化キー<br /><br />iv - 初期化ベクター<br /><br />プレーンテキストを返します。|
|byte[] Decrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm)|input - 暗号化解除対象の暗号化テキスト<br /><br />alg - 暗号化アルゴリズム<br /><br />プレーンテキストを返します。|
|byte[] Decrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 暗号化解除対象の暗号化テキスト<br /><br />alg - 暗号化アルゴリズム<br /><br />key - 暗号化キー<br /><br />iv - 初期化ベクター<br /><br />プレーンテキストを返します。|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|証明書の失効状態を確認しないで、X.509 チェーン検証を実行します。<br /><br />input - 証明書オブジェクト<br /><br />検証に成功したら `true`、検証に失敗したら `false` を返します。|


## <a name="next-steps"></a>次の手順

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](api-management-policy-reference.md)
+ [ポリシーのサンプル](policy-samples.md)
