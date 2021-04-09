---
title: Azure Application Gateway で HTTP ヘッダーを書き換える | Microsoft Docs
description: この記事では、Azure Application Gateway で HTTP ヘッダーの書き換えの概要を説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 7c5b4f0d5d4b153684683963c56b7506e76d963e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575655"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Application Gateway で HTTP ヘッダーを書き換える

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

クライアントとサーバーは、HTTP ヘッダーを使用して、要求または応答に追加の情報を渡すことができます。 これらのヘッダーの書き換えによって、HSTS/X-XSS-Protection などのセキュリティ関連ヘッダー フィールドの追加、機密情報が漏れる可能性のある応答ヘッダー フィールドの削除、X-Forwarded-For ヘッダーからのポート情報の削除など、重要なタスクを実現できます。

Application Gateway を使用することで、要求/応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求および応答ヘッダーを追加、削除、または更新することができます。 また、条件を追加することで、特定の条件が満たされた場合にのみ、指定したヘッダーが確実に書き換えられるようにできます。

Application Gateway は、要求と応答に関する追加情報を格納するための複数の[サーバー変数](#server-variables)もサポートしています。 このため、簡単に強力な書き換え規則を作成できます。

> [!NOTE]
>
> HTTP ヘッダーの書き換えは、[Standard_V2 and WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md) でのみサポートされます。

![ヘッダーを書き換える](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>サポートされているヘッダー

要求と応答では、ホスト、接続、およびアップグレードのヘッダーを除くすべてのヘッダーを書き換えることができます。 また、アプリケーション ゲートウェイを使用してカスタム ヘッダーを作成し、ゲートウェイを経由してルーティングされる要求と応答にヘッダーを追加することもできます。

## <a name="rewrite-conditions"></a>書き換え条件

書き換え条件を使用して、HTTP(S) の要求と応答のコンテンツを評価し、1 つ以上の条件が満たされたときにのみヘッダーの書き換えを実行することができます。 アプリケーション ゲートウェイは、次の 3 種類の変数を使用して、HTTP(S) の要求と応答のコンテンツを評価します。

- 要求の HTTP ヘッダー。
- 応答の HTTP ヘッダー。
- Application Gateway のサーバー変数。

条件を使用して、指定した変数が存在するかどうか、指定した変数が特定の値と一致するかどうか、または指定した変数が特定のパターンと一致するかどうかを、評価することができます。 条件に正規表現のパターン マッチングを設定するには、[Perl Compatible Regular Expressions (PCRE) ライブラリ](https://www.pcre.org/)を使用します。 正規表現の構文については、[Perl 正規表現の man ページ](https://perldoc.perl.org/perlre.html)をご覧ください。

## <a name="rewrite-actions"></a>書き換えアクション

書き換えアクションを使用して、書き換える要求および応答ヘッダーと、ヘッダーの新しい値を指定します。 新しいヘッダーを作成すること、既存のヘッダーの値を変更すること、または既存のヘッダーを削除することができます。 新しいヘッダーまたは既存のヘッダーの値は、次の種類値に設定できます。

- テキスト。
- 要求ヘッダー。 要求ヘッダーを指定するには、{http_req_<*ヘッダー名*>} という構文を使用する必要があります。
- 応答ヘッダー。 応答ヘッダーを指定するには、{http_resp_<*ヘッダー名*>} という構文を使用する必要があります。
- サーバー変数。 サーバー変数を指定するには、{var_<*サーバー変数*>} という構文を使用する必要があります。
- テキスト、要求ヘッダー、応答ヘッダー、サーバー変数の組み合わせ。

## <a name="server-variables"></a>サーバー変数

Application Gateway では、サーバー変数を使用して、サーバー、クライアントとの接続、およびその接続での現在の要求に関する情報が格納されます。 格納される情報には、クライアントの IP アドレスや Web ブラウザーの種類などがあります。 サーバー変数は、たとえば新しいページが読み込まるときや、フォームが投稿されるときに動的に変化します。 これらの変数を使用して、書き換え条件を評価し、ヘッダーを書き換えることができます。 サーバー変数の値を使用してヘッダーを書き換えるには、{var_ *serverVariable*} という構文でこれらの変数を指定する必要があります

アプリケーション ゲートウェイでは、次のサーバー変数がサポートされています。

| 変数名 | 説明                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | X-Forwarded-For クライアント要求ヘッダー フィールドと、IP1、IP2、IP3 などの形式でそれに追加される `client_ip` 変数 (この表で後ほど説明します) が含まれています。 X-Forwarded-For フィールドがクライアント要求ヘッダーにない場合、`add_x_forwarded_for_proxy` 変数は `$client_ip` 変数と等しくなります。 この変数は、Application Gateway によって設定された X-Forwarded-For ヘッダーを書き換えるときに特に有用です。この方法により、ヘッダーにはポート情報は含まれず、IP アドレスのみが含まれるようになります。 |
| ciphers_supported          | クライアントでサポートされている暗号の一覧。          |
| ciphers_used               | 確立された TLS 接続で使用される暗号の文字列。 |
| client_ip                  | アプリケーション ゲートウェイが要求を受信したクライアントの IP アドレス。 アプリケーション ゲートウェイと元のクライアントの前にリバース プロキシがある場合、*client_ip* ではリバース プロキシの IP アドレスが返されます。 |
| client_port                | クライアント ポート。                                                  |
| client_tcp_rtt             | クライアント TCP 接続の情報。 TCP_INFO ソケット オプションをサポートするシステムで使用できます。 |
| client_user                | HTTP 認証の使用時に、認証のために提供されるユーザー名。 |
| host                       | 優先順位は次の通りです: 要求行のホスト名、Host 要求ヘッダー フィールドのホスト名、要求に一致するサーバー名。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、ホストの値は *contoso.com* になります |
| cookie_ *name*              | *name* クッキー。                                            |
| http_method                | URL 要求を行うために使用されたメソッド。 たとえば、GET、POST などです。 |
| http_status                | セッションの状態。 たとえば、200、400、403 です。                       |
| http_version               | 要求プロトコル。 通常、HTTP/1.0、HTTP/1.1、または HTTP/2.0 です。 |
| query_string               | 要求された URL 内で "?" の後にある、変数と値のペアから成る一覧。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、query_string 値は *id=123&title=fabrikam* になります |
| received_bytes             | 要求の長さ (要求行、ヘッダー、および要求本文を含む)。 |
| request_query              | 要求行内の引数。                                |
| request_scheme             | 要求スキーム。http または https です。                            |
| request_uri                | 完全な元の要求 URI (引数を含む)。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、request_uri 値は */article.aspx?id=123&title=fabrikam* になります   |
| sent_bytes                 | クライアントに送信されたバイト数。                             |
| server_port                | 要求を受け付けたサーバーのポート。                 |
| ssl_connection_protocol    | 確立された TLS 接続のプロトコル。        |
| ssl_enabled                | 接続が TLS モードで動作する場合は “オン”。 それ以外の場合は、空の文字列です。 |
| uri_path                   | Web クライアントがアクセスする必要があるホスト内の特定のリソースを識別します。 これは、引数を含まない要求 URI の部分です。 例: 要求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` では、uri_path 値は */article.aspx* になります  |

## <a name="rewrite-configuration"></a>書き換えの構成

HTTP ヘッダーの書き換えを構成するには、次の手順のようにする必要があります。

1. HTTP ヘッダーの書き換えに必要なオブジェクトを作成します。

   - **書き換えアクション**:書き換えようとしている要求フィールドと要求ヘッダー フィールド、およびヘッダーの新しい値を指定するために使用されます。 1 つ以上の書き換え条件を 1 つの書き換えアクションに関連付けることができます。

   - **書き換え条件**:オプションの構成。 書き換え条件では、HTTP(S) の要求と応答の内容が評価されます。 HTTP(S) の要求または応答が書き換え条件に一致する場合、書き換えアクションが発生します。

     複数の条件を 1 つのアクションと関連付けた場合は、すべての条件が満たされた場合にのみアクションが発生します。 つまり、操作は論理 AND 操作です。

   - **書き換え規則**:複数の書き換えアクション/書き換え条件の組み合わせが含まれます。

   - **規則のシーケンス**:書き換え規則の実行順序を決定するのに役立ちます。 この構成は、書き換えセットに複数の書き換え規則がある場合に便利です。 規則のシーケンスの値が小さい書き換え規則から先に実行されます。 2 つの書き換え規則に同じ規則のシーケンスを割り当てた場合、実行順序は非決定論的となります。

   - **書き換えセット**:要求ルーティング規則に関連付けられる複数の書き換え規則が含まれます。

2. 書き換えセット (*rewriteRuleSet*) をルーティング規則にアタッチします。 書き換え構成が、ルーティング規則によってソース リスナーにアタッチされます。 基本ルーティング規則を使うと、ヘッダー書き換え構成はソース リスナーに関連付けられ、グローバルなヘッダーの書き換えになります。 パスベースのルーティング規則を使うと、ヘッダー書き換え構成は URL パス マップで定義されます。 その場合、サイトの特定のパス領域にのみ適用されます。
   > [!NOTE]
   > URL 書き換えではヘッダーが変更されます。パスの URL は変更されません。

HTTP ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="common-scenarios"></a>一般的なシナリオ

ヘッダーの書き換えを使用するための一般的なシナリオを以下に示します。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-Forwarded-For ヘッダーからポート情報を削除する

Application Gateway では、要求をバックエンドに転送する前に、すべての要求に X-Forwarded-For ヘッダーが挿入されます。 このヘッダーは、IP ポート のコンマ区切りリストです。 バック エンド サーバーでヘッダーに IP アドレスが含まれることだけが必要なシナリオがあるとします。 ヘッダーの書き換えを使用して X-Forwarded-For ヘッダーからポート情報を削除できます。 これを行う 1 つの方法は、ヘッダーを add_x_forwarded_for_proxy サーバー変数に設定することです。

![ポートを削除する](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>リダイレクト URL を変更する

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 たとえば、アプリ サービスがアプリケーション ゲートウェイの背後でホストされており、クライアントに相対パスへのリダイレクトを行わせる必要があるときに、これを行えます。 (たとえば、contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトです)。

App Service はマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントに要求をルーティングします。 アプリ サービスの既定のドメイン名 *.azurewebsites.net (たとえば contoso.azurewebsites.net) は、アプリケーション ゲートウェイのドメイン名 (たとえば contoso.com) とは異なります。 クライアントからの元の要求には、ホスト名としてアプリケーション ゲートウェイのドメイン名 contoso.com が設定されているので、アプリケーション ゲートウェイでホスト名を contoso.azurewebsites.net に変更します。 アプリ サービスが適切なエンドポイントに要求をルーティングできるようにこの変更を行います。

アプリ サービスでは、リダイレクト応答を送信するとき、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (contoso.com/path2) を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。

この問題は、場所ヘッダーのホスト名をアプリケーション ゲートウェイのドメイン名に設定することで解決できます。

ホスト名を置換する手順を次に示します。

1. 応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件で書き換え規則を作成します。 パターン「`(https?):\/\/.*azurewebsites\.net(.*)$`」を入力します。
1. アプリケーション ゲートウェイのホスト名を含むように、場所ヘッダーを書き換えるにアクションを実行します。 これを行うには、ヘッダー値として「`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`」を入力します。

![場所ヘッダーを変更する](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>脆弱性を防ぐための HTTP セキュリティ ヘッダーを実装する

アプリケーションの応答で必要なヘッダーを実装することにより、いくつかのセキュリティの脆弱性を修正できます。 たとえば、X-XSS-Protection、Strict-Transport-Security、Content-Security-Policy などのセキュリティ ヘッダーです。 Application Gateway を使用して、すべての応答にこれらのヘッダーを設定できます。

![セキュリティ ヘッダー](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>不要なヘッダーを削除する

HTTP 応答から、機密情報を明らかにするヘッダーを削除することができます。 たとえば、バックエンド サーバー名、オペレーティング システム、ライブラリの詳細などの情報を削除できます。 アプリケーション ゲートウェイを使用して、これらのヘッダーを削除できます。

![ヘッダーを削除する](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>ヘッダーの有無を確認する

HTTP 要求または応答ヘッダーを評価し、ヘッダーまたはサーバー変数の有無を確認できます。 この評価は、特定のヘッダーが存在する場合にのみヘッダーの書き換えを実行する場合に役立ちます。

![ヘッダーの有無を確認する](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>制限事項

- 応答内に同じ名前のヘッダーが複数含まれている場合、これらのヘッダーのいずれかの値を書き換えると、応答内の他のヘッダーが破棄されます。 応答に複数の Set-Cookie ヘッダーを設定できるので、これは通常、Set-Cookie ヘッダーで発生します。 このようなシナリオの 1 つが、アプリ サービスをアプリケーション ゲートウェイとともに使用していて、アプリケーション ゲートウェイで Cookie ベースのセッション アフィニティを構成している場合です。 この場合、応答には 2 つの Set-Cookie ヘッダーが含まれ、1 つはアプリ サービスで使用されるもの (`Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`) で、もう 1 つはアプリケーション ゲートウェイ アフィニティ用 (`Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`) です。 このシナリオでどちらかの Set-Cookie ヘッダーを書き換えると、もう一方の Set-Cookie ヘッダーが応答から削除されることがあります。

- アプリケーション ゲートウェイが要求をリダイレクトするように構成されている場合、またはカスタム エラー ページを表示するように構成されている場合は、書き換えはサポートされません。

- Connection、Upgrade、および Host の各ヘッダーの書き換えは現在サポートされていません。

- ヘッダー名には、任意の英数字と、[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) で定義されている特定の記号を含めることができます。 現在はヘッダー名内で特殊文字のアンダー スコア (\_) がサポートされていません。

## <a name="next-steps"></a>次のステップ

HTTP ヘッダーを書き換える方法について学習するには、以下をご覧ください。

- [Azure portal を使用して HTTP ヘッダーを書き換える](./rewrite-http-headers-portal.md)
- [Azure PowerShell を使用して HTTP ヘッダーを書き換える](add-http-header-rewrite-rule-powershell.md)