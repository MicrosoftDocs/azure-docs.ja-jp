---
title: "Azure SDK for Node.js で Azure Data Lake Store の使用を開始する | Microsoft Docs"
description: "Node.js を使用して Data Lake Store アカウントとファイル システムを操作する方法について説明します。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 091ab246826c96b9d816c87b27014c1e54039429


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Azure SDK for Node.js で Azure Data Lake Store の使用を開始する
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

> [!NOTE]
> 大量のデータ (サイズの大きいファイル、多数のファイル、またはその両方) をアップロードおよびダウンロードする場合は、[Python SDK](data-lake-store-get-started-python.md)、[.NET SDK](data-lake-store-get-started-net-sdk.md)、または [Azure PowerShell](data-lake-store-get-started-powershell.md) を使用することをお勧めします。 これらのオプションではスレッドを複数使用してデータ移動が並列化されるため、パフォーマンスが改善されます。
> 
> 

Azure SDK for Node.js を使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、[Data Lake Store の概要](data-lake-store-overview.md)に関する記事をご覧ください。 現在 SDK では以下のものがサポートされています

* **Node.js のバージョン: 0.10.0 以降**
* **アカウント用の REST API のバージョン: 2015-10-01-preview**
* **ファイルシステム用の REST API のバージョン: 2015-10-01-preview**

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Active Directory アプリケーションを作成する**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Store アプリケーションを認証します。 Azure AD での認証方法には、**エンドユーザー認証**と**サービス間認証**があります。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="how-to-install"></a>インストール方法
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する
以下のスニペットは、Azure AD を使用した Data Lake Store での&2; つの別々の認証方法を示したものです。 Data Lake Store での認証に利用できるさまざまな方法の詳細については、[Data Lake Store での Azure Active Directory を使用した認証](data-lake-store-authenticate-using-active-directory.md)に関するページを参照してください。

次のスニペットには、Azure AD ドメイン名や Azure AD アプリのクライアント ID などの入力値も必要です。これらの詳細情報は、作成した Azure AD アプリケーションからすべて取得できます。また、アプリケーションの詳細は上記のリンク先にも記載されています。

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Data Lake Store クライアントを作成する
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>コンテンツを持つファイルを作成する
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>ファイルとフォルダーの一覧を取得する
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>関連項目
* [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK for Node.js - Data Lake Analytics の管理](https://www.npmjs.com/package/azure-arm-datalake-analytics)




<!--HONumber=Feb17_HO1-->


