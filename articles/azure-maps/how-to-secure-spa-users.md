---
title: ユーザー サインインでシングル ページ アプリケーションをセキュリティで保護する方法
titleSuffix: Azure Maps
description: Azure Maps Web SDK で Azure AD シングル サインオン に対応したシングル ページ アプリケーションを構成する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895615"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>ユーザー サインインでシングル ページ アプリケーションをセキュリティで保護する

次のガイドは、コンテンツ サーバーでホストされているか、Web サーバーの依存関係が最小限であるアプリケーションについて説明します。 このアプリケーションは、保護されたリソースを Azure AD ユーザーにのみ提供します。 このシナリオの目的は、Azure AD に対して Web アプリケーションを認証し、ユーザーに代わって Azure Maps REST API を呼び出せるようにすることです。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD でアプリケーションの登録を作成する

ユーザーがサインインするための Web アプリケーションを Azure AD で作成します。 Web アプリケーションは、ユーザー アクセスを Azure Maps REST API へ委任します。

1. Azure portal の Azure サービスの一覧で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。  

    > [!div class="mx-imgBorder"]
    > ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

2. **[名前]** を入力して **[Support account type]\(サポートされるアカウントの種類\)** を選択し、リダイレクト URI を指定します。この URI は、トークンを発行する Azure AD URL を表し、マップ コントロールがホストされる URL でもあります。 詳細なサンプルについては、[Azure Maps Azure AD サンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)に関する記事を参照してください。 次に、 **[登録]** を選択します。  

3. 委任された API アクセス許可を Azure Maps に割り当てるには、アプリケーションに移動します。 次に、 **[アプリの登録]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[所属する組織で使用している API]** で、「**Azure Maps**」を検索して選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を追加する](./media/how-to-manage-authentication/app-permissions.png)

4. **[Access Azure Maps] (Azure Maps へのアクセス)** の横にあるチェック ボックスをオンにしてから、 **[アクセス許可の追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

5. `oauth2AllowImplicitFlow`を有効にする: これを有効にするには、アプリ登録の **[マニフェスト]** セクションで `oauth2AllowImplicitFlow` を `true` に設定します。

6. Web SDK で使用する Azure AD アプリ ID と Azure AD テナント ID をアプリ登録からコピーします。 Azure AD アプリの登録の詳細と `x-ms-client-id` を、Azure マップ アカウントから Web SDK に追加します。

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. ユーザーまたはグループの Azure ロールベースのアクセス制御 (Azure RBAC) を構成します。 [Azure RBAC を有効にするには、次のセクションを参照](#grant-role-based-access-for-users-to-azure-maps)してください。
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>次のステップ

シングル ページ アプリケーションのシナリオの詳細については、次を参考にしてください。
> [!div class="nextstepaction"]
> [シングルページ アプリケーション](../active-directory/develop/scenario-spa-overview.md)

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)