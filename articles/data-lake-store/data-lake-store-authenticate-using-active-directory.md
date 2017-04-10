---
title: "サービス間認証: Data Lake Store と Azure Active Directory | Microsoft Docs"
description: "Data Lake Store での Azure Active Directory を使用したサービス間認証を行う方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: ae2280f7bd7945f723d88dc6ce3f9a117074e93f
ms.lasthandoff: 03/03/2017


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用したサービス間認証
> [!div class="op_single_selector"]
> * [サービス間認証](data-lake-store-authenticate-using-active-directory.md)
> * [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store または Azure Data Lake Analytics と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 2 種類のオプションを使用できます。

* エンドユーザー認証 
* サービス間認証 (この記事) 

どちらのオプションでも、OAuth 2.0 トークンがアプリケーションに提供され、このトークンが Azure Data Lake Store または Azure Data Lake Analytics に対するすべての要求にアタッチされます。

この記事では、**サービス間認証用の Azure AD Web アプリケーション**の作成方法について説明します。 エンドユーザー認証用に Azure AD アプリケーションを構成する方法については、「[End-user authentication with Data Lake Store using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md)」 (Data Lake Store での Azure Active Directory を使用したエンドユーザー認証) を参照してください。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

## <a name="step-1-create-an-active-directory-web-application"></a>手順 1: Active Directory Web アプリケーションを作成する

Azure Active Directory を使用して Azure Data Lake Store でのサービス間認証を行う Azure AD Web アプリケーションを作成および構成する方法について説明します。 手順については、[Microsoft Azure での Ruby アプリケーションの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページを参照してください。

上記に示したリンクの指示に従うときは、次のスクリーンショットに示すように、アプリケーションの種類として **[Web アプリ/API]** を必ず選択してください。

![Web アプリの作成](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Web アプリの作成")

## <a name="step-2-get-client-id-client-secret-and-tenant-id"></a>手順 2: クライアント ID、クライアント シークレット、およびテナント ID を取得する
プログラムによってログインするときは、アプリケーションの ID が必要です。 アプリケーションがその独自の資格情報で動作する場合は、さらに認証キーが必要となります。

* アプリケーションのクライアント ID とクライアント シークレットを取得する方法については、「[アプリケーション ID と認証キーを取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」を参照してください。

* テナント ID を取得する方法については、「[テナント ID を取得する](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)」を参照してください。

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>手順 3: Azure AD アプリケーションを Azure Data Lake Store アカウントのファイルまたはフォルダー (サービス間認証用のみ) に割り当てる
1. 新しい [Azure Portal](https://portal.azure.com) にサインオンし、上記で作成した Azure Active Directory アプリケーションに関連付ける Azure Data Lake Store アカウントを開きます。
2. Data Lake Store アカウントのブレードで、 **[データ エクスプローラー]**をクリックします。
   
    ![Data Lake Store アカウントにディレクトリを作成する](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Data Lake Store アカウントにディレクトリを作成する")
3. **[データ エクスプローラー]** ブレードで、Azure AD アプリケーションへのアクセスを付与するファイルまたはフォルダーをクリックし、**[アクセス]** をクリックします。 ファイルへのアクセスを構成する場合は、**[ファイルのプレビュー]** ブレードから **[アクセス]** をクリックします。
   
    ![Data Lake ファイル システムに ACL を設定する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Data Lake ファイル システムに ACL を設定する")
4. **[アクセス]** ブレードには、既にルートに割り当てられている標準アクセスとカスタム アクセスが一覧表示されます。 **[追加]** アイコンをクリックして、カスタムレベルの ACL を追加します。
   
    ![標準アクセスとカスタム アクセスを一覧表示する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "標準アクセスとカスタム アクセスを一覧表示する")
5. **[追加]** アイコンをクリックして、**[カスタム アクセスの追加]** ブレードを開きます。 このブレードの **[ユーザーまたはグループの選択]** をクリックし、**[ユーザーまたはグループの選択]** ブレードで、作成しておいた Azure Active Directory アプリケーションを探します。 検索対象のグループが多数存在する場合は、上部にあるテキスト ボックスを使用してグループ名をフィルター処理できます。 追加するグループをクリックして、 **[選択]**をクリックします。
   
    ![グループを追加する](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "グループを追加する")
6. **[アクセス許可の選択]** をクリックして、アクセス許可を選択するともに、このアクセス許可に既定の ACL、アクセス ACL、またはその両方のいずれを割り当てるか選択します。 **[OK]**をクリックします。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "グループにアクセス許可を割り当てる")
   
    Data Lake Store でのアクセス許可と既定/アクセス ACL の詳細については、「[Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。
7. **[カスタム アクセスの追加]** ブレードで **[OK]** をクリックします。 新しく追加されたグループは、関連付けられたアクセス許可と一緒に **[アクセス]** ブレードに一覧表示されます。
   
    ![グループにアクセス許可を割り当てる](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "グループにアクセス許可を割り当てる")    

## <a name="next-steps"></a>次のステップ
この記事では、Azure AD Web アプリケーションを作成し、.NET SDK、Java SDK などを使用して作成するクライアント アプリケーションに必要な情報を収集しました。これで、以下の記事に進むことができます。これらの記事では、Azure AD Web アプリケーションを使用して、最初に Data Lake Store で認証を行ってからストアで他の操作を実行する方法について説明しています。

* [.NET SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-net-sdk.md)
* [Java SDK で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-java-sdk.md)

この記事では、アプリケーションに対してユーザー プリンシパルを実行するうえで必要な基本的な手順を説明しました。 さらに詳しい情報については、次の記事をご覧ください。
* [PowerShell を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [サービス プリンシパル認証に証明書認証を使用する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [他の方法で Azure AD に対して認証する](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)



