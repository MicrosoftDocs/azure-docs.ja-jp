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
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103177"
---
# <a name="single-page-application-app-registration"></a>シングルページ アプリケーション：アプリの登録

Microsoft ID プラットフォームにシングル ページ アプリケーション (SPA) を登録するには、次の手順を実行します。 登録手順は、暗黙的な許可フローをサポートする MSAL.js 1.0 と、PKCE での承認コード フローをサポートする MSAL.js 2.0 で異なります。

## <a name="create-the-app-registration"></a>アプリの登録を作成する

MSAL.js 1.0 ベースと MSAL.js 2.0 ベースの両方のアプリケーションで、次の手順を完了して最初のアプリ登録を作成します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **[名前]** を入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. アプリケーションで **サポートされているアカウントの種類** を選択します。 **[リダイレクト URI]** は入力 **しないでください**。 さまざまなアカウントの種類の説明については、「[アプリケーションを登録する](quickstart-register-app.md)」を参照してください。
1. **[登録]** を選択して、アプリの登録を作成します。

次に、 **[リダイレクト URI]** を使用してアプリの登録を構成し、Microsoft ID プラットフォームがクライアントをセキュリティ トークンと共にリダイレクトする場所を指定します。 アプリケーションで使用している MSAL.js のバージョンに適した手順を使用します。

- [承認コード フローを使用した MSAL.js 2.0](#redirect-uri-msaljs-20-with-auth-code-flow) (推奨)
- [暗黙的なフローを使用した MSAL.js 1.0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>リダイレクト URI: [承認コード フローを使用した MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

MSAL.js 2.0 以降を使用するアプリのリダイレクト URI を追加するには、次の手順に従います。 MSAL.js 2.0 以降では、[ブラウザーのサード パーティ Cookie の制限](reference-third-party-cookies-spas.md)に対応して、PKCE および CORS を使用した承認コード フローをサポートしています。 暗黙的な許可フローは、MSAL.js 2.0 以降ではサポートされていません。

1. Azure portal で、「[アプリの登録を作成する](#create-the-app-registration)」で前に作成したアプリの登録を選択します。
1. **[管理]** で、 **[認証]**  >  **[プラットフォームを追加]** の順に選択します。
1. **[Web アプリケーション]** の下で、 **[シングル ページ アプリケーション]** タイルを選択します。
1. **[リダイレクト URI]** の下で、[リダイレクト URI](reply-url.md) を入力します。 **[暗黙的な許可およびハイブリッド フロー]** の下にあるチェック ボックスをオンに **しないでください**。
1. **[構成]** を選択して、リダイレクト URI の追加を完了します。

これで、シングル ページ アプリケーション (SPA) の登録が完了し、クライアントがリダイレクトされ、セキュリティ トークンが送信されるリダイレクト URI が構成されました。 **[プラットフォームの追加]** ウィンドウの **[シングル ページ アプリケーション]** タイルを使用してリダイレクト URI を構成すると、アプリケーションの登録は、PKCE と CORS を使用した承認コード フローをサポートするように構成されます。

さらに詳細なガイダンスについては、[チュートリアル](tutorial-v2-javascript-auth-code.md)に従ってください。

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>リダイレクト URI: [暗黙的なフローを使用した MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

次の手順に従って、MSAL.js 1.3 以前と暗黙的な許可フローを使用するシングル ページ アプリのリダイレクト URI を追加します。 MSAL.js 1.3 以前を使用するアプリケーションでは、承認コード フローはサポートされていません。

1. Azure portal で、「[アプリの登録を作成する](#create-the-app-registration)」で前に作成したアプリの登録を選択します。
1. **[管理]** で、 **[認証]**  >  **[プラットフォームを追加]** の順に選択します。
1. **[Web アプリケーション]** の下で、 **[シングル ページ アプリケーション]** タイルを選択します。
1. **[リダイレクト URI]** の下で、[リダイレクト URI](reply-url.md) を入力します。
1. **[暗黙的な許可およびハイブリッド フロー]** を有効にします。
    - アプリケーションでユーザーがサインインする場合は、 **[ID トークン]** を選択します。
    - アプリケーションでも保護された Web API を呼び出す必要がある場合は、 **[アクセス トークン]** を選択します。 これらのトークンの種類の詳細については、[ID トークン](id-tokens.md)と[アクセス トークン](access-tokens.md)に関するページを参照してください。
1. **[構成]** を選択して、リダイレクト URI の追加を完了します。

これで、シングル ページ アプリケーション (SPA) の登録が完了し、クライアントがリダイレクトされ、セキュリティ トークンが送信されるリダイレクト URI が構成されました。 **ID トークン** と **アクセス トークン** のどちらかまたは両方を選択すると、暗黙的な許可フローが有効になります。

さらに詳細なガイダンスについては、[チュートリアル](tutorial-v2-javascript-spa.md)に従ってください。

## <a name="note-about-authorization-flows"></a>承認フローについての注意事項

既定では、シングル ページ アプリケーション プラットフォーム構成を使用して作成されたアプリの登録によって、承認コード フローが有効になります。 このフローを利用するには、アプリケーションで MSAL.js 2.0 以降を使用する必要があります。

前述のように、MSAL.js 1.3 を使用するシングル ページ アプリケーションは、暗黙的な許可フローに限定されます。 現在の [OAuth 2.0 のベスト プラクティス](v2-oauth2-auth-code-flow.md)では、SPA に対して、暗黙的なフローではなく承認コード フローを使用することを推奨しています。 また、有効期間が制限された更新トークンを使用することも、Safari ITP のような[最新のブラウザーの Cookie プライバシー制限](reference-third-party-cookies-spas.md)にアプリケーションを適合させるのに役立ちます。

アプリの登録で表示されるすべての運用シングル ページ アプリケーションが MSAL.js 2.0 と承認コード フローを使用している場合は、Azure portal のアプリの登録の **[認証]** ウィンドウで [暗黙的な許可] 設定をオフにします。 ただし、暗黙的なフローを有効 (オン) にしたままにしておくと、MSAL.js 1.x と暗黙的なフローを使用するアプリケーションは引き続き機能します。

## <a name="next-steps"></a>次のステップ

前の手順で作成したアプリの登録を使用するようにアプリのコードを構成します。[アプリのコード構成](scenario-spa-app-configuration.md)。
