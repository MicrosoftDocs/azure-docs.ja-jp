---
title: Azure AD にパブリック クライアント アプリを登録する - Azure API for FHIR
description: この記事では、Azure での FHIR API のデプロイの準備として、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3624812cf9119c270c30cf5a7cbebb57030a5113
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019003"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Azure Active Directory にパブリック クライアント アプリケーションを登録する

この記事では、パブリック アプリケーションを Azure Active Directory に登録する方法について説明します。  

クライアント アプリケーションの登録とは、ユーザーに代わって認証を行い、API のアクセス許可を要求することができるアプリケーションの Azure Active Directory の表現です。 パブリック クライアントとは、シークレットの機密性を保持できないモバイル アプリケーションや単一ページの JavaScript アプリケーションなどのアプリケーションです。 この手順は [Confidential クライアントの登録](register-confidential-azure-ad-client-app.md)と似ていますが、パブリック クライアントがアプリケーションのシークレットを保持することは信頼できないため、追加する必要はありません。

このクイックスタートからは、[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)方法に関する全般情報が提供されます。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="application-registration-overview"></a>アプリケーションの登録の概要

1. アプリケーションに表示名を与えます。

2. 応答 URL を入力してください。 応答 URL は、認証コードがクライアント アプリケーションに返される場所です。 後で応答 URL を追加し、既存のものを編集することができます。

    ![Azure portal。 新しいパブリック アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


[デスクトップ](../../active-directory/develop/scenario-desktop-app-registration.md)、[モバイル](../../active-directory/develop/scenario-mobile-app-registration.md)、[シングルページ](../../active-directory/develop/scenario-spa-app-registration.md) アプリケーションをパブリック アプリケーションとして構成するには:

1. [Azure portal](https://portal.azure.com) の **[アプリの登録]** でアプリを選択し、 **[認証]** を選択します。

2. **[詳細設定]** の **[既定のクライアントの種類]** を選択します。 **[アプリケーションは、パブリック クライアントとして扱います]** には **[はい]** を選択します。

3. シングルページ アプリケーションの場合、 **[アクセス トークン]** と **[ID トークン]** を選択して暗黙のフローを有効にします。

   - アプリケーションでユーザーがサインインする場合は、 **[ID トークン]** を選択します。
   - アプリケーションでも保護された Web API を呼び出す必要がある場合は、 **[アクセス トークン]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

[Confidential クライアント アプリケーション](register-confidential-azure-ad-client-app.md)と同様に、このアプリケーションがユーザーに代わって要求できる API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を開きます。

    Azure API for FHIR を使用する場合は、 **[所属する組織で使用している API]** の下で Azure Healthcare API を検索して、Azure Healthcare API へのアクセス許可を追加します。 これは、既に [Azure API for FHIR](fhir-paas-powershell-quickstart.md) がデプロイされている場合にのみ、見つけることができます。

    
    他のリソース アプリケーションを参照している場合は、 **[自分の API][ で作成済みの](register-resource-azure-ad-client-app.md)FHIR API リソース アプリケーションの登録** を選択します。

    ![Azure portal。 新しいパブリック API のアクセス許可 - Azure API for FHIR の既定値](media/public-client-app/api-permissions.png)


2. アプリケーションから要求できるようにするアクセス許可を選択します。![Azure portal。 アプリのアクセス許可](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR サーバーのオーソリティを検証する
この記事で登録したアプリケーションと FHIR サーバーが同じ Azure AD テナントにある場合、次の手順に進むことができます。

クライアント アプリケーションを、FHIR サーバーとは別の Azure AD テナントで構成する場合は、**オーソリティ** を更新する必要があります。 Azure API for FHIR の [設定] --> [認証] でオーソリティを設定してください。 オーソリティを、 **https://login.microsoftonline.com/\<TENANT-ID>** に設定します。

## <a name="next-steps"></a>次のステップ

この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Postman を使用して FHIR サーバーへのアクセスをテストします。
 
>[!div class="nextstepaction"]
>[Postman を使用して Azure API for FHIR にアクセスする](access-fhir-postman-tutorial.md)