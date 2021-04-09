---
title: REST - サービス間認証 - Data Lake Storage Gen1 - Azure
description: REST API を利用し、Azure Data Lake Storage Gen1 と Azure Active Directory でサービス間認証を行う方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4947bab58a6d23bcc3e3212a9e3e7dc0c4fa8578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106707"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Azure Data Lake Storage Gen1 に対する REST API を使用したサービス間認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

この記事では、REST API を使用して、Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します。 REST API を使用した Data Lake Store Gen1 に対するエンド ユーザー認証については、「[End-user authentication with Data Lake Storage Gen1 using REST API](data-lake-store-end-user-authenticate-rest-api.md)」(Data Lake Storage Gen1 での REST API を使用したエンドユーザー認証) を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Storage Gen1 に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

## <a name="service-to-service-authentication"></a>サービス間認証

このシナリオでは、操作を実行するための独自の資格情報をアプリケーションが提供します。 そのために、次のスニペットに示すように POST 要求を発行する必要があります。

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

この要求の出力には、後で REST API 呼び出しで渡す認証トークン (以下の出力の `access-token` で示される) が取り込まれます。 テキスト ファイルに認証トークンを保存します。このファイルは、Data Lake Store Gen1 に対して REST 呼び出しを行うときに必要になります。

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

この記事では、 **非対話型** のアプローチを使用します。 非対話型 (サービス間呼び出し) の詳細については、 [資格情報を使用したサービス間呼び出し](/previous-versions/azure/dn645543(v=azure.100))に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、サービス間認証を使って、REST API を使用して Data Lake Storage Gen1 で認証する方法を説明しました。 これで、REST API を使用して Data Lake Storage Gen1 を使用する方法について説明した次の記事に進むことができます。

* [REST API を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-rest-api.md)
* [REST API を使用した Azure Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-rest-api.md)