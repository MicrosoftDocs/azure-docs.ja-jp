---
title: Microsoft ID プラットフォームの認証ライブラリ | Azure
description: Microsoft ID プラットフォームと互換性のあるクライアント ライブラリとミドルウェアの一覧。 これらのライブラリを使用して、ユーザーのサインイン (認証) と保護された Web API アクセス (認可) のサポートをアプリケーションに追加します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690655"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft ID プラットフォームの認証ライブラリ

次の表は、いくつかのアプリケーションの種類に対する Microsoft 認証ライブラリのサポートを示しています。 これには、ライブラリ ソース コードへのリンク、アプリのプロジェクトのパッケージを取得する場所、ライブラリでユーザーのサインイン (認証)、保護された Web API へのアクセス (認可)、またはその両方がサポートされるかどうかが含まれます。

Microsoft ID プラットフォームは、[認定 OpenID プロバイダ](https://openid.net/certification/)として OpenID Foundation に認定されています。 Microsoft Authentication Library (MSAL) や Microsoft がサポートしている別のライブラリではないライブラリを使用する場合は、[認定 OpenID Connect 実装](https://openid.net/developers/certified/)のものを選択します。

[OAuth 2.0 または OpenID Connect 1.0](active-directory-v2-protocols.md) の独自のプロトコルレベル実装を手作業でコーディングする場合は、各標準の仕様におけるセキュリティの考慮事項に細心の注意を払い、[Microsoft SDL][Microsoft-SDL] などのソフトウェア開発ライフサイクル (SDL) 手法に従ってください。

## <a name="single-page-application-spa"></a>シングルページ アプリケーション (SPA)

シングルページ アプリケーションは、ブラウザー画面上で完全に実行され、ページ データ (HTML、CSS、JavaScript) を動的に、またはアプリケーションの読み込み時にフェッチします。 Web API を呼び出して、バックエンド データ ソースとやり取りできます。

SPA のコードは完全にブラウザーで実行されるため、シークレットを安全に格納できない *パブリック クライアント* と見なされます。

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Web アプリケーション

Web アプリケーションでは、HTML、CSS、および JavaScript を生成し、ユーザーの Web ブラウザーに送信してレンダリングするサーバーでコードを実行します。 ユーザーの ID は、ユーザーのブラウザー (フロントエンド) と Web サーバー (バックエンド) の間のセッションとして保持されます。

Web アプリケーションのコードは Web サーバー上で実行されるため、シークレットを安全に格納できる *機密クライアント* と見なされます。

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>デスクトップ アプリケーション

デスクトップ アプリケーションは、通常、ユーザー インターフェイスを表面に出し、ユーザーのデスクトップで実行されることを意図した、バイナリ (コンパイル済み) コードです。

デスクトップ アプリケーションはユーザーのデスクトップで実行されるため、シークレットを安全に保存できない *パブリック クライアント* と見なされます。

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>モバイル アプリケーション

モバイル アプリケーションは、通常、ユーザー インターフェイスを表面に出し、ユーザーのモバイル デバイスで実行されることを意図した、バイナリ (コンパイル済み) コードです。

モバイル アプリケーションはユーザーのモバイル デバイスで実行されるため、シークレットを安全に保存できない *パブリック クライアント* と見なされます。

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>サービス/デーモン

サービスとデーモンは、サーバー間およびその他の無人 (*ヘッドレス* とも呼ばれます) 通信でよく使用されます。 キーボードの前にユーザーがいて資格情報を入力したりリソースへのアクセスを同意したりすることがないため、これらのアプリケーションでは、Web API のリソースへの認可済みアクセスを要求するときに、ユーザーではなくそれ自身として認証されます。

サーバーで実行されるサービスまたはデーモンは、シークレットを安全に格納できる *機密クライアント* と見なされます。

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>次のステップ

Microsoft Authentication Library の詳細については、「[Microsoft Authentication Library (MSAL)の概要](msal-overview.md)」を参照してください。

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
