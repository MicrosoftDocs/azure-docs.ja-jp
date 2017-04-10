---
title: "REST API で Data Lake Store の使用を開始する | Microsoft Docs"
description: "WebHDFS REST API を使用して Data Lake Store に対する操作を実行する"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 33574b0c1f023a8a5f83b1bf06f0523623891757
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>REST API で Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

この記事では、WebHDFS REST API と Data Lake Store REST API を使用して、アカウントの管理と、Azure Data Lake Store に対するファイル システム操作を実行する方法について説明します。 Azure Data Lake Store では、アカウント管理操作用の独自の REST API を公開しています。 ただし、Data Lake Store は、HDFS および Hadoop エコシステムと互換性があるため、WebHDFS REST API を使用したファイル システム操作もサポートしています。

> [!NOTE]
> Data Lake Store の REST API サポートの詳細については、「 [Azure Data Lake Store REST API リファレンス](https://msdn.microsoft.com/library/mt693424.aspx)」を参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Active Directory アプリケーションを作成する**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Store アプリケーションを認証します。 Azure AD での認証方法には、**エンドユーザー認証**と**サービス間認証**があります。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。
* [cURL](http://curl.haxx.se/)。 この記事では、cURL を使用して、Data Lake Store アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
Azure Active Directory を使用した認証方法には&2; つあります。

### <a name="end-user-authentication-interactive"></a>エンド ユーザー認証 (対話型)
このシナリオでは、アプリケーションはユーザーにログインを求め、すべての操作はユーザーのコンテキストで実行されます。 対話型認証のためには次の手順を実行します。

1. アプリケーションでユーザーを次の URL にリダイレクトします。
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> は、URL で使用できるようにエンコードする必要があります。 そのため、https://localhost の場合、`https%3A%2F%2Flocalhost` を使用します。
   > 
   > 
   
    このチュートリアルでは、上記 URL のプレースホルダーの値を置換し、Web ブラウザーのアドレス バーに貼り付けることができます。 ユーザーは、Azure ログインを使用して認証するためにリダイレクトされます。 正常にログインすると、ブラウザーのアドレス バーに応答が表示されます。 応答は次の形式になります。
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. 応答から承認コードをキャプチャします。 このチュートリアルでは、Web ブラウザーのアドレス バーから承認コードをコピーして、次に示すように POST 要求でトークン エンドポイントに渡すことができます。
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > この場合、\<REDIRECT-URI> をエンコードする必要はありません。
   > 
   > 
3. 応答は、アクセス トークン (例: `"access_token": "<ACCESS_TOKEN>"`) および更新トークン (例: `"refresh_token": "<REFRESH_TOKEN>"`) を含む JSON オブジェクトです。 アプリケーションでは、Azure Data Lake Store にアクセスするときにアクセス トークンを使用し、アクセス トークンの有効期限が切れたときに別のアクセス トークンを取得するために更新トークンを使用します。
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. アクセス トークンの有効期限が切れたときは、次のように更新トークンを使用して新しいアクセス トークンを要求できます。
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

対話型ユーザー認証の詳細については、 [承認コード付与フロー](https://msdn.microsoft.com/library/azure/dn645542.aspx)に関するページを参照してください。

### <a name="service-to-service-authentication-non-interactive"></a>サービス間認証 (非対話型)
このシナリオでは、操作を実行するための独自の資格情報をアプリケーションが提供します。 このアプローチの場合は、次に示すように POST 要求を発行する必要があります。 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

この要求の出力には、後で REST API 呼び出しで渡す認証トークン (以下の出力の `access-token` で示される) が取り込まれます。 この認証トークンをテキスト ファイルに保存します。この記事の後半で必要となります。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

この記事では、 **非対話型** のアプローチを使用します。 非対話型 (サービス間呼び出し) の詳細については、 [資格情報を使用したサービス間呼び出し](https://msdn.microsoft.com/library/azure/dn645543.aspx)に関するページを参照してください。

## <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
この操作は、 [ここ](https://msdn.microsoft.com/library/mt694078.aspx)で定義されている REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

上記のコマンドで、\<`REDACTED`\> を以前に取得した承認トークンに置き換えます。 このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **input.json** ファイルに含まれています。 input.json ファイルの内容は、次のようになります。

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="create-folders-in-a-data-lake-store-account"></a>Data Lake Store アカウントでフォルダーを作成する
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

上記のコマンドで、\<`REDACTED`\> を以前に取得した承認トークンに置き換えます。 このコマンドを実行すると、Data Lake Store アカウントのルート フォルダーの下に **mytempdir** という名前のディレクトリが作成されます。

操作が正常に完了すると、次のような応答が表示されます。

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Data Lake Store アカウントのフォルダーを一覧する
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

上記のコマンドで、\<`REDACTED`\> を以前に取得した承認トークンに置き換えます。

操作が正常に完了すると、次のような応答が表示されます。

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
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Data Lake Store アカウントにデータをアップロードする
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

WebHDFS REST API を使用したデータのアップロードは、次に説明するように&2; 段階のプロセスとなります。

1. アップロードするファイル データを送信することなく、HTTP PUT 要求を送信します。 次のコマンドで、**\<yourstorename>** を実際の Data Lake Store 名に置き換えます。
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    このコマンドの出力には、次に示すように、一時的なリダイレクト URL が含まれます。
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. 次に、応答内の **Location** プロパティに関して一覧表示された URL に対して別の HTTP PUT 要求を送信する必要があります。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    出力は次のようになります。
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Data Lake Store アカウントからデータを読み取る
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

Data Lake Store アカウントからのデータの読み取りは、2 段階のプロセスとなります。

* まず、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`に対して GET 要求を送信します。 これにより、次の GET 要求を送信する場所が返されます。
* 次に、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`に対して GET 要求を送信します。 これにより、ファイルの内容が表示されます。

ただし、1 番目の手順と&2; 番目の手順の間に入力パラメーターの違いはないため、 `-L` パラメーターを使用して最初の要求を送信できます。 `-L` オプションは、基本的に&2; つの要求を&1; つの要求に結合し、新しい場所で cURL により要求をやり直します。 最後に、次のように、すべての要求呼び出しの出力が表示されます。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

次のような出力が表示されます。

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Data Lake Store アカウントのファイルの名前を変更する
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

ファイルの名前を変更するには、次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

次のような出力が表示されます。

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Data Lake Store アカウントからファイルを削除する
この操作は、 [ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用して、ファイルを削除します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

出力は次のように表示されます。

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store アカウントを削除する
この操作は、 [ここ](https://msdn.microsoft.com/library/mt694075.aspx)で定義されている REST API 呼び出しをベースにしています。

Data Lake Store アカウントを削除するには、次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

出力は次のように表示されます。

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)


