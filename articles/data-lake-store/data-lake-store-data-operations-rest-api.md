---
title: REST API:Azure Data Lake Storage Gen1 に対するファイルシステム操作 | Microsoft Docs
description: WebHDFS REST API を使用して、Azure Data Lake Storage Gen1 に対するファイルシステム操作を実行します
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 51e0fb2ffa7b573ecfeda163d9ad99597ff735a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109206"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>REST API を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

この記事では、WebHDFS REST API と Data Lake Storage Gen1 REST API を使用して Azure Data Lake Storage Gen1 に対するファイルシステム操作を実行する方法について説明します。 REST API を使用して Data Lake Storage Gen1 に対するアカウント管理操作を実行する方法については、[REST API を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-rest-api.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

* **[cURL](https://curl.haxx.se/)** 。 この記事では、cURL を使用して、Data Lake Storage Gen1 アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
Azure Active Directory を使用した認証方法には 2 つあります。

* アプリケーションのエンドユーザー認証 (対話型) については、「[End-user authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md)」 (.NET SDK を使用した Data Lake Storage Gen1 に対するエンドユーザー認証) を参照してください。
* アプリケーションのサービス間認証 (非対話型) については、「[Service-to-service authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md)」 (.NET SDK を使用した Data Lake Storage Gen1 に対するサービス間認証) を参照してください。


## <a name="create-folders"></a>フォルダーを作成します。
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

上記のコマンドの \<`REDACTED`\> を、以前に取得した承認トークンに置き換えます。 このコマンドを実行すると、Data Lake Storage Gen1 アカウントのルート フォルダーの下に **mytempdir** という名前のディレクトリが作成されます。

操作が正常に完了すると、次のスニペットのような応答が表示されます。

```output
{"boolean":true}
```

## <a name="list-folders"></a>フォルダーを一覧表示する
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

上記のコマンドの \<`REDACTED`\> を、以前に取得した承認トークンに置き換えます。

操作が正常に完了すると、次のスニペットのような応答が表示されます。

```output
{
"FileStatuses": {
    "FileStatus": [{
        "length": 0,
        "pathSuffix": "mytempdir",
        "type": "DIRECTORY",
        "blockSize": 268435456,
        "accessTime": 1458324719512,
        "modificationTime": 1458324719512,
        "replication": 0,
        "permission": "777",
        "owner": "<GUID>",
        "group": "<GUID>"
    }]
}
}
```

## <a name="upload-data"></a>データのアップロード
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

上記の構文の **-T** パラメーターは、ファイルのアップロード先となる場所です。

出力は次のスニペットのようになります。
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>データの読み取り
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

Data Lake Storage Gen1 アカウントからのデータの読み取りは、2 段階のプロセスとなります。

* まず、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`に対して GET 要求を送信します。 この呼び出しにより、次の GET 要求の送信先が返されます。
* 次に、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`に対して GET 要求を送信します。 この呼び出しにより、ファイルの内容が表示されます。

ただし、1 番目の手順と 2 番目の手順の間に入力パラメーターの違いはないため、 `-L` パラメーターを使用して最初の要求を送信できます。 `-L` オプションにより、基本的に 2 つの要求が 1 つの要求に結合され、新しい場所で cURL により要求がやり直されます。 最後に、次のスニペットに示すように、すべての要求呼び出しの出力が表示されます。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

次のスニペットのような出力が表示されます。

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>ファイル名を変更する
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

ファイルの名前を変更するには、次の cURL コマンドを使用します。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

次のスニペットのような出力が表示されます。

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>ファイルを削除する
この操作は、 [ここ](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用して、ファイルを削除します。 **\<yourstorename>** を、自分の Data Lake Storage Gen1 アカウント名に置き換えます。

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

出力は次のように表示されます。

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>次のステップ
* [REST API を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-rest-api.md)

## <a name="see-also"></a>関連項目
* [Azure Data Lake Storage Gen1 REST API リファレンス](/rest/api/datalakestore/)
* [Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)