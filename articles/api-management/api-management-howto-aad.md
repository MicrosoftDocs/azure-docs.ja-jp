---
title: Azure Active Directory を使用して開発者アカウントを承認する - Azure API Management | Microsoft Docs
description: API Management で Azure Active Directory を使用してユーザーを承認する方法について説明します。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 6116ece6035b326440108c1f2b2effd1b6bb9258
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072545"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する

この記事では、Azure Active Directory (Azure AD) 内のユーザーに対して開発者ポータルへのアクセスを有効にする方法について説明します。 また、Azure AD ユーザーが含まれた外部グループを追加することで Azure AD ユーザーのグループを管理する方法についても説明します。

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
- Azure API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD を使用して開発者アカウントを承認する

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. 選択 ![矢印](./media/api-management-howto-aad/arrow.png)。
3. 検索ボックスに、「**api**」と入力します。
4. **[API Management サービス]** を選択します。
5. お使いの API Management サービス インスタンスを選択します。
6. **[セキュリティ]** の **[ID]** を選択します。
7. 一番上にある **[+追加]** を選択します。

    **[ID プロバイダーの追加]** ウィンドウが右側に表示されます。
8. **[プロバイダーの種類]** で **[Azure Active Directory]** を選択します。

    その他必要な情報を入力するためのコントロールがウィンドウに表示されます。 そのようなコントロールとして、 **[クライアント ID]** や **[クライアント シークレット]** があります (これらのコントロールに関する情報は、記事の後半で取得します)。
9. **[リダイレクト URL]** の内容をメモします。
    
   ![Azure Portal で ID プロバイダーを追加するための手順](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. ブラウザーで別のタブを開きます。 
11. Active Directory にアプリを登録するために、[Azure portal - [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動します。
12. **[管理]** の **[アプリの登録]** を選択します。
13. **[新規登録]** を選択します。 **[アプリケーションの登録]** ページで、次のように値を設定します。
    
* **[名前]** をわかりやすい名前に設定します。 例: *developer-portal*
* **[サポートされているアカウントの種類]** を **[この組織のディレクトリ内のアカウントのみ]** に設定します。 
* **[リダイレクト URI]** を手順 9 で取得した値に設定します。 
* **[登録]** を選択します。 

14.  アプリケーションが登録されたら、 **[概要]** ページから **[アプリケーション (クライアント) ID]** をコピーします。 
15. API Management インスタンスに戻ります。 **[ID プロバイダーの追加]** ウィンドウで、 **[クライアント ID]** ボックスに **[アプリケーション (クライアント) ID]** の値を貼り付けます。
16. Azure AD 構成に戻り、 **[管理]** の下の **[証明書とシークレット]** を選択します。 **[New client secret]\(新しいクライアント シークレット\)** ボタンを選択します。 **[説明]** に値を入力して、 **[期限]** で任意のオプションを選択し、 **[追加]** を選択します。 ページから離れる前に、クライアント シークレット値をコピーします。 これは、次のステップで必要になります。 
17. **[管理]** の下で、 **[認証]** を選択し、 **[暗黙的な許可]** の下の **[ID トークン]** を選択します
18. API Management インスタンスに戻り、 **[クライアント シークレット]** ボックスにシークレットを貼り付けます。

    > [!IMPORTANT]
    > キーの有効期限が切れる前に、**クライアント シークレット** キーを更新するようにしてください。 
    >  
    >

19. **[ID プロバイダーの追加]** ウィンドウには、 **[許可されているテナント]** テキスト ボックスもあります。 ここには、API Management サービス インスタンスの API へのアクセスを許可する Azure AD インスタンスのドメインを指定します。 複数のドメインを指定する場合は、改行文字、スペース、またはコンマで区切ります。

> [!NOTE]
> **[許可されているテナント]** セクションには、複数のドメインを指定できます。 アプリケーションが登録されている元のドメインとは別のドメインからユーザーがサインインするには、別のドメインの全体管理者がアプリケーションにディレクトリ データへのアクセス許可を付与する必要があります。 アクセス許可を付与するために、全体管理者は次の操作を行う必要があります。a. `https://<URL of your developer portal>/aadadminconsent` に移動します (例: https://contoso.portal.azure-api.net/aadadminconsent) 。
> b. アクセスを許可する Azure AD テナントのドメイン名を入力します。
> c. **[Submit]\(送信\)** をクリックします。 

20.  必要な構成を指定したら、 **[追加]** を選択します。

変更が保存されると、指定された Azure AD インスタンスのユーザーは、「[Azure AD アカウントを使用して開発者ポータルにサインインする](#log_in_to_dev_portal)」の手順に従って開発者ポータルにサインインできます。

## <a name="add-an-external-azure-ad-group"></a>外部 Azure AD グループを追加する

Azure AD インスタンス内のユーザーのアクセスを有効にした後、API Management で Azure AD グループを追加できます。 これにより、グループ内の開発者と目的の成果物の関連付けをより簡単に管理できます。

 > [!IMPORTANT]
 > 外部の Azure AD グループを追加するには、前のセクションの手順に従って、まず **[ID]** タブで Azure AD インスタンスを構成する必要があります。 さらに、アプリケーションに、`Directory.Read.All` アクセス許可とともに Azure AD Graph API へのアクセス権限を付与する必要があります。 

外部の Azure AD グループは、API Management インスタンスの **[グループ]** タブから追加します。

1. **[グループ]** タブを選択します。
2. **[AAD グループの追加]** ボタンを選択します。
   ![[AAD グループの追加] ボタン](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 追加するグループを選択します。
4. **[選択]** ボタンを押します。

外部 Azure AD グループを追加した後、そのプロパティを確認および構成できます。 **[グループ]** タブからグループの名前を選択します。ここでは、グループの **[名前]** と **[説明]** の情報を編集できます。
 
これで、構成された Azure AD インスタンスのユーザーが開発者ポータルにサインインできるようになります。 これらのユーザーは、可視性があるすべてのグループを表示し、サブスクライブすることができます。

## <a name="a-idlog_in_to_dev_portalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Azure AD アカウントを使用して開発者ポータルにサインインする

前のセクションで構成した Azure AD アカウントを使用して開発者ポータルにサインインするには、次の操作を行います。

1. Active Directory のアプリケーション構成のサインイン URL を使用して新しいブラウザー ウィンドウを開き、 **[Azure Active Directory]** を選択します。

   ![サインイン ページ][api-management-dev-portal-signin]

1. Azure AD 内のいずれかのユーザーの資格情報を入力し、 **[サインイン]** を選択します。

   ![ユーザー名とパスワードを使用してサインインする][api-management-aad-signin]

1. 追加情報が必要な場合は、登録フォームが表示されることがあります。 登録フォームに入力し、 **[サインアップ]** を選択します。

   ![登録フォームの [サインアップ] ボタン][api-management-complete-registration]

ユーザーは、API Management サービス インスタンスの開発者ポータルにサインインしました。

![登録が完了した後の開発者ポータル][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
