---
title: 暗黙的な許可から承認ワークフロー フローに JavaScript のシングルページ アプリを移行する | Azure
titleSuffix: Microsoft identity platform
description: MSAL.js 1.x を使用する JavaScript SPA と暗黙的な許可フローを、MSAL.js 2.x と PKCE および CORS サポートを使用する承認コード フローに更新する方法。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365942"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>暗黙的な許可から承認ワークフロー フローに JavaScript のシングルページ アプリを移行する

Microsoft Authentication Library for JavaScript (MSAL.js) v2.0 では、Microsoft ID プラットフォームでのシングルページ アプリケーションに対する、PKCE、CORS を使用した承認コード フローがサポートされます。 以降のセクションの手順に従い、暗黙的な許可を使用する MSAL.js 1.x アプリケーションを、MSAL.js 2.0+ (以下 *2.x*) と承認コード フローに移行します。

MSAL.js 2.x は、ブラウザーで暗黙的な許可のフローではなく承認コード フローをサポートすることで、MSAL.js 1.x よりも強化されています。 MSAL.js 2.x では、暗黙的フローはサポートされて **いません**。

## <a name="migration-steps"></a>移行の手順

MSAL.js 2.x と承認コード フローにアプリケーションを更新するには、主に 3 つの手順があります。

1. [アプリ登録](#switch-redirect-uris-to-spa-platform)リダイレクト URI を **Web** プラットフォームから **シングルページ アプリケーション** プラットフォームに切り替えます。
1. [コード](#switch-redirect-uris-to-spa-platform)を MSAL.js 1.x から **2.x** に更新します。
1. 登録を共有しているすべてのアプリケーションが MSAL.js 2.x と承認コード フローに更新されたら、アプリ登録の[暗黙的な許可](#disable-implicit-grant-settings)を無効にします。

後続のセクションでは、各手順についてさらに詳しく説明します。

## <a name="switch-redirect-uris-to-spa-platform"></a>リダイレクト URI を SPA プラットフォームに切り替える

アプリケーションの既存のアプリ登録を使い続ける場合、Azure portal を使用し、登録のリダイレクト URI を SPA プラットフォームに更新します。 これを行うと、登録を利用するアプリに対して PKCE および CORS サポートを使用した承認コード フローが有効になります (ただし、アプリケーションのコードを MSAL.js v2.x に更新する必要があります)。

**Web** プラットフォーム リダイレクト URI で現在構成されているアプリ登録には、以下の手順に従います。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインし、**Azure Active Directory** を選択します。
1. **[アプリの登録]** でアプリケーションを選択し、 **[認証]** を選択します。
1. **[Web]** プラットフォーム タイルの **[リダイレクト URI]** で、URI を移行する必要があることを示す警告バナーを選択します。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Azure portal の Web アプリ タイルの暗黙的なフロー警告バナー":::
1. アプリケーションで MSAL.js 2.x が使用されているリダイレクト URI "*のみ*" を選択し、 **[構成]** を選択します。

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Azure portal の SPA ペインで [リダイレクト URI] ペインを選択する":::

これらのリダイレクト URI が **[シングルページ アプリケーション]** プラットフォーム タイルに表示され、承認コード フローで CORS がサポートされ、これらの URI で PKCE が有効になっていることが示されるはずです。

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azure portal のアプリ登録のシングルページ アプリケーション タイル":::

既存の登録でリダイレクト URI を更新する代わりに、[新しいアプリ登録を作成する](scenario-spa-app-registration.md)こともできます。

## <a name="update-your-code-to-msaljs-2x"></a>コードを MSAL.js 2.x に更新する

MSAL 1.x で、次のように [UserAgentApplication][msal-js-useragentapplication] を初期化し、アプリケーション インスタンスを作成しました。

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

MSAL 2.x では、[PublicClientApplication][msal-js-publicclientapplication] を代わりに初期化します。

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

MSAL 2.x をアプリケーションに追加する方法の完全なチュートリアルについては、「[チュートリアル: 認証コード フローを使用して、ユーザーをサインインさせ、JavaScript シングルページ アプリ (SPA) から Microsoft Graph API を呼び出す](tutorial-v2-javascript-auth-code.md)」を参照してください。

場合によってはコードに行う必要がある追加の変更については、GitHub で[移行ガイド](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md)を参照してください。

## <a name="disable-implicit-grant-settings"></a>暗黙的な許可設定の無効化

このアプリ登録とそのクライアント ID が使用されるすべての運用アプリケーションを MSAL 2.x と承認コード フローに更新したら、アプリ登録で暗黙的な許可設定のチェックマークをオフにしてください。

アプリ登録で暗黙的な許可設定のチェックマークをオフにすると、登録とそのクライアント ID を利用するすべてのアプリケーションに対して暗黙的フローが無効になります。

すべてのアプリケーションを MSAL.js 2.x と [PublicClientApplication][msal-js-publicclientapplication] に更新するまで、暗黙的な許可フローを無効に **しないでください**。

## <a name="next-steps"></a>次のステップ

暗黙と承認コード フローの違いなど、承認コード フローの詳細については、[Microsoft ID プラットフォームと OAuth 2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)に関する記事を参照してください。

Microsoft ID プラットフォームでの JavaScript シングルページ アプリケーションの開発についてさらに詳しく知りたい場合は、複数パートから構成される記事の「[シナリオ: シングルページ アプリケーション](scenario-spa-overview.md)」シリーズが、作業を開始するのに役立ちます。

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
