---
title: "エンドユーザー認証: Data Lake Store と Azure Active Directory | Microsoft Docs"
description: "Data Lake Store での Azure Active Directory を使用したエンドユーザー認証を行う方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 0f6af54b351235390afa88f1ce156abd839a723f
ms.lasthandoff: 03/03/2017


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したエンドユーザーの認証
> [!div class="op_single_selector"]
> * [サービス間認証](data-lake-store-authenticate-using-active-directory.md)
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 (この記事)
* サービス間認証

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

この記事では、**エンドユーザー認証用の Azure AD ネイティブ アプリケーション**の作成方法について説明します。 サービス間認証用に Azure AD アプリケーションを構成する方法については、「[Service-to-service authentication with Data Lake Store using Azure Active Directory (Data Lake Store での Azure Active Directory を使用したサービス間認証)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* サブスクリプション ID。 これは Azure ポータルから取得できます。 たとえば、[Data Lake Store アカウント] ブレードから入手できます。
  
    ![サブスクリプション ID の取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD ドメイン名。 Azure ポータルの右上隅にマウスを置くことで取得できます。 次のスクリーンショットでは、ドメイン名は **contoso.onmicrosoft.com** であり、丸かっこ内の GUID はテナント ID です。 
  
    ![AAD ドメインの取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>エンドユーザー認証
これは、エンドユーザーに Azure AD 経由でアプリケーションにログインしてもらう場合に推奨する方法です。 アプリケーションは、ログインしたエンド ユーザーと同じアクセス レベルで Azure リソースにアクセスできます。 エンド ユーザーは、アプリケーションのアクセスを維持するために、資格情報を定期的に入力する必要があります。

エンドユーザーがログインすると、アクセス トークンと更新トークンがアプリケーションに与えられます。 アクセス トークンは Data Lake Store または Data Lake Analytics に対するすべての要求にアタッチされ、既定では&1; 時間有効です。 更新トークンは、新しいアクセス トークンを取得するために使用でき、定期的に使用されるのであれば、既定では最大&2; 週間有効です。 エンド ユーザーのログインには、2 つの異なる方法を使用できます。

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2.0 ポップアップの使用
アプリケーションで、エンドユーザーが資格情報を入力できる OAuth 2.0 認証ポップアップをトリガーできます。 このポップアップは、必要であれば、Azure AD の&2; 要素認証 (2FA) プロセスでも機能します。 

> [!NOTE]
> この方法は、Python または Java 用の Azure AD Authentication Library (ADAL) ではまだサポートされていません。
> 
> 

### <a name="directly-passing-in-user-credentials"></a>ユーザーの資格情報を直接渡す
アプリケーションで、ユーザーの資格情報を Azure AD に直接提供できます。 この方法は、組織 ID ユーザー アカウントのみで機能します。@outlook.com や @live.com で終わる個人/"Live ID" ユーザー アカウントには対応しません。 さらに、この方法は、Azure AD の&2; 要素認証 (2FA) を必要とするユーザー アカウントには対応しません。

### <a name="what-do-i-need-to-use-this-approach"></a>この方法を使用するには何が必要か
* Azure AD ドメイン名。 これは、この記事の前提条件で既に示されています。
* Azure AD **ネイティブ アプリケーション**
* Azure AD ネイティブ アプリケーションのクライアント ID
* Azure AD ネイティブ アプリケーションの応答 URI
* 委任されたアクセス許可を設定する


## <a name="step-1-create-an-active-directory-web-application"></a>手順 1: Active Directory Web アプリケーションを作成する

Azure Active Directory を使用して Azure Data Lake Store でのエンドユーザー間認証を行う Azure AD ネイティブ アプリケーションを作成および構成する方法について説明します。 手順については、[Microsoft Azure での Ruby アプリケーションの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。

上記に示したリンクの指示に従うときは、次のスクリーンショットに示すように、アプリケーションの種類として **[ネイティブ]** を必ず選択してください。

![Web アプリの作成](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "ネイティブ アプリの作成")

## <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>手順 2. クライアント ID と応答 URI を取得し、委任されたアクセス許可を設定する

Azure AD ネイティブ アプリケーションのクライアント ID (アプリケーション ID とも言います) を取得するには、[クライアント ID の取得](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)に関するページを参照してください。

リダイレクト URI を取得するには、次の手順に従います。

1. Azure Portal で、**[Azure Active Directory]** を選択します。**[アプリの登録]** をクリックし、作成したばかりの Azure AD ネイティブ アプリケーションを見つけてクリックします。

2. アプリケーションの **[設定]** ブレードで、**[リダイレクト URI]** をクリックします。

    ![リダイレクト URI の取得](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. 表示された値をコピーします。


## <a name="step-3-set-permissions"></a>手順 3: アクセス許可を設定する

1. Azure Portal で、**[Azure Active Directory]** を選択します。**[アプリの登録]** をクリックし、作成したばかりの Azure AD ネイティブ アプリケーションを見つけてクリックします。

2. アプリケーションの **[設定]** ブレードで、**[必要なアクセス許可]** をクリックし、**[追加]** をクリックします。

    ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. **[API アクセスの追加]** ブレードで、**[API を選択します]** をクリックします。**[Azure Data Lake]** をクリックし、**[選択]** をクリックします。

    ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  **[API アクセスの追加]** ブレードで、**[アクセス許可の選択]** をクリックします。**[Full access to Data Lake Store (Data Lake Store にフル アクセス許可を与える)]** チェック ボックスをオンにし、**[選択]** をクリックします。

    ![[クライアント ID]](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    **[Done]**をクリックします。

5. 最後の&2; つの手順を繰り返して、**Windows Azure Service Management API** にも、アクセス許可を与えます。
   
## <a name="next-steps"></a>次のステップ
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java SDK などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Store で認証を行ってからストアで他の操作を実行する方法について説明しています。

* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)


