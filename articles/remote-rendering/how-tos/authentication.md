---
title: 認証
description: 認証のしくみについて説明します
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565857"
---
# <a name="configure-authentication"></a>認証を構成する。

Azure Remote Rendering には、[Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) と同じ認証メカニズムが使用されます。 REST API を正常に呼び出すには、クライアントで以下の内 *1 つ*を設定する必要があります。

* **AccountKey**: Azure portal の Remote Rendering アカウントの [キー] タブで取得できます。 アカウント キーは、開発、またはプロトタイプ作成でのみ推薦されます。
    ![アカウント ID](./media/azure-account-primary-key.png)

* **AuthenticationToken**: Azure AD トークンであり、[MSAL ライブラリ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)を使用して取得できます。 ユーザー資格情報を受け入れ、これらの資格情報を使用してアクセス トークンを取得するには、複数の異なるフローが使用できます。

* **MRAccessToken**: MR トークンであり、Azure Mixed Reality Security Token Service (STS) から取得できます。 次の呼び出しのような REST 呼び出しを使用して、`https://sts.mixedreality.azure.com` エンドポイントから取得されます。

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    ここで Authorization ヘッダーは、`Bearer <Azure_AD_token>` または `Bearer <accoundId>:<accountKey>` のように書式設定されます。 セキュリティの観点では前者が推薦されます。 この REST 呼び出しで返されたトークンが、MR アクセス トークンです。

## <a name="authentication-for-deployed-applications"></a>デプロイされたアプリケーションの認証

開発時には、短時間でプロトタイプを作成するためにアカウント キーを使用することをお勧めします。 組み込みのアカウント キーを使用してアプリケーションを運用環境に出荷しないことをお勧めします。 推奨される方法は、ユーザーベースまたはサービスベースの Azure AD 認証方法を使用することです。

 Azure AD 認証については、[Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) サービスに関するページの「[Azure AD ユーザー認証](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)」セクションを参照してください。

 詳細については、「[チュートリアル:Azure Remote Rendering とモデル ストレージのセキュリティ保護 - Azure Active Directory 認証](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)」を参照してください

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

サービスに付与するアクセス レベルを制御するには、ロールベースのアクセス権を付与するときに次のロールを使用します。

* **Remote Rendering 管理者**:ユーザーに、Azure Remote Rendering の変換、セッション管理、レンダリング、診断の機能を提供します。
* **Remote Rendering クライアント**:ユーザーに、Azure Remote Rendering のセッション管理、レンダリング、診断の機能を提供します。

## <a name="next-steps"></a>次のステップ

* [アカウントの作成](create-an-account.md)
* [認証に Azure フロントエンド API を使用する](frontend-apis.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
