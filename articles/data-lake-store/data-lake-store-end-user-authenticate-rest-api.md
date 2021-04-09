---
title: エンドユーザー認証 - REST と Data Lake Storage Gen1 - Azure
description: REST API と Azure Active Directory を使用した Azure Data Lake Storage Gen1 でエンドユーザー認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103749"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>REST API を使用した Azure Data Lake Storage Gen1 でのエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

この記事では、REST API を使用した Azure Data Lake Storage Gen1 でのエンドユーザー認証を行う方法について説明します。 REST API を使用した Data Lake Storage Gen1 でのサービス間認証については、「[Service-to-service authentication with Data Lake Storage Gen1 using REST API (Data Lake Storage Gen1 に対する REST API を使用したサービス間認証)](data-lake-store-service-to-service-authenticate-rest-api.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 「[End-user authentication with Data Lake Storage Gen1 using Azure Active Directory (Azure Active Directory を使用した Data Lake Storage Gen1 でのエンドユーザー認証)](data-lake-store-end-user-authenticate-using-active-directory.md)」のステップを完了している必要があります。

* **[cURL](https://curl.haxx.se/)** 。 この記事では、cURL を使用して、Data Lake Storage Gen1 アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="end-user-authentication"></a>エンドユーザー認証
エンドユーザー認証は、ユーザーに Azure AD 経由でアプリケーションにログインしてもらう場合に推奨する方法です。 アプリケーションはログインしたユーザーと同じアクセス レベルで Azure リソースにアクセスできます。 エンド ユーザーはアプリケーションのアクセスを維持するために、資格情報を定期的に入力する必要があります。

エンド ユーザーがログインすると、アクセス トークンと更新トークンがアプリケーションに付与されます。 アクセス トークンは Data Lake Storage Gen1 または Data Lake Analytics に対する各要求にアタッチされ、既定では 1 時間有効です。 更新トークンは、新しいアクセス トークンを取得するために使用でき、定期的に使用されるのであれば、既定では最大 2 週間有効です。 エンド ユーザーのログインには、2 つの異なる方法を使用できます。

このシナリオでは、アプリケーションはユーザーにログインを求め、すべての操作はユーザーのコンテキストで実行されます。 次の手順に従います。

1. アプリケーションでユーザーを次の URL にリダイレクトします。

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> は、URL で使用するにはエンコードする必要があります。 そのため、 https://localhost では `https%3A%2F%2Flocalhost` を使用します)

    このチュートリアルでは、上記 URL のプレースホルダーの値を置換し、Web ブラウザーのアドレス バーに貼り付けることができます。 ユーザーは、Azure ログインを使用して認証するためにリダイレクトされます。 正常にログインすると、ブラウザーのアドレス バーに応答が表示されます。 応答は次の形式になります。

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. 応答から承認コードをキャプチャします。 このチュートリアルでは、Web ブラウザーのアドレス バーから承認コードをコピーして、次のスニペットで示すように POST 要求でトークン エンドポイントに渡すことができます。

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > この場合、\<REDIRECT-URI> をエンコードする必要はありません。
   > 
   > 

3. 応答はアクセス トークン (例: `"access_token": "<ACCESS_TOKEN>"`) および更新トークン (例: `"refresh_token": "<REFRESH_TOKEN>"`) を含む JSON オブジェクトです。 アプリケーションでは、Azure Data Lake Storage Gen1 にアクセスするときにアクセス トークンを使用し、アクセス トークンの有効期限が切れたときに別のアクセス トークンを取得するために更新トークンを使用します。

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. アクセス トークンの有効期限が切れたときは、次のスニペットで示すように更新トークンを使用して新しいアクセス トークンを要求できます。

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

対話型ユーザー認証の詳細については、 [承認コード付与フロー](/previous-versions/azure/dn645542(v=azure.100))に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、Azure Data Lake Storage Gen1 での認証に REST API を使用してサービス間認証を使用する方法を学習しました。 これで、REST API を使用して Azure Data Lake Storage Gen1 を使用する方法について説明した次の記事に進めるようになりました。

* [REST API を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-rest-api.md)
* [REST API を使用した Azure Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-rest-api.md)