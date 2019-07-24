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
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 10792c912e3028cd12eedb83b1b211bab20bc5a7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961065"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する

この記事では、Azure Active Directory (Azure AD) 内のユーザーに対して開発者ポータルへのアクセスを有効にする方法について説明します。 また、Azure AD ユーザーが含まれた外部グループを追加することで Azure AD ユーザーのグループを管理する方法についても説明します。

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
- Azure API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD を使用して開発者アカウントを承認する

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. Select ![矢印](./media/api-management-howto-aad/arrow.png)。
1. 検索ボックスに、「**api**」と入力します。
1. **[API Management サービス]** を選択します。
1. お使いの API Management サービス インスタンスを選択します。
1. **[セキュリティ]** の **[ID]** を選択します。

1. 一番上にある **[+追加]** を選択します。

    **[ID プロバイダーの追加]** ウィンドウが右側に表示されます。
1. **[プロバイダーの種類]** で **[Azure Active Directory]** を選択します。

    その他必要な情報を入力するためのコントロールがウィンドウに表示されます。 そのようなコントロールとして、**[クライアント ID]** や **[クライアント シークレット]** があります  (これらのコントロールに関する情報は、記事の後半で取得します)。
1. **[リダイレクト URL]** の内容をメモします。
    
   ![Azure Portal で ID プロバイダーを追加するための手順](./media/api-management-howto-aad/api-management-with-aad001.png)  
1. ブラウザーで別のタブを開きます。 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. Select ![矢印](./media/api-management-howto-aad/arrow.png)。
1. 「**active**」と入力します。 **[Azure Active Directory]** ウィンドウが表示されます。
1. **[Azure Active Directory]** を選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. **[新しいアプリケーションの登録]** を選択します。

    ![新しいアプリの登録を作成するための選択内容](./media/api-management-howto-aad/api-management-with-aad002.png)

    **[作成]** ウィンドウが右側に表示されます。 ここに Azure AD アプリの関連情報を入力します。
1. アプリケーションの名前を入力します。
1. アプリケーション タイプとして **[Web アプリ/API]** を選択します。
1. サインイン URL には、開発者ポータルのサインイン URL を入力します。 この例では、サインイン URL は `https://apimwithaad.portal.azure-api.net/signin` です。
1. **[作成]** を選択して、アプリケーションを作成します。
1. アプリを検索するには、**[アプリの登録]** を選択して名前で検索します。

    ![アプリの検索ボックス](./media/api-management-howto-aad/find-your-app.png)
1. アプリケーションの登録後、**[応答 URL]** に移動し、**[リダイレクト URL]** が、手順 9. でメモした値に設定されていることを確認します。 
1. アプリケーションを構成する場合 (**アプリ ID の URL** を変更するなど) は、**[プロパティ]** を選択します。

    ![[プロパティ] ウィンドウを開く](./media/api-management-howto-aad/api-management-with-aad004.png)

    このアプリケーションで複数の Azure AD インスタンスを使用する場合は、**[マルチテナント]** に対して **[はい]** を選択します。 既定値は **[いいえ]** です。
1. **[必要なアクセス許可]** を選択して、アプリケーションのアクセス許可を設定します。
1. アプリケーションを選択し、**[ディレクトリ データの読み取り]** と **[サインインとユーザー プロファイルの読み取り]** のチェック ボックスをオンにします。

    ![アクセス許可のチェック ボックス](./media/api-management-howto-aad/api-management-with-aad005.png)

1. **[アクセス許可を付与する]** を選択してアプリケーションのアクセス許可に同意します。

    アプリケーションのアクセス許可と委任されたアクセス許可の詳細については、[Graph API へのアクセス][Accessing the Graph API]に関するページを参照してください。
    
1. 左側のウィンドウで、**[アプリケーション ID]** の値をコピーします。

    ![[アプリケーション ID] の値](./media/api-management-howto-aad/application-id.png)
1. API Management アプリケーションに戻ります。 

    **[ID プロバイダーの追加]** ウィンドウで、**[クライアント ID]** ボックスに **[アプリケーション ID]** の値を貼り付けます。
1. Azure AD の構成に戻り、**[キー]** を選択します。
1. 名前と期間を指定して新しいキーを作成します。 
1. **[保存]** を選択します。 キーが生成されます。

    キーをクリップボードにコピーします。

    ![キーを作成するための選択内容](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > このキーを書き留めておきます。 Azure AD の構成ウィンドウを閉じた後はキーを再表示できません。
    > 
    > 

1. API Management アプリケーションに戻ります。 

    **[ID プロバイダーの追加]** ウィンドウで、**[クライアント シークレット]** ボックスにキーを貼り付けます。

    > [!IMPORTANT]
    > キーの有効期限が切れる前に、**クライアント シークレット** キーを更新するようにしてください。 
    >  
    >

1. **[ID プロバイダーの追加]** ウィンドウには、**[許可されているテナント]** テキスト ボックスもあります。 ここには、API Management サービス インスタンスの API へのアクセスを許可する Azure AD インスタンスのドメインを指定します。 複数のドメインを指定する場合は、改行文字、スペース、またはコンマで区切ります。

    **[許可されているテナント]** セクションには、複数のドメインを指定できます。 アプリケーションが登録されている元のドメインとは別のドメインからユーザーがサインインするには、別のドメインの全体管理者がアプリケーションにディレクトリ データへのアクセス許可を付与する必要があります。 アクセス許可を付与するために、全体管理者は次の操作を行う必要があります。
    
    a. `https://<URL of your developer portal>/aadadminconsent` に移動します (例: https://contoso.portal.azure-api.net/aadadminconsent)。
    
    b. アクセスを許可する Azure AD テナントのドメイン名を入力します。
    
    c. **[Submit]\(送信\)** をクリックします。 
    
    次の例では、miaoaad.onmicrosoft.com の全体管理者がこの特定の開発者ポータルの権限を付与しようとしています。 

1. 必要な構成を指定したら、**[追加]** を選択します。

    ![[ID プロバイダーの追加] ウィンドウの [追加] ボタン](./media/api-management-howto-aad/api-management-with-aad007.png)

変更が保存されると、指定された Azure AD インスタンスのユーザーは、「[Azure AD アカウントを使用して開発者ポータルにサインインする](#log_in_to_dev_portal)」の手順に従って開発者ポータルにサインインできます。

![Azure AD テナントの名前の入力](./media/api-management-howto-aad/api-management-aad-consent.png)

次の画面で、アクセス許可の付与を確認するメッセージが全体管理者に表示されます。 

![アクセス許可の割り当ての確認](./media/api-management-howto-aad/api-management-permissions-form.png)

全体管理者がアクセス許可を付与する前に全体管理者以外のユーザーがログインしようとすると、サインイン試行は失敗し、エラー画面が表示されます。

## <a name="add-an-external-azure-ad-group"></a>外部 Azure AD グループを追加する

Azure AD インスタンス内のユーザーのアクセスを有効にした後、API Management で Azure AD グループを追加できます。 これにより、グループ内の開発者と目的の成果物の関連付けをより簡単に管理できます。

外部の Azure AD グループを構成するには、先に前のセクションの手順を実行して、**[ID]** タブで Azure AD を構成する必要があります。 

外部の Azure AD グループは、API Management インスタンスの **[グループ]** タブから追加します。

1. **[グループ]** タブを選択します。
1. **[AAD グループの追加]** ボタンを選択します。
   ![[AAD グループの追加] ボタン](./media/api-management-howto-aad/api-management-with-aad008.png)
1. 追加するグループを選択します。
1. **[選択]** ボタンを押します。

外部 Azure AD グループを追加した後、そのプロパティを確認および構成できます。 **[グループ]** タブからグループの名前を選択します。ここでは、グループの **[名前]** と **[説明]** の情報を編集できます。
 
これで、構成された Azure AD インスタンスのユーザーが開発者ポータルにサインインできるようになります。 これらのユーザーは、可視性があるすべてのグループを表示し、サブスクライブすることができます。

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Azure AD アカウントを使用して開発者ポータルにサインインする

前のセクションで構成した Azure AD アカウントを使用して開発者ポータルにサインインするには、次の操作を行います。

1. Active Directory のアプリケーション構成のサインイン URL を使用して新しいブラウザー ウィンドウを開き、**[Azure Active Directory]** を選択します。

   ![サインイン ページ][api-management-dev-portal-signin]

1. Azure AD 内のいずれかのユーザーの資格情報を入力し、**[サインイン]** を選択します。

   ![ユーザー名とパスワードを使用してサインインする][api-management-aad-signin]

1. 追加情報が必要な場合は、登録フォームが表示されることがあります。 登録フォームに入力し、**[サインアップ]** を選択します。

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
