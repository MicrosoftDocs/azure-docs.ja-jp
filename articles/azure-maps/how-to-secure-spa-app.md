---
title: 非対話型サインインでシングル ページ アプリケーションをセキュリティで保護する方法
titleSuffix: Azure Maps
description: 非対話型の Azure ロールベースのアクセス制御 (Azure RBAC) と Azure Maps Web SDK を使用してシングル ページ アプリケーションを構成する方法。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092729"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>非対話型サインインでシングル ページ アプリケーションをセキュリティで保護する方法

次のガイドでは、Azure Active Directory (Azure AD) を使用して、ユーザーが Azure AD にサインインできない場合に Azure Maps アプリケーションにアクセス トークンを提供するアプリケーションについて説明します。 このフローでは、シングル ページ Web アプリケーションからのみアクセスできるようにセキュリティで保護する必要がある、Web サービスをホストしている必要があります。 Azure AD への認証を実行できる実装は複数あります。 このガイドでは、製品である Azure Function を利用してアクセス トークンを取得します。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps は、ユーザーのサインオンや対話型フローからのアクセス トークンをサポートできます。 対話型フローを使用すると、アクセス失効とシークレット管理のスコープをより制限することができます。

## <a name="create-azure-function"></a>Azure 関数の作成

Azure AD の認証を行うセキュリティで保護された Web サービス アプリケーションを作成します。 

1. Azure portal で関数を作成します。 詳細については、[Azure 関数の作成](../azure-functions/functions-get-started.md)に関する記事を参照してください。

2. シングル ページ Web アプリケーションからアクセスできるように、Azure 関数で CORS ポリシーを構成します。 これにより、ブラウザー クライアントが Web アプリケーションの許可された配信元に安全にアクセスできるようになります。 詳細については、「[CORS 機能の追加](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality)」を参照してください。

3. Azure 関数に[システム割り当て ID を追加](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)して、Azure AD に認証するサービス プリンシパルを作成できるようにします。  

4. Azure Maps アカウントにシステム割り当て ID に対するロールベースのアクセス権を付与します。 詳細については、「[ロールベースのアクセス権を付与する](#grant-role-based-access)」を参照してください。

5. サポートされているいずれかのメカニズムまたは REST プロトコルを使用して、システム割り当て ID を使用して Azure Maps アクセス トークンを取得する Azure 関数のコードを記述します。 詳細については、「[Azure リソースのトークンを取得する](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)」を参照してください

    REST プロトコルの例のサンプルを次に示します。

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    応答のサンプル:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Azure 関数 HttpTrigger のセキュリティを構成する

   * [関数アクセス キーを作成する](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * 運用環境の Azure 関数の [HTTP エンドポイントをセキュリティで保護](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)する。
   
7. Web アプリケーション Azure Maps Web SDK を構成します。 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>ロールベースのアクセス権を付与する

"*Azure ロールベースのアクセス制御 (Azure RBAC)* " のアクセス権を付与するには、システム割り当て ID を 1 つ以上の Azure ロールの定義に割り当てます。 Azure Maps に使用できる Azure ロールの定義を表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC を使用してアクセス権を付与する](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** タブの **[ロール]** で、 **[Azure Maps データ閲覧者]** や **[Azure Maps データ共同作成者]** などの組み込みの Azure Maps ロールの定義を選択します。 **[アクセスの割り当て先]** で、 **[関数アプリ]** を選択します。 プリンシパルを名前で選択します。 次に、 **[保存]** を選択します。

   * 詳細については、[Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md)に関する記事をご覧ください。

> [!WARNING]
> Azure Maps の組み込みロールの定義は、多くの Azure Maps REST API に対する非常に大きな承認アクセスを提供します。 API のアクセスを最小限に制限するには、[カスタム ロールの定義を作成して、システム割り当て ID をカスタム ロールの定義に割り当てる](../role-based-access-control/custom-roles.md)方法に関する記事を参照してください。 これにより、アプリケーションが Azure Maps にアクセスするために必要な最小限の権限が有効になります。

## <a name="next-steps"></a>次のステップ

シングル ページ アプリケーションのシナリオの詳細については、次を参考にしてください。
> [!div class="nextstepaction"]
> [シングルページ アプリケーション](../active-directory/develop/scenario-spa-overview.md)

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示す他のサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)