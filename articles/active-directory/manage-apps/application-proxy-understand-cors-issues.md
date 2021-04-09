---
title: Azure AD アプリケーション プロキシの CORS の問題を理解して解決する
description: Azure AD アプリケーション プロキシでの CORS について、また CORS の問題を識別して解決する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b57fc7e3af99819c9b27b6bc796e501d1db02818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259170"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory アプリケーション プロキシの CORS の問題を理解して解決する

[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) が原因で、Azure Active Directory アプリケーション プロキシ経由で公開するアプリや API で問題が発生することがあります。 この記事では、Azure AD アプリケーション プロキシでの CORS の問題と解決策について説明します。

通常、ブラウザーのセキュリティ機能により、Web ページでは AJAX 要求を別のドメインに送信することはできません。 この制限は、*同一オリジン ポリシー* と呼ばれ、悪意のあるサイトが、別のサイトから機密データを読み取れないようにします。 ただし、他のサイトから Web API を呼び出したほうが良い場合もあります。 W3C 標準である CORS によって、サーバーは同一オリジン ポリシーを緩和したり、一部のクロスオリジン要求を許可して他の要求は拒否したりできます。

## <a name="understand-and-identify-cors-issues"></a>CORS の問題の理解と識別

次のように、2 つの URL のスキーム、ホスト、ポートが同一である ([RFC 6454](https://tools.ietf.org/html/rfc6454)) 場合、2 つの URL のオリジンは同じです。

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

以下の URL は、前の 2 つとはオリジンが異なります。

-   http:\//contoso.net - 異なるドメイン
-   http:\//contoso.com:9000/foo.html - 異なるポート
-   https:\//contoso.com/foo.html - 異なるスキーム
-   http:\//www.contoso.com/foo.html - 異なるサブドメイン

同一オリジン ポリシーは、正しいアクセス制御ヘッダーを使用する場合を除き、他のオリジンからのリソースにアプリがアクセスするのを防ぎます。 CORS ヘッダーが存在しないか正しくない場合、クロス オリジン要求は失敗します。 

CORS の問題はブラウザーのデバッグ ツールを使用して識別できます。

1. ブラウザーを起動して Web アプリをブラウズします。
1. **F12** キーを押してデバッグ コンソールを起動します。
1. トランザクションを再現し、コンソール メッセージを確認します。 CORS 違反があると、オリジンに関するコンソール エラーが発生します。

次のスクリーンショットでは、**[Try It]\(テスト\)** ボタンを選択すると、https:\//corswebclient-contoso.msappproxy.net が Access-Control-Allow-Origin ヘッダーに見つからなかったという CORS エラー メッセージが表示されました。

![CORS の問題](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>アプリケーション プロキシでの CORS の課題

次の例は、Azure AD アプリケーション プロキシの CORS の一般的なシナリオを示しています。 内部サーバーは、**CORSWebService** Web API コントローラーと、**CORSWebService** を呼び出す **CORSWebClient** をホストします。 **CORSWebClient** から **CORSWebService** への AJAX 要求があります。

![オンプレミスの同一オリジン要求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient アプリは、オンプレミスでホストしたときは動作しますが、Azure AD アプリケーション プロキシ経由で公開すると、読み込みに失敗するかエラーで終了します。 アプリケーション プロキシ経由で、異なるアプリとして CORSWebClient アプリと CORSWebService アプリを別々に公開した場合、2 つのアプリは異なるドメインでホストされます。 CORSWebClient から CORSWebService への AJAX 要求はクロス オリジン要求であり、失敗します。

![アプリケーション プロキシの CORS 要求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>アプリケーション プロキシの CORS の問題に対する解決策

上記の CORS の問題は、複数ある方法のいずれかで解決できます。

### <a name="option-1-set-up-a-custom-domain"></a>オプション 1: カスタム ドメインの設定

アプリのオリジン、コード、またはヘッダーを変更する必要なしに、同じオリジンから公開するには、Azure AD アプリケーション プロキシの[カスタム ドメイン](./application-proxy-configure-custom-domain.md)を使用します。 

### <a name="option-2-publish-the-parent-directory"></a>オプション 2:親ディレクトリを公開する

両方のアプリの親ディレクトリを公開します。 この解決策は、Web サーバー上にアプリが 2 つしかない場合は特に有効です。 各アプリを別々に公開する代わりに、共通の親ディレクトリを公開してオリジンを同じにすることができます。

次の例は、CORSWebClient アプリのポータルである Azure AD アプリケーション プロキシのページを示しています。  **[内部 URL]** が *contoso.com/CORSWebClient* に設定されている場合、アプリから *contoso.com/CORSWebService* ディレクトリに対しては、これらがクロス オリジンであるため、正常に要求を実行できません。 

![アプリの個別公開](./media/application-proxy-understand-cors-issues/image4.png)

代わりに、*CORSWebClient* ディレクトリと *CORSWebService* ディレクトリの両方を含む親ディレクトリを公開するように **[内部 URL]** を設定します。

![親ディレクトリの公開](./media/application-proxy-understand-cors-issues/image5.png)

結果として得られるアプリの URL は、CORS の問題を効果的に解決します。

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>オプション 3:HTTP ヘッダーを更新する

オリジン要求に一致するように、Web サービス上でカスタム HTTP 応答ヘッダーを追加します。 インターネット インフォメーション サービス (IIS) で実行されている Web サイトの場合、IIS マネージャーを使用してヘッダーを変更します。

![IIS マネージャーでのカスタム応答ヘッダーの追加](./media/application-proxy-understand-cors-issues/image6.png)

この変更には、コードの変更は一切必要ありません。 Fiddler のトレースで確認できます。

**Post the Header Addition**\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Content-Type: text/plain; charset=utf-8\
Expires: -1\
Vary:Accept-Encoding\
サーバー: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.net**\
X-AspNet-Version:4.0.30319\
X-Powered-By:ASP.NET\
Content-Length:17

### <a name="option-4-modify-the-app"></a>オプション 4:アプリを変更する

適切な値と共に Access-Control-Allow-Origin ヘッダーを追加することによって、CORS をサポートするようにアプリを変更することができます。 ヘッダーを追加する方法は、アプリのコードの言語によって異なります。 最も多くの作業量が必要なため、コードの変更は最もお勧めしない選択肢です。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>オプション 5:アクセス トークンの有効期間を延長する

アプリが認証のために *login.microsoftonline.com* にリダイレクトし、アクセス トークンの期限が切れた場合など、一部の CORS の問題は解決できません。 このとき、CORS の呼び出しは失敗します。 このシナリオの回避策は、ユーザーのセッション中に期限が切れるのを防ぐために、アクセス トークンの有効期間を延長することです。 このための手順については、[Azure AD の構成可能なトークン有効期間](../develop/active-directory-configurable-token-lifetimes.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目
- [チュートリアル:Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md) 
- [Azure AD アプリケーション プロキシのデプロイ計画](application-proxy-deployment-plan.md) 
- [Azure Active Directory アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](application-proxy.md)