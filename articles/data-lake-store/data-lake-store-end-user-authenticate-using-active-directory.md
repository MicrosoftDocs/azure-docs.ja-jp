---
title: エンド ユーザー認証 - Data Lake Storage Gen1 で Azure AD を使用する
description: Data Lake Storage Gen1 による Azure Active Directory を使用したエンドユーザー認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103783"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Data Lake Storage Gen1 による Azure Active Directory を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [サービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 では、認証するために Azure Active Directory を使用します。 Data Lake Storage Gen1 または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 (この記事)
* サービス間の認証 (上のドロップダウンからこのオプションを選択します)

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Storage Gen1 または Azure Data Lake Analytics に対する各要求にアタッチされます。

この記事では、**エンドユーザー認証用の Azure AD ネイティブ アプリケーション** の作成方法について説明します。 サービス間認証用に Azure AD アプリケーションを構成する方法については、[Data Lake Storage Gen1 による Azure Active Directory を使用したサービス間認証](./data-lake-store-service-to-service-authenticate-using-active-directory.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* サブスクリプション ID。 これは Azure Portal から取得できます。 たとえば、[Data Lake Storage Gen1 アカウント] ブレードから入手できます。

    ![サブスクリプション ID の取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD ドメイン名。 Azure Portal の右上隅にマウスを置くことで取得できます。 次のスクリーンショットでは、ドメイン名は **contoso.onmicrosoft.com** であり、丸かっこ内の GUID はテナント ID です。

    ![AAD ドメインの取得](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Azure テナント ID。 テナント ID を取得する方法については、「[テナント ID を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」を参照してください。

## <a name="end-user-authentication"></a>エンドユーザー認証
この認証メカニズムは、エンド ユーザーに Azure AD 経由でアプリケーションにサインインしてもらう場合に推奨される方法です。 アプリケーションは、ログインしたエンド ユーザーと同じアクセス レベルで Azure リソースにアクセスできます。 エンド ユーザーは、アプリケーションのアクセスを維持するために、資格情報を定期的に入力する必要があります。

エンドユーザーがサインインすると、アプリケーションにアクセス トークンと更新トークンが付与されます。 アクセス トークンは Data Lake Storage Gen1 または Data Lake Analytics に対する各要求にアタッチされ、既定では 1 時間有効です。 更新トークンは、新しいアクセス トークンを取得するために使用でき、既定では最大 2 週間有効です。 エンド ユーザーのサインインには、2 つの異なる方法を使用できます。

### <a name="using-the-oauth-20-pop-up"></a>OAuth 2.0 ポップアップの使用
アプリケーションで、エンド ユーザーが資格情報を入力できる OAuth 2.0 認証ポップアップをトリガーできます。 このポップアップは、必要であれば、Azure AD の 2 要素認証 (2FA) プロセスでも機能します。

> [!NOTE]
> この方法は、Python または Java 用の Azure AD Authentication Library (ADAL) ではまだサポートされていません。
>
>

### <a name="directly-passing-in-user-credentials"></a>ユーザーの資格情報を直接渡す
アプリケーションで、ユーザーの資格情報を Azure AD に直接提供できます。 この方法は、組織 ID ユーザー アカウントのみで機能します。@outlook.com や @live.com で終わるアカウントを含む個人や "live ID" のユーザー アカウントには対応しません。 さらに、この方法は、Azure AD の 2 要素認証 (2FA) を必要とするユーザー アカウントには対応しません。

### <a name="what-do-i-need-for-this-approach"></a>この方法を使用するための要件
* Azure AD ドメイン名。 この要件は、この記事の前提条件で既に示されています。
* Azure AD テナント ID。 この要件は、この記事の前提条件で既に示されています。
* Azure AD **ネイティブ アプリケーション**
* Azure AD ネイティブ アプリケーションのアプリケーション ID
* Azure AD ネイティブ アプリケーションのリダイレクト URI
* 委任されたアクセス許可を設定する


## <a name="step-1-create-an-active-directory-native-application"></a>手順 1:Active Directory ネイティブ アプリケーションを作成する

Azure Active Directory を使用して Data Lake Storage Gen1 によるエンドユーザー間認証を行う Azure AD ネイティブ アプリケーションを作成および構成します。 手順については、[Microsoft Azure での Ruby アプリケーションの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関するページを参照してください。

リンクの指示に従うときは、次のスクリーンショットに示すように、アプリケーションの種類として **[ネイティブ]** を必ず選択してください。

![Web アプリを作成する](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "ネイティブ アプリを作成する")

## <a name="step-2-get-application-id-and-redirect-uri"></a>手順 2:アプリケーション ID とリダイレクト URI を取得する

アプリケーション ID の取得については、[アプリケーション ID の取得](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)に関するページをご覧ください。

リダイレクト URI を取得するには、次の手順を実行します。

1. Azure Portal で **[Azure Active Directory]** を選択します。 **[アプリの登録]** をクリックし、作成した Azure AD ネイティブ アプリケーションを見つけてクリックします。

2. アプリケーションの **[設定]** ブレードで、 **[リダイレクト URI]** をクリックします。

    ![リダイレクト URI の取得](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. 表示された値をコピーします。


## <a name="step-3-set-permissions"></a>手順 3:アクセス許可を設定する

1. Azure Portal で **[Azure Active Directory]** を選択します。 **[アプリの登録]** をクリックし、作成した Azure AD ネイティブ アプリケーションを見つけてクリックします。

2. アプリケーションの **[設定]** ブレードで、 **[必要なアクセス許可]** をクリックし、 **[追加]** をクリックします。

    ![[設定] ブレードを示すスクリーンショット。[リダイレクト URI] オプションが呼び出され、実際の URI が呼び出されている [リダイレクト URI] ブレードが表示されています。](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. **[API アクセスの追加]** ブレードで、 **[API を選択します]** をクリックします。 **[Azure Data Lake]** をクリックし、 **[選択]** をクリックします。

    ![[API を選択します] オプションが呼び出されている [API アクセスの追加] ブレードと、[Azure Data Lake] オプションと [選択] オプションが呼び出されている [API を選択します] ブレードのスクリーンショット。](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  **[API アクセスの追加]** ブレードで、 **[アクセス許可の選択]** をクリックします。 **[Full access to Data Lake Store (Data Lake Store にフル アクセス許可を与える)]** チェック ボックスをオンにし、 **[選択]** をクリックします。

    ![[アクセス許可の選択] オプションが呼び出されている [API アクセスの追加] ブレードと、[Have full access to the Azure Data Lake service]\(Azure Data Lake サービスへのフル アクセス\) オプションと [選択] オプションが呼び出されている [アクセスの有効化] ブレードのスクリーンショット。](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    **[Done]** をクリックします。

5. 最後の 2 つの手順を繰り返して、**Windows Azure Service Management API** にも、アクセス許可を与えます。

## <a name="next-steps"></a>次のステップ
この記事では、Azure AD ネイティブ アプリケーションを作成し、.NET SDK、Java SDK、REST API などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Storage Gen1 による認証を行ってからストアに対して他の操作を実行する方法について説明しています。

* [Data Lake Storage Gen1 による Java SDK を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-java-sdk.md)
* [Data Lake Storage Gen1 による .NET SDK を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-net-sdk.md)
* [Data Lake Storage Gen1 による Python を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-python.md)
* [Data Lake Storage Gen1 による REST API を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-rest-api.md)