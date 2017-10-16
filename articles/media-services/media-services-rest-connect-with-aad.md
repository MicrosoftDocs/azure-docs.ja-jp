---
title: "REST で Azure AD 認証を使用して Azure Media Services API にアクセスする | Microsoft Docs"
description: "REST で Azure Active Directory 認証を使用して Azure Media Services API にアクセスする方法を説明します。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>REST で Azure AD 認証を使用して Azure Media Services API にアクセスする

Azure Media Services チームは、Azure Media Services にアクセスするために Azure Active Directory (Azure AD) 認証をサポートすることを発表しています。 さらに、Media Services にアクセスするための Azure Access Control サービス認証を廃止する予定であることを公表しています。 すべての Azure サブスクリプションとすべての Media Services アカウントは Azure AD テナントに割り当てられるため、Azure AD 認証のサポートによって、多数のセキュリティ上のメリットがもたらされます。 この変更と移行の詳細については、次のブログの投稿と記事を参照してください (アプリで Media Services .NET SDK を使用する場合)。

- [Azure Media Services announces support for Azure AD and deprecation of Access Control authentication](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation) (Azure Media Services による Azure AD のサポートと Access Control 認証の廃止の発表)
- [Azure AD 認証を使用して Azure Media Services API にアクセスする](media-services-use-aad-auth-to-access-ams-api.md)
- [Microsoft .NET で Azure AD 認証を使用して Azure Media Services API にアクセスする](media-services-dotnet-get-started-with-aad.md)
- [Azure ポータルで Azure AD 認証を開始する](media-services-portal-get-started-with-aad.md)

Media Services ソリューションの開発を次の制約の下で行わなければならない場合があります。

*   使用するプログラミング言語が Microsoft .NET または C# ではない、またはランタイム環境が Windows ではない。
*   Azure AD Authentication Library などの Azure AD ライブラリをプログラミング言語で使用できない、またはランタイム環境で使用できない。

アプリケーションの開発が、Access Control 認証と Azure Media Services へのアクセスの両方で REST API を使用して行われている場合があります。 このような場合は、Azure AD 認証と Azure Media Services へのアクセスで REST API のみを使用する方法が必要です。 Azure AD ライブラリまたは Media Services .NET SDK に依存しないようにする必要があります。 この記事では、このようなシナリオに適合するソリューションを説明し、サンプル コードを示します。 コードはすべて REST API 呼び出しであり、Azure AD または Azure Media Services ライブラリに依存していないため、他のプログラミング言語に簡単に変換できます。

> [!IMPORTANT]
> 現在 Media Services では、Azure Access Control Service 認証モデルがサポートされています。 ただし、Access Control 認証は 2018 年 6 月 1 日に廃止される予定です。 できるだけ早く Azure AD 認証モデルに移行することをお勧めします。


## <a name="design"></a>設計

この記事では、次の認証および承認の設計を使用しています。

*  承認プロトコル: OAuth 2.0。 OAuth 2.0 は、認証と承認の両方をカバーする Web セキュリティ標準です。 Google、Microsoft、Facebook、および PayPal でサポートされています。 OAuth 2.0 は、2012 年 10 月に発行されています。 Microsoft は、OAuth 2.0 と OpenID Connect を完全サポートしています。 Azure Active Directory、Open Web Interface for .NET (OWIN) Katana、Azure AD ライブラリを含むさまざまなサービスとクライアント ライブラリで、これらの標準の両方がサポートされています。
*  付与タイプ: クライアント資格情報。 クライアント資格情報は、OAuth 2.0 における 4 つの付与タイプの 1 つです。 Azure AD Microsoft Graph API アクセスでよく使用されています。
*  認証モード: サービス プリンシパル。 もう 1 つの認証モードは、ユーザー (対話型) 認証です。

Media Services を使用するための Azure AD の認証および承認フローには、4 つのアプリケーションまたはサービスが関係しています。 これらのアプリケーション、サービス、およびフローを次の表に示します。

|アプリケーションの種類 |アプリケーション |フロー|
|---|---|---|
|クライアント | ユーザーのアプリまたはソリューション | Azure サブスクリプションと Media Services アカウントが存在する Azure AD テナントにこのアプリ (実際にはそのプロキシ) が登録されます。 登録されたアプリのサービス プリンシパルに、Media Services アカウントの Access Control (IAM) の所有者または共同作成者ロールが付与されます。 サービス プリンシパルは、アプリのクライアント ID とクライアント シークレットによって表されます。 |
|ID プロバイダー (IDP) | IDP としての Azure AD | 登録されたアプリのサービス プリンシパル (クライアント ID とクライアント シークレット) が、IDP としての Azure AD によって認証されます。 この認証は、内部的かつ暗黙的に実行されます。 クライアント資格情報フローのように、ユーザーの代わりにクライアントが認証されます。 |
|Secure Token Service (STS)/OAuth サーバー | STS としての Azure AD | IDP (OAuth 2.0 の用語では OAuth サーバー) によって認証された後、STS としての Azure AD/OAuth サーバーによって、中間層のリソース (ここでは Media Services REST API エンドポイント) にアクセスするためのアクセス トークンまたは JSON Web トークン (JWT) が発行されます。 |
|リソース | Media Services REST API | STS としての Azure AD または OAuth サーバーが発行するアクセス トークンによって、すべての Media Services REST API 呼び出しが承認されます。 |

サンプル コードを実行して JWT トークンまたはアクセス トークンをキャプチャした場合、JWT には次の属性があります。

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

JWT の属性と、前の表に示した 4 つのアプリケーションまたはサービスとのマッピングを次に示します。

|アプリケーションの種類 |アプリケーション |JWT 属性 |
|---|---|---|
|クライアント |ユーザーのアプリまたはソリューション |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". 次のセクションで Azure AD に登録するアプリケーションのクライアント ID。 |
|ID プロバイダー (IDP) | IDP としての Azure AD |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  GUID は、Microsoft テナント の ID です (microsoft.onmicrosoft.com)。 各テナントには、独自の一意の ID があります。 |
|Secure Token Service (STS)/OAuth サーバー |STS としての Azure AD | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". GUID は、Microsoft テナント の ID です (microsoft.onmicrosoft.com)。 |
|リソース | Media Services REST API |aud: "https://rest.media.azure.net". アクセス トークンの受信者また対象者。 |

## <a name="steps-for-setup"></a>セットアップ手順

Azure AD 認証用に Azure AD アプリケーションを登録して設定し、Azure Media Services REST API エンドポイントを呼び出すためのアクセス トークンを取得するには、次の手順を完了します。

1.  [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、Azure AD アプリケーション (例: wzmediaservice) を Azure AD テナント (例: microsoft.onmicrosoft.com) に登録します。 Web アプリとして登録してもネイティブ アプリとして登録してもかまいません。 任意のサインオン URL と応答 URL も選択できます (たとえば、両方で http://wzmediaservice.com を選択できます)。
2. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、アプリケーションの **[構成]** タブに移動します。 **クライアント ID** を書き留めます。 次に、**[キー]** で、**クライアント キー** (クライアント シークレット) を生成します。 

    > [!NOTE] 
    > クライアント シークレットを必ず書き留めてください。 二度と表示されることはありません。
    
3.  [Azure ポータル](http://ms.portal.azure.com) で、Media Services アカウントに移動します。 **[アクセス制御 (IAM)]** ウィンドウを選択します。 所有者または共同作成者ロールが割り当てられている新しいメンバーを追加します。 プリンシパルで、手順 1 で登録したアプリケーション名 (この例では wzmediaservice) を検索します。

## <a name="info-to-collect"></a>収集する情報

REST でのコーディングを準備するために、コードに含める次のデータ ポイントを収集します。

*   STS としての Azure AD エンドポイント: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token。 このエンドポイントから JWT アクセス トークンが要求されます。 IDP として機能することに加えて、Azure AD は STS としても機能します。 リソースにアクセスするための JWT (アクセス トークン) は Azure AD が発行します。 JWT トークンにはさまざまな要求が含まれます。
*   リソースまたは対象としての Azure Media Services REST API: https://rest.media.azure.net。
*   クライアント ID: [セットアップ手順](#steps-for-setup)の手順 2 を参照してください。
*   クライアント シークレット: [セットアップ手順](#steps-for-setup)の手順 2 を参照してください。
*   次の形式の Media Services アカウントの REST API エンドポイント: 

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    これは、アプリケーションのすべての Media Services REST API 呼び出しの呼び出し先エンドポイントです。 例: https://willzhanmswjapan.restv2.japanwest.media.azure.net/API。

これら 5 つのパラメーターを web.config または app.config ファイルに入力して、コードで使用できます。

## <a name="sample-code"></a>サンプル コード

サンプル コードは、[Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Azure Media Services にアクセスするための Azure AD Authentication: 両方で REST API を使用) で見つけることができます。

サンプル コードには、次の 2 つの部分があります。

*   DLL ライブラリ プロジェクト。Azure AD の認証および承認を行うためのすべての REST API コードが含まれます。 Media Services REST API エンドポイントへの REST API 呼び出しをアクセス トークンを使用して行うためのメソッドも含まれています。
*   コンソール テスト クライアント。Azure AD 認証を開始し、別の Media Services REST API を呼び出します。

サンプル プロジェクトには、次の 3 つの機能があります。

*   REST API のみを使用する、クライアント資格情報の付与による azure AD 認証。
*   REST API のみを使用する Azure Media Services へのアクセス。
*   REST API のみを使用する Azure Storage へのアクセス (REST API による Media Services アカウントの作成で使用)。


## <a name="where-is-the-refresh-token"></a>更新トークンの行方

一部の読者は、更新トークンはどこに行ったのかと疑問に思うかもしれません。 ここで更新トークンが使用されないのなぜでしょうか。

更新トークンの目的は、アクセス トークンを更新することではありません。 その目的は、トークンの有効期限が切れるときに、エンドユーザー認証またはユーザーの介入をバイパスして、引き続き有効なアクセス トークンを取得することです。 更新トークンの名前は、「ユーザーの再度のサインインをバイパスするトークン」のほうが意味として適しています。

OAuth 2.0 承認付与フローを使用する場合 (ユーザー名とパスワードがユーザーの代理として機能します)、更新トークンによって、ユーザーの介入を要求せずに、更新されたアクセス トークンを取得できます。 ただし、この記事で説明した OAuth 2.0 クライアント資格情報付与フローでは、クライアント自身が代理として機能します。 ユーザーの介入はまったく必要がなく、承認サーバーは更新トークンを提供する必要がないため、提供は行われません。 **GetUrlEncodedJWT** メソッドをデバッグすれば、トークン エンドポイントからの応答にアクセス トークンはあるが、更新トークンはないことがわかります。

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)を開始します。
