---
title: Azure AD にサービス アプリを登録する - Azure API for FHIR
description: トークンの認証と取得に使用できるサービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 34eec3ad0d2fc193744898b6f08cbe50c261c945
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853026"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Azure Active Directory にサービス クライアント アプリケーションを登録する

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 クライアント アプリケーションの登録とは、トークンの認証および取得に使用できるアプリケーションの Azure Active Directory の表現です。 サービス クライアントは、対話型のユーザー認証なしでアクセス トークンを取得するために、アプリケーションから使用されることを目的としています。 アクセス トークンを取得するときには、特定のアプリケーション アクセス許可を持ち、アプリケーション シークレット (パスワード) を使用します。

以下の手順に従って新しいサービス クライアントを作成します。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="service-client-application-details"></a>サービス クライアント アプリケーションの詳細

* サービス クライアントには表示名が必要です。また、応答 URL を指定することもできますが、通常は使用されません。

    ![Azure portal。 新しいサービス クライアント アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API のアクセス許可

サービス クライアント アプリケーション ロールを付与する必要があります。 

1. **[API のアクセス許可]** を開き、[[FHIR API Resource Application Registration]\(FHIR API リソース アプリケーションの登録\)](register-resource-azure-ad-client-app.md) を選択します。 Azure API for FHIR を使用する場合は、 **[所属する組織で使用している API]** の下で Azure Healthcare API を検索して、Azure Healthcare API へのアクセス許可を追加します。

    ![Azure portal。 サービス クライアント API のアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. リソース アプリケーションで定義されているアプリケーション ロールから選択します。

    ![Azure portal。 サービス クライアント アプリケーションのアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. アプリケーションへの同意を許可します。 必要なアクセス許可を持っていない場合は、Azure Active Directory 管理者に確認してください。

    ![Azure portal。 サービス クライアント管理者の同意](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>アプリケーション シークレット

サービス クライアントにはシークレット (パスワード) が必要です。これはトークンを取得するときに使用されます。

1. **[証明書とシークレット]** をクリックします。

2. **[新しいクライアント シークレット]** をクリックします

    ![Azure portal。 サービス クライアント シークレット](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. シークレットの期間を入力します。

4. 生成されると、ポータルに一度だけ表示されます。 メモして、安全に保管してください。

## <a name="next-steps"></a>次のステップ

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-oss-powershell-quickstart.md)