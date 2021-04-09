---
title: 認証 ‐ Data Lake Storage Gen1 で Azure AD を使用する
description: Azure Data Lake Storage Gen1 で Azure Active Directory を使用して認証する方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106571"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Azure Data Lake Storage Gen1 での Azure Active Directory を使用した認証

Azure Data Lake Storage Gen1 では、認証するために Azure Active Directory を使用します。 Data Lake Storage Gen1 と連携して動作するアプリケーションを作成するときは、Azure Active Directory (Azure AD) に対するアプリケーションの認証方法を事前に決めておく必要があります。

## <a name="authentication-options"></a>認証オプション

* **エンドユーザー認証** - Data Lake Storage Gen1 での認証に、エンド ユーザーの Azure 資格情報が使用されます。 Data Lake Storage Gen1 と組み合わせて動作するように作成されたアプリケーションでは、これらのユーザー資格情報を求めるメッセージが表示されます。 そのため、この認証メカニズムは *対話型* であり、アプリケーションはログインしたユーザーのコンテキストで実行されます。 詳細と手順については、[Data Lake Storage Gen1 でのエンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)に関するページをご覧ください。

* **サービス間認証** - Data Lake Storage Gen1 でアプリケーションが自身を認証するようにするには、このオプションを使用します。 このような場合は、Azure Active Directory (AD) アプリケーションを作成して、Data Lake Storage Gen1 での認証に、Azure AD アプリケーションのキーを使用します。 そのため、この認証メカニズムは *非対話型* です。 詳細と手順については、[Data Lake Storage Gen1 でのサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)に関するページをご覧ください。

次の表は、Data Lake Storage Gen1 でエンドユーザー認証メカニズムとサービス間認証メカニズムがどのようにサポートされているかを示しています。 表の見方は次のとおりです。

* ✔* マークは、その認証オプションがサポートされており、認証オプションの使用方法を説明する記事へのリンクがあることを表します。 
* ✔ マークは、その認証オプションがサポートされていることを表します。 
* 空欄は、その認証オプションがサポートされていないことを表します。


|右の項目とともに以下の認証オプションを使用                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|エンドユーザー (MFA なし\*\*)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)** (非推奨)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|エンドユーザー (MFA あり)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|サービス間 (クライアント キーを使用)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|サービス間 (クライアント証明書を使用) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* <b>✔\*</b> マークをクリックしてください。リンク先に移動します。</i><br>
<i>** MFA は、多要素認証 (Multi-Factor Authentication) の意味です。</i>

Azure Active Directory を認証に使用する方法の詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
* [サービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)