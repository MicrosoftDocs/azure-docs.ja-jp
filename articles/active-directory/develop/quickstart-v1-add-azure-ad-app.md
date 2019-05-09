---
title: Azure Active Directory v1.0 エンドポイントを使用してアプリを登録する
description: アプリケーションを追加して Azure Active Directory (Azure AD) v1.0 エンドポイントに登録する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e77767e16ce6ae02c6f1f93891d5acf4bfcb6f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119600"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v10-endpoint"></a>クイック スタート:Azure Active Directory v1.0 エンドポイントを使用してアプリを登録する

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

エンタープライズ開発者や SaaS (サービスとしてのソフトウェア) プロバイダーは、商用クラウド サービスまたは基幹業務アプリケーションを開発し、それらを Azure Active Directory (Azure AD) と統合することによって、自社のサービスにセキュリティで保護されたサインインと認可を実現することができます。 アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。

アプリケーションで Azure AD の機能を使用するには、まず Azure AD テナントにそのアプリケーションを登録する必要があります。 この登録プロセスでは、アプリケーションが配置されている URL、ユーザーが認証された後の応答の送信先となる URL、アプリを識別する URI など、アプリケーションの詳細を Azure AD に提供します。

このクイック スタートでは、Azure portal で既存の**アプリの登録**エクスペリエンスを使用して、Azure AD にアプリケーションを追加して登録する方法について説明します。

> [!NOTE]
> 新しいアプリを登録する場合は、 Azure portal の新しい**アプリの登録 (プレビュー)** エクスペリエンスをお試しください。 使用するには、[アプリの登録 (プレビュー)](quickstart-register-app.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

最初に、アプリケーションを登録するために使用できる Azure AD テナントがあることを確認してください。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure portal を使用して新しいアプリケーションを登録します

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
1. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。
1. **[アプリの登録]** を選択し、**[新しいアプリケーションの登録]** を選択します。

    ![新しいアプリケーションの登録](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration.png)

1. **[作成]** ページが表示されたら、アプリケーションの登録情報を入力します。 

   - **[名前]:** わかりやすいアプリケーション名を入力します
   - **アプリケーションの種類:**
     - デバイスのローカルにインストールされている[クライアント アプリケーション](developer-glossary.md#client-application)については、**[ネイティブ]** を選択します。 OAuth の public [ネイティブ クライアント](developer-glossary.md#native-client)の場合には、この設定を使用します。
     - セキュリティで保護されたサーバーにインストールされている[クライアント アプリケーション](developer-glossary.md#client-application)と[リソース/API アプリケーション](developer-glossary.md#resource-server)については、**[Web アプリ/API]** を選択します。 OAuth の confidential [Web クライアント](developer-glossary.md#web-client)と、public [ユーザーエージェントベース クライアント](developer-glossary.md#user-agent-based-client)の場合には、この設定を使用します。 同じアプリケーションでクライアントとリソース/API を両方とも公開することもできます。
   - **サインオン URL:** "Web アプリ/API" アプリケーションの場合は、アプリのベース URL を指定します。 ローカル マシンで実行されている Web アプリの URL であれば、たとえば `http://localhost:31544` のようになります。 ユーザーはこの URL を使用して、Web クライアント アプリケーションにサインインすることになります。 
   - **リダイレクト URI:** "ネイティブ" アプリケーションの場合は、トークン応答を返すために Azure AD に使用される URI を指定します。 `http://MyFirstAADApp` など、アプリケーションに固有の値を入力してください。

     ![新しいアプリケーションの登録 - 作成](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration-create.png)

     Web アプリケーションまたはネイティブ アプリケーションの具体的な例が必要な場合には、ドキュメントの**クイック スタート**を参照してください。

1. 終わったら、**[作成]** を選択します。

    Azure AD によりアプリケーションに一意の ID が割り当てられ、アプリケーションのメイン登録ページが表示されます。 アプリケーションが Web アプリケーションとネイティブ アプリケーションのどちらであるかに応じて、アプリケーションに機能を追加するためのさまざまなオプションが表示されます。

      > [!NOTE]
      > 既定では、新しく登録された Web アプリケーションは、同じテナントのユーザー**のみ**サインインできる構成になります。

## <a name="next-steps"></a>次の手順

- 同意の概要については、[Azure AD consent framework](consent-framework.md)\(Azure AD 同意フレームワーク\) を参照してください。
- アプリケーション登録で追加の構成機能 (資格情報、アクセス許可、他のテナントのユーザーによるサインインの有効化など) を有効にするには、[Update an application in Azure AD](quickstart-v1-update-azure-ad-app.md)\(Azure AD でのアプリケーションの更新\) を参照してください。
- 登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。
- Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインについては、[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)を参照してください。
