---
title: シングル ページ アプリケーション (SPA) の登録 | Azure
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーションを構築する方法 (アプリの登録) について説明します
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: efd51e90bb14f3d97b76eb6ac45b384192bb8da0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311570"
---
# <a name="single-page-application-app-registration"></a>シングルページ アプリケーション：アプリの登録

Microsoft ID プラットフォームにシングル ページ アプリケーション (SPA) を登録するには、次の手順を実行します。 登録手順は、暗黙的な許可フローをサポートする MSAL.js 1.0 と、PKCE での承認コード フローをサポートする MSAL.js 2.0 で異なります。

[!INCLUDE [MSAL.js 2.0 and Azure AD B2C temporary incompatibility notice](../../../includes/msal-b2c-cors-compatibility-notice.md)]

## <a name="create-the-app-registration"></a>アプリの登録を作成する

MSAL.js 1.0 ベースと MSAL.js 2.0 ベースの両方のアプリケーションで、次の手順を完了して最初のアプリ登録を作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。 アカウントに複数のテナントへのアクセス権がある場合は、上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、作成しようとしているアプリ登録が含まれるテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. **[新規登録]** を選択して、アプリケーションの **[名前]** を入力し、アプリケーションの **[サポートされているアカウントの種類]** を選択します。 **[リダイレクト URI]** は入力**しないでください**。 さまざまなアカウントの種類については、「[Azure portal を使用して新しいアプリケーションを登録します](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)」を参照してください。
1. **[登録]** を選択して、アプリの登録を作成します。

次に、 **[リダイレクト URI]** を使用してアプリの登録を構成し、Microsoft ID プラットフォームがクライアントをセキュリティ トークンと共にリダイレクトする場所を指定します。 アプリケーションで使用している MSAL.js のバージョンに適した手順を使用します。

- [承認コード フローを使用した MSAL.js 2.0](#redirect-uri-msaljs-20-with-auth-code-flow) (推奨)
- [暗黙的なフローを使用した MSAL.js 1.0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>リダイレクト URI: [承認コード フローを使用した MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

MSAL.js 2.0 以降を使用するアプリのリダイレクト URI を追加するには、次の手順に従います。 MSAL.js 2.0 以降では、[ブラウザーのサード パーティ Cookie の制限](reference-third-party-cookies-spas.md)に対応して、PKCE および CORS を使用した承認コード フローをサポートしています。 暗黙的な許可フローは、MSAL.js 2.0 以降ではサポートされていません。

1. Azure portal で、「[アプリの登録を作成する](#create-the-app-registration)」で前に作成したアプリの登録を選択します。
1. **[管理]** の下で、 **[認証]** 、 **[プラットフォームの追加]** の順に選択します。
1. **[Web アプリケーション]** の下で、 **[シングル ページ アプリケーション]** タイルを選択します。
1. **[リダイレクト URI]** の下で、[リダイレクト URI](reply-url.md) を入力します。 **[暗黙的な許可]** の下にあるチェック ボックスをオンに**しないでください**。
1. **[構成]** を選択して、リダイレクト URI の追加を完了します。

これで、シングル ページ アプリケーション (SPA) の登録が完了し、クライアントがリダイレクトされ、セキュリティ トークンが送信されるリダイレクト URI が構成されました。 **[プラットフォームの追加]** ウィンドウの **[シングル ページ アプリケーション]** タイルを使用してリダイレクト URI を構成すると、アプリケーションの登録は、PKCE と CORS を使用した承認コード フローをサポートするように構成されます。

さらに詳細なガイダンスについては、[チュートリアル](tutorial-v2-javascript-auth-code.md)に従ってください。

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>リダイレクト URI: [暗黙的なフローを使用した MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

次の手順に従って、MSAL.js 1.3 以前と暗黙的な許可フローを使用するシングル ページ アプリのリダイレクト URI を追加します。 MSAL.js 1.3 以前を使用するアプリケーションでは、承認コード フローはサポートされていません。

1. Azure portal で、「[アプリの登録を作成する](#create-the-app-registration)」で前に作成したアプリの登録を選択します。
1. **[管理]** の下で、 **[認証]** 、 **[プラットフォームの追加]** の順に選択します。
1. **[Web アプリケーション]** の下で、 **[シングル ページ アプリケーション]** タイルを選択します。
1. **[リダイレクト URI]** の下で、[リダイレクト URI](reply-url.md) を入力します。
1. **暗黙的なフロー**を有効にします。
    - アプリケーションでユーザーがサインインする場合は、 **[ID トークン]** を選択します。
    - アプリケーションでも保護された Web API を呼び出す必要がある場合は、 **[アクセス トークン]** を選択します。 これらのトークンの種類の詳細については、[ID トークン](id-tokens.md)と[アクセス トークン](access-tokens.md)に関するページを参照してください。
1. **[構成]** を選択して、リダイレクト URI の追加を完了します。

これで、シングル ページ アプリケーション (SPA) の登録が完了し、クライアントがリダイレクトされ、セキュリティ トークンが送信されるリダイレクト URI が構成されました。 **ID トークン**と**アクセス トークン**のどちらかまたは両方を選択すると、暗黙的な許可フローが有効になります。

さらに詳細なガイダンスについては、[チュートリアル](tutorial-v2-javascript-spa.md)に従ってください。

## <a name="note-about-authorization-flows"></a>承認フローについての注意事項

既定では、シングル ページ アプリケーション プラットフォーム構成を使用して作成されたアプリの登録によって、承認コード フローが有効になります。 このフローを利用するには、アプリケーションで MSAL.js 2.0 以降を使用する必要があります。

前述のように、MSAL.js 1.3 を使用するシングル ページ アプリケーションは、暗黙的な許可フローに限定されます。 現在の [OAuth 2.0 のベスト プラクティス](v2-oauth2-auth-code-flow.md)では、SPA に対して、暗黙的なフローではなく承認コード フローを使用することを推奨しています。 また、有効期間が制限された更新トークンを使用することも、Safari ITP のような[最新のブラウザーの Cookie プライバシー制限](reference-third-party-cookies-spas.md)にアプリケーションを適合させるのに役立ちます。

アプリの登録で表示されるすべての運用シングル ページ アプリケーションが MSAL.js 2.0 と承認コード フローを使用している場合は、Azure portal のアプリの登録の **[認証]** ウィンドウで [暗黙的な許可] 設定をオフにします。 ただし、暗黙的なフローを有効 (オン) にしたままにしておくと、MSAL.js 1.x と暗黙的なフローを使用するアプリケーションは引き続き機能します。

## <a name="next-steps"></a>次のステップ

次に、前の手順で作成したアプリの登録を使用するようにアプリのコードを構成します。

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-spa-app-configuration.md)
