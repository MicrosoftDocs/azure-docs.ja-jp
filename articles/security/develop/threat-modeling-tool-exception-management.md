---
title: 例外管理 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool での例外管理について説明します。 軽減策に関する情報とコード例をご覧ください。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517429"
---
# <a name="security-frame-exception-management--mitigations"></a>セキュリティ フレーム:例外管理 | 対応策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- serviceDebug ノードを構成ファイルに含めない](#servicedebug)</li><li>[WCF- serviceMetadata ノードを構成ファイルに含めない](#servicemetadata)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API で適切な例外処理が実行されたことを確認する](#exception)</li></ul> |
| **Web アプリケーション** | <ul><li>[エラー メッセージ内のセキュリティの詳細を公開しない](#messages)</li><li>[既定のエラー処理ページを実装する](#default)</li><li>[IIS の deployment メソッドを retail に設定する](#deployment)</li><li>[例外は安全に失敗する必要がある](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- serviceDebug ノードを構成ファイルに含めない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック、NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **手順** | Windows Communication Framework (WCF) サービスでは、デバッグ情報を公開する構成が可能です。 デバッグ情報は、運用環境では使用しないようにしてください。 `<serviceDebug>` タグにより、WCF サービスでデバッグ情報機能を有効にするかどうかが定義されます。 includeExceptionDetailInFaults 属性が true に設定されている場合、アプリケーションからの例外情報はクライアントに返されます。 攻撃者は、デバッグ出力から入手した追加の情報を使用して、アプリケーションが使用するフレームワーク、データベース、その他のリソースを対象とした攻撃をマウントすることができます。 |

### <a name="example"></a>例
次の構成ファイルには、`<serviceDebug>` タグが含まれています。 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
サービスのデバッグ情報を無効にします。 これは、アプリケーションの構成ファイルから `<serviceDebug>` タグを削除することで実行できます。 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- serviceMetadata ノードを構成ファイルに含めない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | ジェネリック、NET Framework 3 |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **手順** | サービスに関する情報を公開すると、サービスの悪用について攻撃者に多くの洞察を与えてしまう可能性があります。 `<serviceMetadata>` タグにより、メタデータの公開機能が有効化されます。 サービス メタデータには、パブリックにアクセスできないようにする必要がある機密情報が含まれている可能性があります。 少なくとも、信頼されたユーザーにのみメタデータへのアクセスを許可し、不要な情報は公開されないことを確認してください。 メタデータを公開する機能をすべて無効にすると、より安全です。 安全な WCF 構成には `<serviceMetadata>` タグが含まれません。 |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>ASP.NET Web API で適切な例外処理が実行されたことを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC 5、MVC 6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Web API での例外処理](https://www.asp.net/web-api/overview/error-handling/exception-handling)、[ASP.NET Web API でのモデル検証](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **手順** | 既定では、ASP.NET Web API でキャッチされなかったほとんどの例外が、状態コード `500, Internal Server Error` を伴った HTTP 応答に変換されます。|

### <a name="example"></a>例
API によって返されるこれらの状態コードを制御するために、次に示すように `HttpResponseException` を使用することができます。 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>例
例外の応答でさらなる制御を行うために、次に示すように `HttpResponseMessage` クラスを使用することができます。 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
`HttpResponseException` 型ではない、ハンドルされない例外をキャッチするには、例外フィルターを使用できます。 例外フィルターは `System.Web.Http.Filters.IExceptionFilter` インターフェイスを実装します。 例外フィルターを記述する最も簡単な方法は、`System.Web.Http.Filters.ExceptionFilterAttribute` クラスから派生させ、OnException メソッドをオーバーライドすることです。 

### <a name="example"></a>例
`NotImplementedException` 例外を HTTP 状態コード `501, Not Implemented` に変換するフィルターを次に示します。 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Web API 例外フィルターを登録する方法はいくつかあります。
- アクションごと
- コントローラーごと
- グローバル

### <a name="example"></a>例
特定のアクションにフィルターを適用するには、フィルターをアクションに属性として追加します。 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>例
`controller` のすべてのアクションにフィルターを適用するには、フィルターを `controller` クラスに属性として追加します。 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>例
すべての Web API コントローラーにグローバルにフィルターを適用するには、フィルターのインスタンスを `GlobalConfiguration.Configuration.Filters` コレクションに追加します。 このコレクション内の例外フィルターは、任意の Web API コントローラー アクションに適用されます。 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>例
モデルの検証のために、モデルの状態を次のように CreateErrorResponse メソッドに渡すことができます。 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

ASP.NET Web API での例外処理とモデルの検証の詳細については、「参照」セクションのリンクを参照してください。 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>エラー メッセージ内のセキュリティの詳細を公開しない

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>一般的なエラー メッセージは、機密性の高いアプリケーション データを含まずに、ユーザーに直接提供されます。 機密データの例は次のとおりです。</p><ul><li>サーバー名</li><li>Connection strings</li><li>ユーザー名</li><li>パスワード</li><li>SQL プロシージャ</li><li>動的 SQL エラーの詳細</li><li>スタック トレースとコード行</li><li>メモリに格納された変数</li><li>ドライブとフォルダーの場所</li><li>アプリケーションのインストール ポイント</li><li>ホスト構成設定</li><li>その他の内部アプリケーションの詳細</li></ul><p>IIS 内でのカスタム エラーを有効にするだけでなく、アプリケーション内のすべてのエラーをトラップし、一般的なエラー メッセージを提供すると、情報漏えいを防ぐことができます。 SQL Server データベースと .NET の例外処理は、その他のエラー処理アーキテクチャの中でも特に詳細であり、アプリケーションをプロファイリングする悪意のあるユーザーにとっては非常に便利なものです。 .NET 例外クラスから派生するクラスの内容は、直接表示しないでください。また、予期しない例外がユーザーに誤って直接表示されることがないように、適切な例外処理が行われることを確認してください。</p><ul><li>例外またはエラーのメッセージで直接検出される特定の詳細情報を除いた、一般的なエラー メッセージをユーザーに直接提供します。</li><li>.NET 例外クラスの内容をユーザーに直接表示しないでください。</li><li>すべてのエラー メッセージをトラップし、適切な場合はアプリケーション クライアントに送信される一般的なエラー メッセージを使用して、ユーザーに通知します。</li><li>例外クラスの内容、特に `.ToString()` からの戻り値あるいは Message または StackTrace プロパティの値をユーザーに直接公開しないでください。 この情報は安全な方法でログ記録し、ユーザーにはより当たり障りのメッセージを表示します。</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>既定のエラー処理ページを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET エラー ページ設定の編集ダイアログ ボックス](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **手順** | <p>ASP.NET アプリケーションでエラーが生じ、HTTP/1.x 500 内部サーバー エラーが発生した場合、または機能構成 (要求フィルターなど) が原因でページが表示されない場合、エラー メッセージが生成されます。 管理者は、アプリケーションでクライアントにわかりやすいメッセージを表示するか、クライアントに詳細なエラー メッセージを表示するか、localhost のみに詳細なエラー メッセージを表示するかを選択できます。 web.config 内の `<customErrors>` タグには 3 つのモードがあります。</p><ul><li>**On:** カスタム エラーが有効になるように指定します。 defaultRedirect 属性が指定されていない場合、ユーザーには一般的なエラーが表示されます。 リモート クライアントとローカル ホストにカスタム エラーが表示されます。</li><li>**Off:** カスタム エラーが無効になるように指定します。 リモート クライアントとローカル ホストに詳細な ASP.NET エラーが表示されます。</li><li>**RemoteOnly:** カスタム エラーがリモート クライアントにのみ表示されることと、ASP.NET エラーがローカル ホストにのみ表示されることを指定します。 これは、既定値です。</li></ul><p>アプリケーション/サイトの `web.config` ファイルを開き、タグに `<customErrors mode="RemoteOnly" />` または `<customErrors mode="On" />` が定義されていることを確認します。</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>IIS の deployment メソッドを retail に設定する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [deployment 要素 (ASP.NET 設定スキーマ)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **手順** | <p>`<deployment retail>` スイッチは、実稼働 IIS サーバーで使用するためのものです。 このスイッチを使用すると、ページ上にトレース出力を生成するアプリケーション機能、エンド ユーザーに詳細なエラー メッセージを表示する機能、debug スイッチが無効化され、これによりアプリケーションのパフォーマンスを最大限に高め、セキュリティ情報の漏えいの可能性を最小限に抑えながら、アプリケーションの実行が支援されます。</p><p>多くの場合、失敗した要求トレースやデバッグなどの開発者向けスイッチおよびオプションが、開発中に有効化されています。 すべての実稼働サーバーの deployment メソッドを retail に設定することをお勧めします。 machine.config ファイルを開き、`<deployment retail="true" />` が true のままであることを確認します。</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>例外は安全に失敗する必要がある

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [安全な失敗](https://owasp.org/www-community/Fail_securely) |
| **手順** | アプリケーションは安全に失敗する必要があります。 特定の意思決定に基づいてブール値を返すすべてのメソッドでは、例外ブロックを慎重に作成する必要があります。 十分に検討せずに例外ブロックを記述すると、セキュリティの問題が潜在する多くの論理エラーが発生します。|

### <a name="example"></a>例
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
上記のメソッドでは、何らかの例外が発生すると、常に true を返します。 エンド ユーザーが、ブラウザーでは考慮されているが `Uri()` コンストラクターでは考慮されていない正しくない URL を入力した場合、例外が返され、攻撃対象は、有効ではあるものの正しくない URL に誘導されます。