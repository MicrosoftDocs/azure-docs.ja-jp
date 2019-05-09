---
title: Azure Active Directory でリソース アプリケーションを登録する - Azure API for FHIR
description: この記事では、リソース アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e8305c5a69fa3fda29f4f1292b7faa59f8ec3608
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870148"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Azure Active Directory にリソース アプリケーションを登録する

この記事では、リソース (または API) アプリケーションを Azure Active Directory に登録する方法について説明します。 リソース アプリケーションは、FHIR サーバー API 自体を Azure Active Directory で表したもので、クライアント アプリケーションは、認証時にリソースへのアクセスを要求できます。 リソース アプリケーションは、OAuth の用語で *audience* とも呼ばれます。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、**[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、**[アプリの登録 (プレビュー)]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="add-a-new-application-registration"></a>新しいアプリケーションの登録を追加する

新しいアプリケーションの詳細を入力します。 表示名に特定の要件はありませんが、FHIR サーバーの URI に設定すると、検索が簡単になります。

![[新しいアプリケーションの登録]](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>識別子 URI を設定し、スコープを定義する

リソース アプリケーションには、識別子 URI (アプリケーション ID URI) があり、クライアントは、リソースへのアクセスを要求するときにこれを使用できます。 この値は、アクセス トークンの `aud` 要求を設定します。 この値は、FHIR サーバーの URI に設定することをお勧めします。 FHIR アプリの SMART の場合、*audience* は FHIR サーバーの URI であると想定されます。

1. **[API の公開]** をクリックします

2. *[アプリケーション ID URI]* の横にある **[設定]** をクリックします。

3. 識別子 URI を入力し、**[保存]** をクリックします。 適切な識別子 URI は、FHIR サーバーの URI になります。

4. **[スコープの追加]** をクリックして、API に対して定義するあらゆるスコープを追加します。 Azure AD では現在、スコープ名でスラッシュ (`/`) は使用できません。 代わりに `$` を使用することをお勧めします。 `patient/*.read` のようなスコープは `patient$*.read` になります。

    ![audience とスコープ](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>アプリケーション ロールを定義する

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

## <a name="next-steps"></a>次の手順

この記事では、リソース アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-oss-powershell-quickstart.md)