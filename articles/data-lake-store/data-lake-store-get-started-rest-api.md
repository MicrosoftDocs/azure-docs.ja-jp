---
title: REST を使用した Azure Data Lake Storage Gen1 アカウントの管理
description: WebHDFS REST API を使用し、Azure Data Lake Storage Gen1 でアカウント管理操作を実行します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 13467a51b2a06dbc0ca0ec5eadd139fde8b82ad0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103494"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>REST API を使用した Azure Data Lake Storage Gen1 に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

この記事では、REST API を使用して Azure Data Lake Storage Gen1 に対するアカウント管理操作を実行する方法について説明します。 アカウント管理操作には、Data Lake Storage Gen1 アカウントの作成、Data Lake Storage Gen1 アカウントの削除などがあります。Python を使用して Data Lake Storage Gen1 に対するファイルシステム操作を実行する方法については、「[Filesystem operations on Data Lake Storage Gen1 using REST API](data-lake-store-data-operations-rest-api.md)」 (REST API を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作) をご覧ください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **[cURL](https://curl.haxx.se/)** 。 この記事では、cURL を使用して、Data Lake Storage Gen1 アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
Azure Active Directory を使用した認証方法には 2 つあります。

* アプリケーションのエンドユーザー認証 (対話型) については、「[End-user authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md)」 (.NET SDK を使用した Data Lake Storage Gen1 に対するエンドユーザー認証) を参照してください。
* アプリケーションのサービス間認証 (非対話型) については、「[Service-to-service authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md)」 (.NET SDK を使用した Data Lake Storage Gen1 に対するサービス間認証) を参照してください。


## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントを作成する
この操作は、 [ここ](/rest/api/datalakestore/accounts/create)で定義されている REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstoragegen1name>** を、自分の Data Lake Storage Gen1 名に置き換えます。

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

上記のコマンドで、\<`REDACTED`\> を以前に取得した承認トークンに置き換えます。 このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **input.json** ファイルに含まれています。 input.json ファイルの内容は、次のスニペットのようになります。

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの削除
この操作は、 [ここ](/rest/api/datalakestore/accounts/delete)で定義されている REST API 呼び出しをベースにしています。

Data Lake Storage Gen1 アカウントを削除するには、次の cURL コマンドを使用します。 **\<yourstoragegen1name>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

次のスニペットのような出力が表示されます。

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>次のステップ
* [REST API を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作](data-lake-store-data-operations-rest-api.md)。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Storage Gen1 REST API リファレンス](/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)