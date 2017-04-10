---
title: "Azure Active Directory を使用して開発者アカウントを認証する - Azure API Management | Microsoft Docs"
description: "API Management で Azure Active Directory を使用してユーザーを認証する方法について説明します。"
services: api-management
documentationcenter: API Management
author: steved0x
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
ms.sourcegitcommit: dc279718cbb360e611d7e4cfb6961a087159fb01
ms.openlocfilehash: 7637e6419d17a2d75904fbe63df5f27d4be4bbe3
ms.lasthandoff: 02/24/2017

---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Azure API Management で Azure Active Directory を使用して開発者アカウントを認証する方法
## <a name="overview"></a>概要
このガイドでは、Azure Active Directory 内のユーザーに対して開発者ポータルへのアクセスを有効にする方法について説明します。 また、Azure Active Directory のユーザーが含まれた外部グループを追加することで Azure Active Directory ユーザーのグループを管理する方法についても説明します。

> このガイドの手順を実行するには、アプリケーションの作成先となる Azure Active Directory が事前に必要です。
> 
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Azure Active Directory を使用して開発者アカウントを認証する方法
まず、ご利用の API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックします。 API Management パブリッシャー ポータルが表示されます。

![パブリッシャー ポータル][api-management-management-console]

> まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の概要][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。
> 
> 

左側の **[API Management]** メニューで **[セキュリティ]** をクリックして、**[外部 ID]** をクリックします。

![[外部 ID]][api-management-security-external-identities]

**[Azure Active Directory]**をクリックします。 **リダイレクト URL** を書き留めて、Azure クラシック ポータルの Azure Active Directory に切り替えます。

![[外部 ID]][api-management-security-aad-new]

**[追加]** ボタンをクリックして新しい Azure Active Directory アプリケーションを作成し、**[組織で開発中のアプリケーションを追加]** を選択します。

![新しい Azure Active Directory アプリケーションの追加][api-management-new-aad-application-menu]

アプリケーションの名前を入力し、 **[Web アプリケーションや Web API]**を選択して、次へ進むボタンをクリックします。

![新しい Azure Active Directory アプリケーション][api-management-new-aad-application-1]

**[サインオン URL]**には、開発者ポータルのサインオン URL を入力します。 この例では、**サインオン URL** は `https://aad03.portal.current.int-azure-api.net/signin` です。 

**[アプリ ID URL]**に、Azure Active Directory の既定のドメインとカスタム ドメインのいずれかを入力し、一意の文字列を追加します。 この例では、**https://contoso5api.onmicrosoft.com** という既定のドメインが使用され、**/api** というサフィックスが指定されています。

![新しい Azure Active Directory アプリケーションのプロパティ][api-management-new-aad-application-2]

チェック マークのボタンをクリックし、アプリケーションを作成して保存します。**[構成]** タブに切り替え、新しいアプリケーションを構成します。

![作成された新しい Azure Active Directory アプリケーション][api-management-new-aad-app-created]

このアプリケーションで複数の Azure Active Directory を使用する場合は、**[アプリケーションはマルチテナントです]** で **[はい]** をクリックします。 既定値は **[いいえ]**です。

![[はい]][api-management-aad-app-multi-tenant]

パブリッシャー ポータルの **[外部 ID]** タブの **[Azure Active Directory]** セクションから**リダイレクト URL** をコピーし、**[応答 URL]** ボックスに貼り付けます。 

![[応答 URL]][api-management-aad-reply-url]

[構成] タブの一番下までスクロールし、**[アプリケーションのアクセス許可]** ボックスの一覧を選択して、**[ディレクトリ データの読み取り]** チェック ボックスをオンにします。

![アプリケーションのアクセス許可][api-management-aad-app-permissions]

**[アクセス許可の委任]** ボックスの一覧を選択し、**[サインオンを有効にして、ユーザーのプロファイルを読み取る]** チェック ボックスをオンにします。

![委任されたアクセス許可][api-management-aad-delegated-permissions]

> アプリケーションと委任されたアクセス許可の詳細については、「[Graph API にアクセスする][Accessing the Graph API]」を参照してください。
> 
> 

**クライアント ID** をクリップボードにコピーします。

![クライアント ID][api-management-aad-app-client-id]

パブリッシャー ポータルに戻り、Azure Active Directory のアプリケーション構成からコピーした ID を **[クライアント ID]** に貼り付けます。

![クライアント ID][api-management-client-id]

Azure Active Directory の構成に戻り、**[キー]** セクションの **[時間の選択]** ボックスの一覧をクリックし、期間を指定します。 この例では **[1 年間]** が使用されます。

![キー][api-management-aad-key-before-save]

**[保存]** をクリックして構成を保存し、キーを表示します。 キーをクリップボードにコピーします。

> このキーを書き留めておきます。 Azure Active Directory の構成ウィンドウを閉じると、キーは再表示できません。
> 
> 

![キー][api-management-aad-key-after-save]

パブリッシャー ポータルに戻り、キーを **[クライアント シークレット]** ボックスに貼り付けます。

![[クライアント シークレット]][api-management-client-secret]

**[許可されたテナント]** で、API Management サービス インスタンスの API にアクセスできるディレクトリを指定します。 アクセス許可を付与する Azure Active Directory インスタンスのドメインを指定します。 複数のドメインを指定する場合は、改行文字、スペース、またはコンマで区切ります。

![[許可されたテナント]][api-management-client-allowed-tenants]


必要な構成を指定したら、 **[保存]**をクリックします。

![保存][api-management-client-allowed-tenants-save]

変更が保存さたら、指定された Azure Active Directory 内のユーザーは、「[Azure Active Directory アカウントを使用して開発者ポータルにログインする方法][Log in to the Developer portal using an Azure Active Directory account]」の手順に従って開発者ポータルにサインインできます。

**[許可されたテナント]** セクションには、複数のドメインを指定できます。 アプリケーションが登録されている元のドメインとは別のドメインからユーザーがログインするには、別のドメインの全体管理者がアプリケーションにディレクトリ データへのアクセス許可を付与する必要があります。 アクセス許可を付与するには、全体管理者は `https://<URL of your developer portal>/aadadminconsent` (たとえば、https://contoso.portal.azure-api.net/aadadminconsent) に移動し、アクセス許可する Active Directory テナントのドメイン名を入力して、[送信] をクリックします。 次の例では、`miaoaad.onmicrosoft.com` の全体管理者がこの特定の開発者ポータルの権限を付与しようとしています。 

![アクセス許可][api-management-aad-consent]

次の画面で、アクセス許可の付与を確認するメッセージが全体管理者に表示されます。 

![アクセス許可][api-management-permissions-form]

> 全体管理者がアクセス許可を付与する前に全体管理者以外のユーザーがログインしようとすると、ログインに失敗し、エラー画面が表示されます。
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>外部の Azure Active Directory グループを追加する方法
Azure Active Directory 内のユーザーのアクセスを有効にした後は、Azure Active Directory のグループを API Management に追加することで、グループ内の開発者と目的の成果物の関連付けを管理しやすくすることができます。

> 外部の Azure Active Directory グループを構成するには、先に前のセクションの手順を実行して、[外部 ID] タブで Azure Active Directory を構成する必要があります。 
> 
> 

外部の Azure Active Directory グループの追加は、グループにアクセス許可を付与する成果物の **[可視性]** タブで実行します。 **[成果物]**をクリックし、目的の成果物の名前をクリックします。

![成果物の構成][api-management-configure-product]

**[可視性]** タブに切り替え、**[Azure Active Directory からグループを追加]** をクリックします。

![グループの追加][api-management-add-groups]

ドロップダウン リストから **[Azure Active Directory テナント]** を選択し、**[追加するグループ]** ボックスに目的のグループの名前を入力します。

![グループの選択][api-management-select-group]

次の例に示すように、グループの名前が Azure Active Directory の **[グループ]** 一覧に表示されます。

![Azure Active Directory グループの一覧][api-management-aad-groups-list]

**[追加]** をクリックすると、グループ名が検証され、グループが追加されます。 この例では、**Contoso 5 Developers** という外部グループが追加されました。 

![グループが追加された][api-management-aad-group-added]

**[保存]** をクリックして、グループの新しい選択肢を保存します。

1 つの成果物から Azure Active Directory グループを構成したら、API Management サービス インスタンス内の他の製品でも **[可視性]** タブにこのグループのチェック ボックスが表示されます。

追加した後で外部グループのプロパティの確認や構成を行うには、**[グループ]** タブでグループの名前をクリックします。

![グループの管理][api-management-groups]

ここでは、グループの **[名前]** と **[説明]** を編集できます。

![グループの編集][api-management-edit-group]

構成した Azure Active Directory のユーザーは、開発者ポータルにサインインし、次のセクションの指示に従って、可視性を有効化されたグループの表示とサブスクライブを実行できます。

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Azure Active Directory アカウントを使用して開発者ポータルにログインする方法
前のセクションで構成した Azure Active Directory アカウントを使用して開発者ポータルにログインするには、Active Directory のアプリケーション構成の**サインオン URL** を使用して新しいブラウザー ウィンドウを開き、**[Azure Active Directory]** をクリックします。

![開発者ポータル][api-management-dev-portal-signin]

Azure Active Directory 内のいずれかのユーザーの資格情報を入力し、 **[サインイン]**をクリックします。

![[サインイン]][api-management-aad-signin]

追加情報が必要な場合は、登録フォームが表示されることがあります。 登録フォームに入力し、 **[サインアップ]**をクリックします。

![登録][api-management-complete-registration]

ユーザーが API Management サービス インスタンスの開発者ポータルにログインしました。

![登録の完了][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
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
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account


