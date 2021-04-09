---
title: 対話型シングル サインオンを使用して Web アプリケーションをセキュリティで保護する方法
titleSuffix: Azure Maps
description: OpenID Connect プロトコルを使用し、Azure Maps Web SDK で Azure AD シングル サインオンをサポートする Web アプリケーションの構成方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895586"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>ユーザー サインインを使用した Web アプリケーションのセキュリティ保護

以下のガイドは、Web サーバーでホストされ、複数のビジネス シナリオを保持し、Web サーバーにデプロイされるアプリケーションに関するものです。 このアプリケーションは、Azure AD ユーザーにのみ、セキュリティで保護されたリソースを提供する必要があります。 このシナリオの目的は、Azure AD に対して Web アプリケーションを認証し、ユーザーに代わって Azure Maps REST API を呼び出せるようにすることです。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD でアプリケーションの登録を作成する

ユーザーがサインインできるよう、Azure AD で Web アプリケーションを作成する必要があります。 この Web アプリケーションは、ユーザー アクセスを Azure Maps REST API に委任します。

1. Azure portal の Azure サービスの一覧で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。  

    > [!div class="mx-imgBorder"]
    > ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

2. **[名前]** を入力して **[Support account type]\(サポートされるアカウントの種類\)** を選択し、リダイレクト URI を指定します。これは、トークンを発行する Azure AD URL を表し、マップ コントロールがホストされる URL でもあります。 詳細については、Azure AD の「[シナリオ:ユーザーをサインインさせる Web アプリ](../active-directory/develop/scenario-web-app-sign-user-overview.md)」を参照してください。 Azure AD シナリオに指定されている手順を完了します。  

3. アプリケーションの登録が完了したら、ユーザーに対してアプリケーション サインインが機能することを確認します。 サインインが機能するなら、アプリケーションに対して、Azure Maps REST API への委任されたアクセスを許可できます。
    
4.  委任された API アクセス許可を Azure Maps に割り当てるには、アプリケーションに移動します。 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[所属する組織で使用している API]** で、「**Azure Maps**」を検索して選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を追加する](./media/how-to-manage-authentication/app-permissions.png)

5. **[Access Azure Maps] (Azure Maps へのアクセス)** の横にあるチェック ボックスをオンにしてから、 **[アクセス許可の追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

6. アプリケーション シークレットを使用してアプリの登録を構成することにより、Web アプリケーションが Azure Maps REST API を呼び出せるようにします。詳細な手順については、「[Web API を呼び出す Web アプリ:アプリの登録](../active-directory/develop/scenario-web-app-call-api-app-registration.md)」の手順に従う必要があります。 ユーザーに代わって Azure AD に対する認証を行うには、シークレットが必要です。 アプリの登録証明書またはシークレットは、Web アプリケーションが Azure AD に対する認証のために取得できるように、セキュリティで保護されたストアに格納されている必要があります。 
   
   * アプリケーションで Azure AD アプリの登録とシークレットが既に構成されている場合、この手順はスキップしてかまいません。

> [!Tip]
> アプリケーションが Azure 環境でホストされている場合は、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) と Azure Key Vault インスタンスを使用し、Azure Key Vault シークレットまたは証明書にアクセスするための[アクセス トークンを取得することにより](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)シークレットにアクセスすることをお勧めします。 Azure Key Vault に接続してシークレットを取得するには、[マネージド ID を使用した接続のチュートリアル](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)を参照してください。
   
7. Azure Maps Web SDK がトークンにアクセスできるように、セキュリティで保護されたトークン エンドポイントを実装します。 
   
   * トークン コントローラーのサンプルについては、[Azure Maps の Azure AD サンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)を参照してください。 
   * AspNetCore 以外の実装などについては、Azure AD ドキュメントの「[アプリのトークンを取得する](../active-directory/develop/scenario-web-app-call-api-acquire-token.md)」を参照してください。
   * セキュリティで保護されたトークン エンドポイントは、認証され許可されているユーザーが Azure Maps REST API を呼び出せるように、アクセス トークンを返す役割を担います。

8. ユーザーまたはグループの Azure ロールベースのアクセス制御 (Azure RBAC) を構成します。 [ロールベースのアクセスをユーザーに許可する](#grant-role-based-access-for-users-to-azure-maps)方法に関するセクションを参照してください。

9. セキュリティで保護されたトークン エンドポイントにアクセスするため、Azure Maps Web SDK を使用して Web アプリケーション ページを構成します。 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>次のステップ

Web アプリケーション シナリオの理解を深めます。
> [!div class="nextstepaction"]
> [シナリオ: ユーザーをサインインさせる Web アプリ](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps の Azure AD Web アプリのサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)