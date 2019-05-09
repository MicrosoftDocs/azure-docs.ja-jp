---
title: Azure Application Gateway で HTTP ヘッダーを書き換える | Microsoft Docs
description: この記事では、Azure Application Gateway で HTTP ヘッダーを書き換える機能の概要を説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544154"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Application Gateway で HTTP ヘッダーを書き換える (パブリック プレビュー)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

クライアントとサーバーは、HTTP ヘッダーを使用して、要求または応答に追加の情報を渡すことができます。 これらの HTTP ヘッダーの書き換えによって、HSTS/X-XSS-Protection などのセキュリティ関連ヘッダー フィールドの追加、機密情報が漏れる可能性がある応答ヘッダー フィールドの削除、X-Forwarded-For ヘッダーからのポート情報の削除など、いくつかの重要なシナリオを実現できます。アプリケーション ゲートウェイでは、要求および応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求ヘッダーと応答ヘッダーを追加、削除、更新する機能がサポートされています。 また、条件を追加して、特定の条件が満たされた場合にのみ特定のヘッダーが書き換えられるようにする機能も提供されます。
> [!NOTE]
>
> HTTP ヘッダーの書き換えは、[新しい SKU の [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) でのみサポートされます

## <a name="headers-supported-for-rewrite"></a>書き換えがサポートされているヘッダー

機能では、Host、Connection、Upgrade ヘッダーを除く、要求と応答のすべてのヘッダーを書き換えることができます。 また、アプリケーション ゲートウェイを使用してカスタム ヘッダーを作成し、ゲートウェイを経由してルーティングされる要求と応答にヘッダーを追加することもできます。 

## <a name="rewrite-conditions"></a>書き換え条件

書き換え条件を使用して、HTTP(S) の要求と応答のコンテンツを評価し、1 つ以上の条件が満たされたときにのみヘッダーの書き換えを実行することができます。 アプリケーション ゲートウェイでは、次の 3 種類の変数を使用して、HTTP(S) の要求と応答のコンテンツが評価されます。

- 要求の HTTP ヘッダー
- 応答の HTTP ヘッダー
- アプリケーション ゲートウェイのサーバー変数

条件を使用して、指定した変数が存在するかどうか、指定した変数が特定の値と完全に一致するかどうか、または指定した変数が特定のパターンと完全に一致するかどうかを、評価することができます。 条件に正規表現のパターン マッチングを実装するには、[Perl Compatible Regular Expressions (PCRE) ライブラリ](https://www.pcre.org/)を使用します。 正規表現の構文については、[Perl 正規表現の man ページ](http://perldoc.perl.org/perlre.html)をご覧ください。

## <a name="rewrite-actions"></a>書き換えアクション

書き換えアクションは、書き換えようとしている要求および応答のヘッダーと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。 新しいヘッダーを作成すること、既存のヘッダーの値を変更すること、または既存のヘッダーを削除することができます。 新しいヘッダーまたは既存のヘッダーの値は、次の種類値に設定できます。

- Text 
- 要求ヘッダー: 要求ヘッダーを指定するには、{http_req_<*ヘッダー名*>} という構文を使用する必要があります
- 応答ヘッダー: 応答ヘッダーを指定するには、{http_resp_<*ヘッダー名*>} という構文を使用する必要があります
- サーバー変数: サーバー変数を指定するには、{var_<*サーバー変数*>} という構文を使用する必要があります
- テキスト、要求ヘッダー、応答ヘッダー、サーバー変数の組み合わせ。

## <a name="server-variables"></a>サーバー変数

アプリケーション ゲートウェイでは、サーバー変数を使用して、サーバー、クライアントとの接続、およびその接続での現在の要求 (クライアントの IP アドレスや Web ブラウザーの種類など) に関する情報が格納されます。 これらの変数は、新しいページが読み込まれるときやフォームが投稿されるときなどに、動的に変更されます。 これらのサーバー変数を使用して、書き換え条件を評価し、ヘッダーを書き換えることができます。 

アプリケーション ゲートウェイでは、次のサーバー変数がサポートされています。

| サポートされているサーバー変数 | 説明                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | "X-Forwarded-For" クライアント要求ヘッダー フィールドと、(IP1、IP2、IP3、...) の形式でそれに追加される `client_ip` 変数 (この表で後ほど説明します) が含まれています。"X-Forwarded-For" フィールドがクライアント要求ヘッダーに存在しない場合、`add_x_forwarded_for_proxy` 変数は `$client_ip` 変数と等しくなります。 この変数は、Application Gateway によって設定された X-Forwarded-For ヘッダーを、IP アドレスのみが含まれてポート情報は含まれないように書き換えるシナリオで、特に役に立ちます。 |
| ciphers_supported          | クライアントでサポートされている暗号の一覧を返します          |
| ciphers_used               | 確立された SSL 接続で使用される暗号の文字列を返します |
| client_ip                  | アプリケーション ゲートウェイが要求を受信したクライアントの IP アドレス。 アプリケーション ゲートウェイと元のクライアントの前にリバース プロキシがある場合、*client_ip* ではリバース プロキシの IP アドレスが返されます。 |
| client_port                | クライアント ポート                                                  |
| client_tcp_rtt             | クライアントの TCP 接続に関する情報。TCP_INFO ソケット オプションをサポートするシステムで使用できます |
| client_user                | HTTP 認証の使用時に、認証のために提供されるユーザー名 |
| host                       | 優先順位は次の通りです: 要求行のホスト名、"Host" 要求ヘッダー フィールドのホスト名、要求に一致するサーバー名 |
| cookie_*name*              | *name* クッキー                                            |
| http_method                | URL 要求を行うために使用されたメソッド。 たとえば、GET、POST などです。 |
| http_status                | セッションの状態。例: 200、400、403 など                       |
| http_version               | 要求のプロトコル。通常は "HTTP/1.0"、"HTTP/1.1"、または "HTTP/2.0" です |
| query_string               | 要求された URL 内で "?" の後にある、変数と値のペアから成る一覧。 |
| received_bytes             | 要求の長さ (要求行、ヘッダー、および要求本文を含む) |
| request_query              | 要求行内の引数                                |
| request_scheme             | 要求スキーム。"http" または "https" です                            |
| request_uri                | 完全な元の要求 URI (引数を含む)                   |
| sent_bytes                 | クライアントに送信されたバイト数                             |
| server_port                | 要求を受け付けたサーバーのポート                 |
| ssl_connection_protocol    | 確立された SSL 接続のプロトコルを返します        |
| ssl_enabled                | 接続が SSL モードで動作している場合は "on"、それ以外の場合は空の文字列 |

## <a name="rewrite-configuration"></a>書き換えの構成

HTTP ヘッダーの書き換えを構成するには、次のようにする必要があります。

1. http ヘッダーの書き換えに必要な新しいオブジェクトを作成します。

   - **書き換えアクション**: 書き換えようとしている要求および要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。 1 つ以上の書き換え条件を書き換えアクションに関連付けるように選択できます。

   - **書き換え条件**: これはオプション構成です。 書き換え条件が追加された場合、HTTP(S) 要求と応答のコンテンツが評価されます。 書き換え条件に関連付けられた書き換えアクションの実行は、HTTP(S) 要求または応答が書き換え条件と一致するかどうかに基づいて決定されます。 

     アクションに複数の条件が関連付けられている場合、そのアクションが実行されるのは、すべての条件が満たされている場合のみです。つまり、論理 AND 演算が実行されます。

   - **書き換えルール**: 書き換えルールには、書き換えアクションと書き換え条件の複数の組み合わせが含まれます。

   - **ルール順序**: さまざまな書き換えルールの実行順序を決定するのに役立ちます。 これは、書き換えセットに複数の書き換えルールがある場合に便利です。 ルール順序の値が小さい書き換えルールから先に実行されます。 2 つの書き換えルールに同じルール順序を指定した場合、実行順序は非決定性となります。

   - **書き換えセット**: 要求ルーティング規則に関連付けられる複数の書き換えルールが含まれます。

2. 書き換えセット (*rewriteRuleSet*) をルーティング規則にアタッチする必要があります。 これは、書き換え構成がルーティング規則によってソース リスナーにアタッチされるためです。 基本ルーティング規則の使用時には、書き換えの構成は、ソース リスナーに関連付けられている、グローバルなヘッダーの書き換えとなります。 パスベースのルーティング規則を使用する場合、ヘッダーの書き換えの構成は、URL パス マップで定義されています。 そのため、サイトの特定のパス領域にのみ適用されます。

http ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="common-scenarios"></a>一般的なシナリオ

以下では、ヘッダーの書き換えを必要とする一般的なシナリオをいくつか説明します。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X-Forwarded-For ヘッダーからポート情報を削除する

アプリケーション ゲートウェイでは、要求をバックエンドに転送する前に、すべての要求に X-Forwarded-For ヘッダーが挿入されます。 このヘッダーの形式は、"IP:ポート" のコンマ区切りリストです。 ただし、シナリオによっては、バックエンド サーバーで IP アドレスのみを含むヘッダーが要求される場合があります。 このようなシナリオを実現するため、ヘッダーの書き換えを使用して X-Forwarded-For ヘッダーからポート情報を削除できます。 これを行う 1 つの方法は、ヘッダーを add_x_forwarded_for_proxy サーバー変数に設定することです。 

![ポートを削除する](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>リダイレクト URL を変更する

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 そのようなシナリオの 1 つは、アプリ サービスがアプリケーション ゲートウェイの背後でホストされていて、クライアントをその相対パスにリダイレクトすることが必要な場合です (contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクト)。 

アプリ サービスはマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントにルーティングします。 アプリ サービスの既定のドメイン名 *.azurewebsites.net (たとえば contoso.azurewebsites.net) は、アプリケーション ゲートウェイのドメイン名 (たとえば contoso.com) とは異なります。 クライアントからの元の要求には、ホスト名としてアプリケーション ゲートウェイのドメイン名 contoso.com が設定されているので、アプリケーション ゲートウェイでホスト名を contoso.azurewebsites.net に変更し、アプリ サービスを適切なエンドポイントにルーティングできるようにします。 アプリ サービスでは、リダイレクト応答を送信するとき、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (contoso.com/path2) を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。 

この問題は、場所ヘッダーのホスト名をアプリケーション ゲートウェイのドメイン名に設定することで解決できます。 これを行うには、パターンとして `(https?):\/\/.*azurewebsites\.net(.*)$` を入力することによって、応答の場所ヘッダーに azurewebsites.net が含まれるかどうかを評価し、ヘッダー値として `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` を入力することによって、場所ヘッダーがアプリケーション ゲートウェイのホスト名になるように書き換えるアクションを実行する条件で、書き換えルールを作成できます。

![場所ヘッダーを変更する](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>脆弱性を防ぐための HTTP セキュリティ ヘッダーを実装する

アプリケーションの応答で必要なヘッダーを実装することにより、いくつかのセキュリティの脆弱性を修正できます。 たとえば、X-XSS-Protection、Strict-Transport-Security、Content-Security-Policy などのセキュリティ ヘッダーです。アプリケーション ゲートウェイを使用して、すべての応答にこれらのヘッダーを設定できます。

![セキュリティ ヘッダー](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>制限事項

- Connection、Upgrade、および Host の各ヘッダーの書き換えはまだサポートされていません。

- ヘッダー名には、任意の英数字と、[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) で定義されている特定の記号を含めることができます。 ただし、現在はヘッダー名内で特殊文字の "アンダー スコア" (\_) がサポートされていません。 

## <a name="need-help"></a>お困りの際は、

この機能についてお困りの際は、[AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) でお問い合わせください。

## <a name="next-steps"></a>次の手順

HTTP ヘッダーを書き換える方法について学習するには、以下をご覧ください。

-  [Azure portal を使用して HTTP ヘッダーを書き換える](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Azure PowerShell を使用して HTTP ヘッダーを書き換える](add-http-header-rewrite-rule-powershell.md)