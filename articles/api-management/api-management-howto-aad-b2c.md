---
title: "Azure Active Directory B2C を使用して開発者アカウントを承認する - Azure API Management | Microsoft Docs"
description: "API Management で Azure Active Directory B2C を使用してユーザーを承認する方法について説明します。"
services: api-management
documentationcenter: API Management
author: miaojiang
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eb7deb1a79d9db9ac5cfbea69b8d3c564eb55577
ms.lasthandoff: 03/09/2017

---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Azure API Management で Azure Active Directory B2C を使用して開発者アカウントを承認する方法
## <a name="overview"></a>概要
Azure Active Directory B2C は、コンシューマー向け Web アプリケーションおよびモバイル アプリケーション用のクラウド ID 管理ソリューションです。 これを使用して、開発者ポータルへのアクセスを管理することができます。 このガイドでは、Azure Active Directory B2C との統合のために API Management サービスで必要な構成について説明します。 従来の Azure Active Directory を使用して開発者ポータルへのアクセスを有効にする方法については、[Azure Active Directory を使用して開発者アカウントを承認する方法]に関する記事を参照してください。

> [!NOTE]
> このガイドの手順を実行するには、アプリケーションの作成先となる Azure Active Directory B2C テナントが事前に必要です。 また、サインアップ ポリシーとサインイン ポリシーを用意しておく必要があります。 詳細については、[Azure Active Directory B2C の概要]に関する記事を参照してください。

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して開発者アカウントを承認する

1. まず、ご利用の API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

   ![パブリッシャー ポータル][api-management-management-console]

   > [!NOTE]
   > まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の使用][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。

2. **[API Management]** メニューの **[セキュリティ]** をクリックします。 **[ID]** タブで、**[Azure Active Directory B2C]** を選択します。

  ![外部 ID 1][api-management-howto-aad-b2c-security-tab]

3. **リダイレクト URL** を書き留めて、Azure Portal の Azure Active Directory B2C に切り替えます。

  ![外部 ID 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. **[アプリケーション]** をクリックします。

  ![新しいアプリケーション 1 の登録][api-management-howto-aad-b2c-portal-menu]

5. **[追加]** ボタンをクリックして、新しい Azure Active Directory B2C アプリケーションを作成します。

  ![新しいアプリケーション 2 の登録][api-management-howto-aad-b2c-add-button]

6. **[新しいアプリケーション]** ブレードで、アプリケーションの名前を入力します。 **[Web App/Web API (Web アプリ/Web API)]** で **[はい]** を選択し、**[暗黙的フローを許可する]** で **[はい]** を選択します。 次に、発行者ポータルの **[ID]** タブの **[Azure Active Directory B2C]** セクションから**リダイレクト URL** をコピーし、**[応答 URL]** ボックスに貼り付けます。

  ![新しいアプリケーション 3 の登録][api-management-howto-aad-b2c-app-details]

7. **[Create]** ボタンをクリックします。 アプリケーションが作成され、**[アプリケーション]** ブレードに表示されます。 アプリケーション名をクリックすると、その詳細が表示されます。

  ![新しいアプリケーション 4 の登録][api-management-howto-aad-b2c-app-created]

8. **[プロパティ]** ブレードの**アプリケーション ID** をクリップボードにコピーします。

  ![アプリケーション ID 1][api-management-howto-aad-b2c-app-id]

9. 発行者ポータルに戻り、ID を **[クライアント ID]** ボックスに貼り付けます。

  ![アプリケーション ID 2][api-management-howto-aad-b2c-client-id]

10. Azure Portal に戻り、**[キー]** をクリックし、**[キーの生成]** をクリックします。 **[保存]** をクリックして構成を保存し、**アプリ キー**を表示します。 キーをクリップボードにコピーします。

  ![アプリ キー 1][api-management-howto-aad-b2c-app-key]

11. パブリッシャー ポータルに戻り、キーを **[クライアント シークレット]** ボックスに貼り付けます。

  ![アプリ キー 2][api-management-howto-aad-b2c-client-secret]

12. **[許可されるテナント]** ボックスに、Azure Active Directory B2C テナントのドメイン名を指定します。

  ![許可されるテナント][api-management-howto-aad-b2c-allowed-tenant]

13. **[Signup Policy (サインアップ ポリシー)]** と **[Signin Policy (サインイン ポリシー)]** を指定します。 必要に応じて、**[Profile Editing Policy (プロファイル編集ポリシー)]** と **[Password Reset Policy (パスワードのリセット ポリシー)]** も指定します。

  ![ポリシー][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > ポリシーの詳細については、「[Azure Active Directory B2C: 拡張可能なポリシー フレームワーク]」を参照してください。

14. 必要な構成を指定したら、**[保存]** をクリックします。

  変更が保存されると、開発者は新しいアカウントを作成し、Azure Active Directory B2C を使用して開発者ポータルにサインインできるようになります。

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して開発者アカウントにサインアップする

1. Azure Active Directory B2C を使用して開発者アカウントにサインアップするには、新しいブラウザー ウィンドウを開き、開発者ポータルに移動します。 **[サインアップ]** ボタンをクリックします。

   ![開発者ポータル 1][api-management-howto-aad-b2c-dev-portal]

2. **Azure Active Directory B2C** によるサインアップを選択します。

   ![開発者ポータル 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. 前のセクションで構成したサインアップ ポリシーにリダイレクトされます。 サインアップの方法として、電子メール アドレスか既存のソーシャル アカウントを選択します。

   > [!NOTE]
   > 発行者ポータルの **[ID]** タブで有効になっているオプションが Azure Active Directory B2C のみの場合は、サインアップ ポリシーに直接リダイレクトされます。

   ![[開発者ポータル]][api-management-howto-aad-b2c-dev-portal-b2c-options]

   サインアップが完了すると、開発者ポータルにリダイレクトされます。 これで、API Management サービス インスタンスの開発者ポータルにサインインしました。

    ![登録の完了][api-management-registration-complete]

## <a name="next-steps"></a>次のステップ

*  [Azure Active Directory B2C の概要]
*  [Azure Active Directory B2C: 拡張可能なポリシー フレームワーク]
*  [Azure Active Directory B2C で ID プロバイダーとして Microsoft アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして Google アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして LinkedIn アカウントを使用する]
*  [Azure Active Directory B2C で ID プロバイダーとして Facebook アカウントを使用する]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C の概要]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Azure Active Directory を使用して開発者アカウントを承認する方法]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: 拡張可能なポリシー フレームワーク]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Azure Active Directory B2C で ID プロバイダーとして Microsoft アカウントを使用する]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Azure Active Directory B2C で ID プロバイダーとして Google アカウントを使用する]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Azure Active Directory B2C で ID プロバイダーとして Facebook アカウントを使用する]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C で ID プロバイダーとして LinkedIn アカウントを使用する]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

