---
title: Azure AD に Confidential クライアント アプリを登録する - Azure API for FHIR
description: ユーザーの代わりに認証し、リソース アプリケーションへのアクセスを要求する Confidential クライアント アプリを Azure Active Directory に登録します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019023"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory に Confidential クライアント アプリケーションを登録する

このチュートリアルでは、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 

クライアント アプリケーションの登録は、ユーザーに代わって認証に使用でき、[リソース アプリケーション](register-resource-azure-ad-client-app.md)へのアクセスを要求できるアプリケーションを Azure Active Directory で表したものです。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。

ポータルで新しい Confidential アプリケーションを登録するには、次の手順に従います。

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** に移動します。

1. **[アプリの登録]** を選択します。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

1. **[新規登録]** を選択します。

1. アプリケーションに表示名を与えます。

1. 応答 URL を入力してください。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、ここで入力します。

    ![新しい Confidential クライアント アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

これでアプリケーションが登録されたので、ユーザーに代わり、このアプリケーションによって要求できる API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を選択します。

    ![Confidential クライアント。 API のアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. **[アクセス許可の追加]** を選択します。

    Azure API for FHIR を使用する場合は、 **[所属する組織で使用している API]** で **Azure Healthcare API** を検索し、Azure Healthcare API へのアクセス許可を追加します。 これは、既に [Azure API for FHIR](fhir-paas-powershell-quickstart.md) がデプロイされている場合にのみ、見つけることができます。

    別のリソース アプリケーションを参照している場合は、 **[自分の API][ で作成済みの](register-resource-azure-ad-client-app.md)FHIR API リソース アプリケーションの登録** を選択します。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Confidential クライアント。自分の組織の API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. ユーザーに代わって Confidential アプリケーションが要求できるスコープ (アクセス許可) を選択します。

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Confidential クライアント。委任されたアクセス許可":::

## <a name="application-secret"></a>アプリケーション シークレット

1. **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。 

    ![Confidential クライアント。 アプリケーション シークレット](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. シークレットの説明と期間を指定します (1 年間、2 年間、または [なし])。

3. 生成されると、ポータルに一度だけ表示されます。 メモし、安全に保管してください。

## <a name="next-steps"></a>次のステップ

この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Postman を使用して、FHIR サーバーにアクセスします。
 
>[!div class="nextstepaction"]
>[Postman を使用して Azure API for FHIR にアクセスする](access-fhir-postman-tutorial.md)
