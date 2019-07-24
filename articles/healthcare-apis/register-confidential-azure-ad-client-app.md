---
title: Azure Active Directory で Confidential クライアント アプリケーションを登録する - Azure API for FHIR
description: この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 53ebce4151bfc9ef2324f0e75eb7d199d9479e83
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "58408448"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory に Confidential クライアント アプリケーションを登録する

この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 クライアント アプリケーションの登録は、ユーザーに代わって認証に使用でき、[リソース アプリケーション](register-resource-azure-ad-client-app.md)へのアクセスを要求できるアプリケーションを Azure Active Directory で表したものです。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。

ポータルで新しい Confidential アプリケーションを登録するには、以下の手順に従います。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、**[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、**[アプリの登録 (プレビュー)]** をクリックします。

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

    Azure API for FHIR (マネージド サービス) の場合、**[所属する組織で使用している API]** をクリックし、「Azure Healthcare APIs (Azure 医療 API)」を検索します。 オープン ソースの FHIR Server for Azure の場合、[[FHIR API Resource Application Registration]](register-resource-azure-ad-client-app.md)(FHIR API リソース アプリケーションの登録) を選択します。

    ![Confidential クライアント。 自分の API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. ユーザーに代わって Confidential アプリケーションが要求できるスコープ (アクセス許可) を選択します。

    ![Confidential クライアント。 委任されたアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>アプリケーション シークレット

1. アプリケーション シークレット (クライアント シークレット) を作成します。

    ![Confidential クライアント。 アプリケーション シークレット](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. シークレットの説明と期間を指定します。

3. 生成されると、ポータルに一度だけ表示されます。 メモし、安全に保管してください。

## <a name="next-steps"></a>次の手順

この記事では、Confidential クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-oss-powershell-quickstart.md)