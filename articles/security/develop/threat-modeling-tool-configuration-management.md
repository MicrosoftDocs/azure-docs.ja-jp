---
title: Microsoft Threat Modeling Tool の構成管理
titleSuffix: Azure
description: Threat Modeling Tool の構成管理について説明します。 軽減策に関する情報を参照し、コード例を表示します。
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517486"
---
# <a name="security-frame-configuration-management--mitigations"></a>セキュリティ フレーム:構成管理 | 対応策 
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Web アプリケーション** | <ul><li>[コンテンツ セキュリティ ポリシー (CSP) を実装し、インライン javascript を無効にする](#csp-js)</li><li>[ブラウザーの XSS フィルターを有効にする](#xss-filter)</li><li>[デプロイの前に ASP.NET アプリケーションでトレースおよびデバッグを無効にする](#trace-deploy)</li><li>[信頼できるソースのサード パーティ製 javascript にのみアクセスする](#js-trusted)</li><li>[認証された ASP.NET ページに、UI Redressing (クリックジャッキング) に対する防御が組み込まれていることを確認する](#ui-defenses)</li><li>[ASP.NET Web アプリケーションで CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する](#cors-aspnet)</li><li>[ASP.NET ページで ValidateRequest 属性を有効にする](#validate-aspnet)</li><li>[ローカルでホストされている最新の JavaScript ライブラリを使用する](#local-js)</li><li>[自動 MIME スニッフィングを無効にする](#mime-sniff)</li><li>[Windows Azure Web サイトで標準的なサーバー ヘッダーを削除して、フィンガープリントを残すことを回避する](#standard-finger)</li></ul> |
| **[データベース]** | <ul><li>[データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](#firewall-db)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API で CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する](#cors-api)</li><li>[機密データを含む Web API の構成ファイルのセクションを暗号化する](#config-sensitive)</li></ul> |
| **IoT デバイス** | <ul><li>[強力な資格情報ですべての管理インターフェイスが保護されていることを確認する](#admin-strong)</li><li>[不明なコードがデバイスで実行できないことを確認する](#unknown-exe)</li><li>[BitLocker で OS と IoT デバイスの追加のパーティションを暗号化する](#partition-iot)</li><li>[デバイスで有効になっているのが最小サービス/機能のみであることを確認する](#min-enable)</li></ul> |
| **IoT フィールド ゲートウェイ** | <ul><li>[BitLocker で OS と IoT フィールド ゲートウェイの追加のパーティションを暗号化する](#field-bit-locker)</li><li>[インストール中にフィールド ゲートウェイの既定のログイン資格情報が変更されていることを確認する](#default-change)</li></ul> |
| **IoT クラウド ゲートウェイ** | <ul><li>[接続されているデバイスのファームウェアを最新の状態に保つためのプロセスが、クラウド ゲートウェイで実装されていることを確認する](#cloud-firmware)</li></ul> |
| **コンピューターの信頼の境界** | <ul><li>[デバイスのエンドポイント セキュリティ制御が組織ポリシーに従って構成されていることを確認する](#controls-policies)</li></ul> |
| **Azure ストレージ** | <ul><li>[Azure ストレージ アクセス キーの管理がセキュリティで保護されていることを確認する](#secure-keys)</li><li>[Azure Storage で CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[WCF のサービス調整機能を有効にする](#throttling)</li><li>[WCF - メタデータからの情報の漏えい](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>コンテンツ セキュリティ ポリシー (CSP) を実装し、インライン javascript を無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [コンテンツ セキュリティ ポリシーの概要](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)、[コンテンツ セキュリティ ポリシー リファレンス](https://content-security-policy.com/)、[セキュリティ機能](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/)、[コンテンツ セキュリティ ポリシーの概要](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy)、[CSP が使用可能か](https://caniuse.com/#feat=contentsecuritypolicy) |
| **手順** | <p>コンテンツ セキュリティ ポリシー (CSP) は、W3C 標準の多層防御セキュリティ メカニズムです。このポリシーを使用すると、Web アプリケーション所有者が、自身のサイトに埋め込まれたコンテンツを制御できます。 CSP は、Web サーバー上で HTTP 応答ヘッダーとして追加され、ブラウザーによってクライアント側で適用されます。 これは許可リスト ベースのポリシーで、Web サイトが、信頼されたドメインのセットを宣言できます。このドメインから、JavaScript などのアクティブ コンテンツを読み込むことができます。</p><p>CSP のセキュリティ上の利点を次に示します。</p><ul><li>**XSS に対する保護:** ページが XSS に対して脆弱である場合、攻撃者がそれを悪用する方法は 2 つあります。<ul><li>`<script>malicious code</script>` を挿入する。 このエクスプロイトは、CSP の基本制限 1 により動作しません</li><li>`<script src="http://attacker.com/maliciousCode.js"/>` を挿入する。 攻撃者が制御するドメインは、ドメインの CSP の許可リストに追加されないため、このエクスプロイトは動作しません</li></ul></li><li>**データ流出の制御:** Web ページの悪意のあるコンテンツが外部 Web サイトに接続して、データを盗もうとすると、CSP によって接続が中断されます。 接続先ドメインは、CSP の許可リストに追加されないためです</li><li>**クリックジャッキングに対する防御:** クリックジャッキングは、敵が正規の Web サイトを偽装して、ユーザーに UI 要素を強制的にクリックさせる攻撃方法です。 現時点では、クリックジャッキングに対する防御は、X-Frame-Options 応答ヘッダーを構成することで実現します。 すべてのブラウザーがこのヘッダーを使用しているわけではありません。今後は、CSP がクリックジャッキングに対する標準的な防御方法になります</li><li>**リアルタイム攻撃レポート:** CSP 対応の Web サイトがインジェクション攻撃を受けると、Web サーバーで構成されているエンドポイントへの通知が自動的にトリガーされます。 このように、CSP は、リアルタイムの警告システムとして機能します。</li></ul> |

### <a name="example"></a>例
サンプル ポリシー: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
このポリシーにより、Web アプリケーションのサーバーと Google Analytics サーバーからのみ、スクリプトを読み込むことができます。 その他のサイトから読み込まれたスクリプトは拒否されます。 Web サイトで CSP を有効にすると、XSS 攻撃を軽減するために次の機能が自動的に無効になります。 

### <a name="example"></a>例
インライン スクリプトが実行されません。 インライン スクリプトの例を次に示します 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>例
文字列はコードとして評価されません。 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>ブラウザーの XSS フィルターを有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [XSS 保護フィルター](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **手順** | <p>ブラウザーのクロスサイト スクリプト フィルターは、X-XSS-Protection 応答ヘッダー構成によって制御されます。 この応答ヘッダーには、次の値を設定できます。</p><ul><li>`0:` フィルターを無効にします</li><li>`1: Filter enabled` クロスサイト スクリプティング攻撃が検出された場合、攻撃を阻止するために、ブラウザーはページをサニタイズします</li><li>`1: mode=block : Filter enabled`. クロスサイト スクリプティング (XSS) 攻撃が検出された場合、ブラウザーは、ページをサニタイズするのではなく、レンダリングが行われないようにします</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. ブラウザーはページをサニタイズして、違反をレポートします。</li></ul><p>これは、任意の URI に詳細情報を送信する CSP 違反レポートを使用する Chromium 機能です。 最後の 2 つのオプションは、安全な値と見なされます。</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>デプロイの前に ASP.NET アプリケーションでトレースおよびデバッグを無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET デバッグの概要](/previous-versions/ms227556(v=vs.140))、[ASP.NET トレースの概要](/previous-versions/bb386420(v=vs.140))、[方法:ASP.NET アプリケーションのトレースを有効にする](/previous-versions/0x5wc973(v=vs.140))、[方法:ASP.NET アプリケーションのデバッグを有効にする](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **手順** | ページのトレースが有効になっていると、それを要求しているブラウザーも、内部サーバーの状態とワークフローのデータを含むトレース情報をそれぞれ取得します。 それが機密情報であることがあります。 ページでデバッグが有効になっている場合、サーバーでエラーが発生すると、ブラウザーに完全なスタック トレース データのサーバーが提供されます。 そのデータによって、サーバーのワークフローに関する機密情報が公開される場合があります。 |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>信頼できるソースのサード パーティ製 javascript にのみアクセスする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | サード パーティ製の JavaScript は、信頼できるソースからのみ参照してください。 参照エンドポイントは必ず TLS 上に配置されている必要があります。 |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>認証された ASP.NET ページに、UI Redressing (クリックジャッキング) に対する防御が組み込まれていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [OWASP: クリックジャッキング対策に関するチートシート](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)、[IE Internals - X-Frame-Options によるクリックジャッキングへの対応](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **手順** | <p>クリックジャッキング (UI Redressing 攻撃) では、攻撃者は透明または不透明な複数のレイヤーを使用して、実際のページではなく、その上にある別のページのボタンやリンクをユーザーにクリックさせようとします。</p><p>このレイヤーは iframe で悪意のあるページを作成することで実現し、これにより攻撃対象のページが読み込まれます。 つまり、攻撃者は、攻撃対象ユーザーのページ クリックを "ハイジャック" して、そのユーザーを別のページ (ほとんどの場合、他のアプリケーションまたはドメイン、あるいはその両方が所有するページ) に誘導します。 クリックジャッキング攻撃を防ぐには、他のドメインからのフレーミングを許可しないようブラウザーに指示する、適切な X-Frame-Options HTTP 応答ヘッダーを設定します。</p>|

### <a name="example"></a>例
X-FRAME-OPTIONS ヘッダーを設定するには、IIS web.config を使用します。フレーミングが許可されないサイトの Web.config コード スニペットは次のとおりです。 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>例
同じドメイン内のページによってのみフレーミングされるサイトの Web.config コードは次のとおりです。 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>ASP.NET Web アプリケーションで CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム、MVC5 |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>ブラウザーのセキュリティ機能により、Web ページでは AJAX 要求を別のドメインに送信することはできません。 この制限は、同一オリジン ポリシーと呼ばれ、悪意のあるサイトが、別のサイトから機密データを読み取れないようにします。 ただし、場合によっては、他のサイトが使用できるように、API を安全に公開しなければならないこともあります。 クロス オリジン リソース共有 (CORS) はW3C 標準で、サーバーによる同一オリジン ポリシーの緩和を許可します。 CORS を使用することで、サーバーが一部のクロス オリジン要求を、その他の要求を拒否しながら、明示的に許可することができます。</p><p>CORS は、JSONP など、以前の手法に比べて安全性と柔軟性が向上しています。 基本的に、CORS の有効化とは、いくつかの HTTP 応答ヘッダー (Access-Control-*) を Web アプリケーションに追加することです。これは行う方法は複数あります。</p>|

### <a name="example"></a>例
Web.config にアクセスできる場合、CORS は、次のコードを使用して追加できます。 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>例
Web.config にアクセスできない場合、CORS を構成するには、次の CSharp コードを追加します。 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

重要なのは、"Access-Control-Allow-Origin" 属性のオリジンのリストを、必ず信頼できる有限オリジン セットに設定することです。 この構成が不適切だと (例: "*" に設定する)、悪意のあるサイトが、Web アプリケーションへのクロス オリジン要求を無制限にトリガーできるため、アプリケーションが CSRF 攻撃に対して脆弱になります。 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>ASP.NET ページで ValidateRequest 属性を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム、MVC5 |
| **属性**              | 該当なし  |
| **参照**              | [要求の検証 - スクリプト攻撃の防止](https://www.asp.net/whitepapers/request-validation) |
| **手順** | <p>要求の検証は ASP.NET 1.1 以降の機能で、エンコードされていない HTML を含むコンテンツを、サーバーが受け入れられないようにします。 この機能は、スクリプト インジェクション攻撃、つまり、クライアント スクリプト コードや HTML が知らないうちにサーバーに送信され、格納された後、他のユーザーに提供されるのを防ぐことを目的としています。 ただし、すべての入力データを適宜検証し、HTML エンコードを行うことを強くお勧めします。</p><p>要求の検証は、すべての入力データと、危険性のある値の一覧を比較することによって実行されます。 一致が見つかると、ASP.NET は `HttpRequestValidationException` を生成します。 要求の検証機能は既定で有効になっています。</p>|

### <a name="example"></a>例
ただし、この機能は、ページ レベルで無効にできます。 
```XML
<%@ Page validateRequest="false" %> 
```
アプリケーション レベルでも無効にできます 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
要求の検証機能はサポートされていないことに注意してください。MVC6 パイプラインにも含まれていません。 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>ローカルでホストされている最新の JavaScript ライブラリを使用する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | <p>JQuery などの標準の JavaScript ライブラリを使用している開発者は、セキュリティ上の既知の欠陥がない、一般的な JavaScript ライブラリの承認済みバージョンを使用する必要があります。 最新バージョンのライブラリには、以前のバージョンで見つかった脆弱性に対するセキュリティ更新プログラムが含まれるため、これを使用することをお勧めします。</p><p>互換性の理由により最新リリースを使用できない場合は、以下に示すバージョン以降のライブラリを使用してください。</p><p>許容される最小バージョン:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms および Ajax**<ul><li>ASP.NET Web Forms および Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>パブリック CDN など、外部サイトからは JavaScript ライブラリを読み込まないでください</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>自動 MIME スニッフィングを無効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IE8 のセキュリティ パート V:包括的な保護](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)、[MIME タイプ](https://en.wikipedia.org/wiki/Mime_type) |
| **手順** | X-Content-Type-Options ヘッダーは、コンテンツを MIME スニッフィングしないことを開発者が指定できる HTTP ヘッダーです。 このヘッダーは、MIME スニッフィング攻撃を軽減することを目的としています。 ユーザーが制御可能なコンテンツが含まれている可能性のある各ページで、X-Content-Type-Options:nosniff HTTP ヘッダーを使用する必要があります。 この必須のヘッダーをアプリケーションのすべてのページでグローバルに有効にするには、次のいずれかを実行します|

### <a name="example"></a>例
アプリケーションがインターネット インフォメーション サービス (IIS) 7 以降でホストされている場合は、web.config ファイルにヘッダーを追加します。 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>例
グローバルな Application\_BeginRequest を使用してヘッダーを追加します。 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>例
カスタム HTTP モジュールを実装します。 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>例
この必須のヘッダーを特定のページでのみ有効にするには、ヘッダーを個々の応答に追加します。 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Windows Azure Web サイトで標準的なサーバー ヘッダーを削除して、フィンガープリントを残すことを回避する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | EnvironmentType - Azure |
| **参照**              | [Windows Azure Web サイトでの標準的なサーバー ヘッダーの削除](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **手順** | Server、X-Powered-By、X-AspNet-Version などのヘッダーによって、サーバーおよび基盤のテクノロジに関する情報が公開されます。 アプリケーションにフィンガープリントが残らないように、こうしたヘッダーは非表示にすることをお勧めします。 |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>データベース エンジンにアクセスできるように Windows ファイアウォールを構成する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | データベース | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | SQL Azure、OnPrem |
| **属性**              | 該当なし、SQL バージョン - V12 |
| **参照**              | [Azure SQL Database ファイアウォールを構成する方法](../../azure-sql/database/firewall-configure.md)、[データベース エンジンにアクセスできるように Windows ファイアウォールを構成する](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **手順** | ファイアウォール システムは、コンピューター リソースへの不正アクセスを防ぐのに役立ちます。 ファイアウォールを経由して SQL Server データベース エンジンのインスタンスにアクセスするには、SQL Server を実行しているコンピューターで、アクセスを許可するようにファイアウォールを構成する必要があります |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>ASP.NET Web API で CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC 5 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Web API 2 でのクロス オリジン要求を有効にする](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)、[ASP.NET Web API - ASP.NET Web API 2 における CORS サポート](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **手順** | <p>ブラウザーのセキュリティ機能により、Web ページでは AJAX 要求を別のドメインに送信することはできません。 この制限は、同一オリジン ポリシーと呼ばれ、悪意のあるサイトが、別のサイトから機密データを読み取れないようにします。 ただし、場合によっては、他のサイトが使用できるように、API を安全に公開しなければならないこともあります。 クロス オリジン リソース共有 (CORS) はW3C 標準で、サーバーによる同一オリジン ポリシーの緩和を許可します。</p><p>CORS を使用することで、サーバーが一部のクロス オリジン要求を、その他の要求を拒否しながら、明示的に許可することができます。 CORS は、JSONP など、以前の手法に比べて安全性と柔軟性が向上しています。</p>|

### <a name="example"></a>例
App_Start/WebApiConfig.cs で、次のコードを WebApiConfig.Register メソッドに追加します 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>例
EnableCors 属性は、次のように、コント ローラーのアクション メソッドに適用できます。 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

重要なのは、"EnableCors" 属性のオリジンのリストを、必ず信頼できる有限オリジン セットに設定することです。 この構成が不適切だと (例: "*" に設定する)、悪意のあるサイトが、API へのクロス オリジン要求を無制限にトリガーできるため、API が CSRF 攻撃に対して脆弱になります。 EnableCors は、コントローラー レベルで修飾できます。 

### <a name="example"></a>例
クラスの特定のメソッドで CORS を無効にするには、次に示すように DisableCors 属性を使用できます。 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC 6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Core 1.0 でのクロス オリジン要求 (CORS) の有効化](https://docs.asp.net/en/latest/security/cors.html) |
| **手順** | <p>ASP.NET Core 1.0 では、ミドルウェアまたは MVC を使用して CORS を有効にできます。 MVC を使用して CORS を有効にすると、同じ CORS サービスが使用されますが、CORS ミドルウェアは使用されません。</p>|

**方法 1** ミドルウェアで CORS を有効化:アプリケーション全体で CORS を有効にするには、UseCors 拡張メソッドを使用して、CORS ミドルウェアを要求パイプラインに追加します。 クロス オリジン ポリシーを指定するには、CORS ミドルウェアを追加するときに CorsPolicyBuilder クラスを使用します。 この作業を実行する 2 つの方法があります。

### <a name="example"></a>例
1 つ目は、ラムダで UseCors を呼び出す方法です。 ラムダは CorsPolicyBuilder オブジェクトを受け取ります。 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>例
2 つ目は、1 つ以上の名前付き CORS ポリシーを定義し、実行時に名前によってポリシーを選択する方法です。 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**方法 2** MVC で CORS を有効化:開発者は MVC を使用して、特定の CORS を、アクションごと、コントローラーごと、またはすべてのコントローラーに対してグローバルに適用することもできます。

### <a name="example"></a>例
アクションごと:特定のアクションに対して CORS ポリシーを指定するには、EnableCors 属性をそのアクションに追加します。 ポリシー名を入力してください。 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>例
コントローラーごと: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>例
グローバル: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
重要なのは、"EnableCors" 属性のオリジンのリストを、必ず信頼できる有限オリジン セットに設定することです。 この構成が不適切だと (例: "*" に設定する)、悪意のあるサイトが、API へのクロス オリジン要求を無制限にトリガーできるため、API が CSRF 攻撃に対して脆弱になります。 

### <a name="example"></a>例
コント ローラーまたはアクションに対して CORS を無効にするには、DisableCors 属性を使用します。 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>機密データを含む Web API の構成ファイルのセクションを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [方法: DPAPI を使用して ASP.NET 2.0 内の構成セクションを暗号化する方法](/previous-versions/msp-n-p/ff647398(v=pandp.10))、[保護された構成プロバイダーの指定](/previous-versions/68ze1hb2(v=vs.140))、[Azure Key Vault を使用してアプリケーション シークレットを保護する](/azure/architecture/multitenant-identity/web-api) |
| **手順** | Web.config、appsettings.json などの構成ファイルは、一般的に、ユーザー名、パスワード、データベース接続文字列、暗号化キーなどの機密情報の保持に使用されます。 この情報を保護しないと、アプリケーションは、アカウントのユーザー名とパスワード、データベース名、サーバー名などの機密情報を取得する、攻撃者や悪意のあるユーザーに対して脆弱になります。 構成ファイルの重要なセクションは、デプロイメント タイプ (azure/on-prem) に基づいて、DPAPI または Azure Key Vault などのサービスを使用して暗号化してください。 |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>強力な資格情報ですべての管理インターフェイスが保護されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | デバイスまたはフィールド ゲートウェイによって公開される管理インターフェイスすべてを、強力な資格情報を使用して保護する必要があります。 また、WiFi、SSH、ファイル共有、FTP など、他の公開インターフェイスも強力な資格情報で保護します。 既定の脆弱なパスワードは使用しないでください。 |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>不明なコードがデバイスで実行できないことを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Windows 10 IoT Core でのセキュア ブートと BitLocker デバイス暗号化の有効化](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **手順** | UEFI セキュア ブートでは、指定された機関によって署名されたバイナリの実行のみを許可するようにシステムを制限します。 この機能により、不明なコードがプラットフォームで実行できなくなり、コードによって、プラットフォームのセキュリティが低下する可能性を排除できます。 UEFI セキュア ブートを有効にして、コードに署名する信頼済み証明機関の一覧を制限し、 信頼された機関のいずれかを使用して、デバイスにデプロイされているすべてのコードに署名します。 |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>BitLocker で OS と IoT デバイスの追加のパーティションを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | Windows 10 IoT Core は、BitLocker デバイス暗号化の軽量バージョンを実装しており、必要な測定を行う UEFI の必要な preOS プロトコルを含む、プラットフォーム上の TPM の存在に強く依存します。 この preOS 測定により、以降の OS に、OS が起動された方法に関する明確なレコードが確実に保持されます。BitLocker を使用して OS のパーティションだけでなく、追加のパーティションも暗号化してください。このパーティションにも、機密データが格納されていることがあります。 |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>デバイスで有効になっているのが最小サービス/機能のみであることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ソリューションに不要な機能やサービスは OS で有効にしないでください。つまりオフにします。 たとえば、デバイスで UI をデプロイする必要がない場合は、Windows IoT Core をヘッドレス モードでインストールします。 |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>BitLocker で OS と IoT フィールド ゲートウェイの追加のパーティションを暗号化する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | Windows 10 IoT Core は、BitLocker デバイス暗号化の軽量バージョンを実装しており、必要な測定を行う UEFI の必要な preOS プロトコルを含む、プラットフォーム上の TPM の存在に強く依存します。 この preOS 測定により、以降の OS に、OS が起動された方法に関する明確なレコードが確実に保持されます。BitLocker を使用して OS のパーティションだけでなく、追加のパーティションも暗号化してください。このパーティションにも、機密データが格納されていることがあります。 |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>インストール中にフィールド ゲートウェイの既定のログイン資格情報が変更されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT フィールド ゲートウェイ | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | インストール中にフィールド ゲートウェイの既定のログイン資格情報が変更されていることを確認します |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>接続されているデバイスのファームウェアを最新の状態に保つためのプロセスが、クラウド ゲートウェイで実装されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT クラウド ゲートウェイ | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | ゲートウェイの選択 - Azure IoT Hub |
| **参照**              | [IoT Hub を使用したデバイス管理の概要](../../iot-hub/iot-hub-device-management-overview.md)、[デバイス ファームウェアの更新方法](../../iot-hub/tutorial-firmware-update.md) |
| **手順** | LWM2M は、IoT デバイス管理の Open Mobile Alliance のプロトコルです。 Azure の IoT デバイス管理では、デバイスのジョブを使用して物理デバイスと対話することができます。 Azure IoT Hub デバイス管理を使用して、デバイスおよびその他の構成データを定期的に最新の状態に保つためのプロセスが、クラウド ゲートウェイで実装されていることを確認します。 |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>デバイスのエンドポイント セキュリティ制御が組織ポリシーに従って構成されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | コンピューターの信頼の境界 | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ディスク レベルの暗号化用の BitLocker、更新された署名を備えたウイルス対策、ホスト ベースのファイアウォール、OS アップグレード、グループ ポリシーなど、デバイスのエンドポイント セキュリティ制御が組織のセキュリティ ポリシーに従って構成されていることを確認します。 |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Azure ストレージ アクセス キーの管理がセキュリティで保護されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | デプロイ |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Storage セキュリティ ガイド - ストレージ アカウント キーの管理](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **手順** | <p>キー記憶域:Azure Storage アクセス キーはシークレットとして Azure Key Vault に格納し、アプリケーションで Key Vault からキーを取得することをお勧めします。 その理由は次のとおりです。</p><ul><li>アプリケーションではストレージ キーが構成ファイルでハードコーディングされないため、特定のアクセス許可を持たない誰かがキーへのアクセス権を取得する抜け道がなくなります</li><li>Azure Active Directory を使用して、キーへのアクセスを制御できます。 つまり、アカウント所有者が、Azure Key Vault からキーを取得する必要があるいくつかのアプリケーションに、アクセス権を付与できます。 アクセス許可が付与されていないアプリケーションが、キーにアクセスすることはできません</li><li>キーの再生成:セキュリティ上の理由から、Azure ストレージ アクセス キーを再生成するプロセスを設定することをお勧めします。 キーの再生成を計画する理由と方法の詳細については、Azure Storage セキュリティ ガイドのリファレンス記事を参照してください</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Azure Storage で CORS が有効になっている場合、信頼されたオリジンのみが許可されていることを確認する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Storage | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [Azure Storage サービスの CORS のサポート](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **手順** | Azure Storage では、CORS (クロス オリジン リソース共有) を有効にできます。 ストレージ アカウントごとに、そのストレージ アカウントのリソースにアクセスできるドメインを指定できます。 既定では、すべてのサービスで CORS が無効です。 CORS を有効にするには、REST API またはストレージ クライアント ライブラリを使用して、サービス ポリシーを設定するいずれかのメソッドを呼び出します。 |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>WCF のサービス調整機能を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com) |
| **手順** | <p>システム リソースの使用に制限を設定しないと、リソースが使い果たされ、最終的にはサービス拒否攻撃につながる可能性があります。</p><ul><li>**説明:** Windows Communication Foundation (WCF) は、サービス要求をスロットルする機能を提供します。 許可するクライアント要求が多すぎると、システムに要求が殺到し、リソースが使い果たされる可能性があります。 一方、サービスへの要求を許可する数が少なすぎると、正当なユーザーがサービスを使用できなくなる場合があります。 各サービスを個別に調整および構成して、適切な量のリソースを許可する必要があります。</li><li>**推奨** WCF のサービス調整機能を有効にして、アプリケーションに適した制限を設定してください。</li></ul>|

### <a name="example"></a>例
調整が有効になっている構成の例は次のとおりです。
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF - メタデータからの情報の漏えい

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | WCF | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | .NET Framework 3 |
| **属性**              | 該当なし  |
| **参照**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[Fortify Kingdom](https://vulncat.fortify.com) |
| **手順** | 攻撃者がメタデータからシステム情報を入手し、その情報を攻撃方法を計画する際に悪用することがありますが、 WCF サービスが、メタデータを公開するように構成されている場合があります。 メタデータには詳細なサービス情報が含まれているため、運用環境ではブロードキャストしないでください。 ServiceMetaData クラスの `HttpGetEnabled` / `HttpsGetEnabled` プロパティは、サービスでメタデータを公開するかどうかを定義します | 

### <a name="example"></a>例
次のコードは、サービスのメタデータをブロードキャストするよう WCF に指示しています
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
運用環境ではサービス メタデータをブロードキャストしないでください。 ServiceMetaData クラスの HttpGetEnabled/HttpsGetEnabled プロパティを false に設定します。 

### <a name="example"></a>例
次のコードは、サービスのメタデータをブロードキャストしないよう WCF に指示しています。 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```