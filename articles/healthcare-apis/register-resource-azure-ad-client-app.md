---
title: Azure AD へのリソース アプリの登録 - Azure API for FHIR
description: リソース (つまり API) アプリを Azure Active Directory に登録して、クライアント アプリケーションが認証時にリソースへのアクセスを要求できるようにします。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3c5486e31dd3227cfcc8bd3b433602ce58cfa4fb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852992"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Azure Active Directory にリソース アプリケーションを登録する

この記事では、リソース (または API) アプリケーションを Azure Active Directory に登録する方法について説明します。 リソース アプリケーションは、FHIR サーバー API 自体を Azure Active Directory で表したもので、クライアント アプリケーションは、認証時にリソースへのアクセスを要求できます。 リソース アプリケーションは、OAuth の用語で *audience* とも呼ばれます。

## <a name="azure-api-for-fhir"></a>FHIR 用の Azure API

Azure API for FHIR を使用している場合、リソース アプリケーションはサービスのデプロイ時に自動的に作成されます。 アプリケーションをデプロイするときと同じ Azure Active Directory テナントで Azure API for FHIR を使用している限り、この攻略ガイドをスキップし、代わりに Azure API for FHIR をデプロイして開始することができます。

(サブスクリプションに関連付けられていない) 別の Azure Active Directory テナントを使用している場合は、PowerShell を使用して Azure API for FHIR リソース アプリケーションをテナントにインポートできます。

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

または、Azure CLI を使用することもできます。

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR Server for Azure

オープン ソースの FHIR Server for Azure を使用している場合は、以下の手順に従って、リソース アプリケーションを登録します。

### <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

### <a name="add-a-new-application-registration"></a>新しいアプリケーションの登録を追加する

新しいアプリケーションの詳細を入力します。 表示名に特定の要件はありませんが、FHIR サーバーの URI に設定すると、検索が簡単になります。

![[新しいアプリケーションの登録]](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>識別子 URI を設定し、スコープを定義する

リソース アプリケーションには、識別子 URI (アプリケーション ID URI) があり、クライアントは、リソースへのアクセスを要求するときにこれを使用できます。 この値は、アクセス トークンの `aud` 要求を設定します。 この値は、FHIR サーバーの URI に設定することをお勧めします。 FHIR アプリの SMART の場合、*audience* は FHIR サーバーの URI であると想定されます。

1. **[API の公開]** をクリックします

2. *[アプリケーション ID URI]* の横にある **[設定]** をクリックします。

3. 識別子 URI を入力し、 **[保存]** をクリックします。 適切な識別子 URI は、FHIR サーバーの URI になります。

4. **[スコープの追加]** をクリックして、API に対して定義するあらゆるスコープを追加します。 将来、リソース アプリケーションにアクセス許可を付与するためには、少なくとも 1 つのスコープを追加する必要があります。 追加する特定のスコープがない場合は、user_impersonation をスコープとして追加できます。

![audience とスコープ](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>アプリケーション ロールを定義する

Azure API for FHIR と OSS FHIR Server for Azure は、ロールベースのアクセス制御に [Azure Active Directory アプリケーション ロール](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles)を使用します。 FHIR サーバー API で利用できるようにする必要のあるロールを定義するには、リソース アプリケーションの[マニフェスト](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)を開きます。

1. **[マニフェスト]** をクリックします。

    ![アプリケーション ロール](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. `appRoles` プロパティで、ユーザーまたはアプリケーションに与えるロールを追加します。

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>次のステップ

この記事では、リソース アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure API for FHIR をデプロイします。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-powershell-quickstart.md)