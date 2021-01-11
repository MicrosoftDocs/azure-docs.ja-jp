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
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852550"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory に Confidential クライアント アプリケーションを登録する

このチュートリアルでは、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 

クライアント アプリケーションの登録は、ユーザーに代わって認証に使用でき、[リソース アプリケーション](register-resource-azure-ad-client-app.md)へのアクセスを要求できるアプリケーションを Azure Active Directory で表したものです。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。

ポータルで新しい Confidential アプリケーションを登録するには、以下の手順に従います。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. アプリケーションに表示名を与えます。

2. 応答 URL を入力してください。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、ここで入力します。

    ![新しい Confidential クライアント アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API のアクセス許可

次に API のアクセス許可を追加します。

1. **API のアクセス許可**を開きます。

    ![Confidential クライアント。 API のアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. **[アクセス許可の追加]** をクリックします

3. 適切なリソース API を選択します。

    Azure API for FHIR (マネージド サービス) の場合、 **[所属する組織で使用している API]** をクリックし、「Azure Healthcare APIs (Azure 医療 API)」を検索します。 オープン ソースの FHIR Server for Azure の場合、[[FHIR API Resource Application Registration]](register-resource-azure-ad-client-app.md)(FHIR API リソース アプリケーションの登録) を選択します。

    ![Confidential クライアント。 自分の API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. ユーザーに代わって Confidential アプリケーションが要求できるスコープ (アクセス許可) を選択します。

    ![Confidential クライアント。 委任されたアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>アプリケーション シークレット

1. アプリケーション シークレット (クライアント シークレット) を作成します。

    ![Confidential クライアント。 アプリケーション シークレット](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. シークレットの説明と期間を指定します。

3. 生成されると、ポータルに一度だけ表示されます。 メモし、安全に保管してください。

## <a name="next-steps"></a>次のステップ

この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 これで [Azure API for FHIR](fhir-paas-powershell-quickstart.md) をデプロイできます。

Azure API for FHIR をデプロイしたら、利用できる追加設定を確認できます。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-powershell-quickstart.md)