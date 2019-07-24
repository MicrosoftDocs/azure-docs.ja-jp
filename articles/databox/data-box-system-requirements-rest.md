---
title: Microsoft Azure Data Box BLOB ストレージの要件 | Microsoft Docs
description: Azure Data Box BLOB ストレージ向けにサポートされている API、SDK、クライアント ライブラリのバージョンについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 5770cd9a4955013100b7e58698eed77da10c0583
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012258"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box BLOB ストレージの要件

この記事では、Data Box BLOB ストレージでサポートされている Azure の API、SDK、ツールのバージョンの一覧を示します。 Data Box BLOB ストレージでは、Azure と整合性のあるセマンティクスによって BLOB 管理機能が提供されます。 この記事では、Azure Storage サービスと Azure Data Box BLOB ストレージとの間で確認されている相違点もまとめています。

Data Box BLOB ストレージに接続する前にこの情報をよく確認し、必要に応じて再度参照することをお勧めします。


## <a name="storage-differences"></a>ストレージの相違点

|     機能                                             |     Azure Storage                                     |     Data Box BLOB ストレージ |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File ストレージ                                   |    クラウド ベースの SMB ファイル共有のサポート              |    サポートされていません      |
|    サービスによる保存データの暗号化                  |    256 ビット AES 暗号化                             |    256 ビット AES 暗号化 |
|    ストレージ アカウントの種類                                 |    汎用アカウントと Azure Blob Storage アカウント    |    汎用 v1 のみ|
|    BLOB 名                                            |    1,024 文字 (2,048 バイト)                     |    880 文字 (1,760 バイト)|
|    ブロック BLOB の最大サイズ                              |    4.75 TB (100 MB X 50,000 ブロック)                   |    Azure Data Box v 1.8 以降の場合に 4.75 TB (100 MB x 50,000 ブロック)。|
|    ページ BLOB の最大サイズ                               |    8 TB                                               |    1 TB (テラバイト)                   |
|    ページ BLOB のページ サイズ                                  |    512 バイト                                          |    4 KB                   |

## <a name="supported-api-versions"></a>サポートされる API バージョン

Azure Storage サービス API シリーズの次のバージョンが Data Box BLOB ストレージでサポートされています。

パブリック プレビュー リリース (Azure Data Box 1.8 以降)

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>サポートされている SDK バージョン

|     クライアント ライブラリ     |     Data Box BLOB ストレージでサポートされているバージョン     |     Link             |     エンドポイントの指定         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    6.2.0 から 8.7.0                         |    NuGet パッケージ: https://www.nuget.org/packages/WindowsAzure.Storage/ <br>GitHub リリース: https://github.com/Azure/azure-storage-net/releases                                                                      |    app.config ファイル                 |
|    Java                |    4.1.0 から 6.1.0                          |    Maven パッケージ: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>GitHub リリース: https://github.com/Azure/azure-storage-java/releases                                                      |    接続文字列の設定         |
|    Node.js             |    1.1.0 から 2.7.0                          |    NPM リンク: https://www.npmjs.com/package/azure-storage (例: "npm install azure-storage@2.7.0" を実行します)   <br>GitHub リリース: https://github.com/Azure/azure-storage-node/releases                            |    サービス インスタンスの宣言    |
|    C++                 |    2.4.0 から 3.1.0                          |    NuGet パッケージ: https://www.nuget.org/packages/wastorage.v140/   <br>GitHub リリース: https://github.com/Azure/azure-storage-cpp/releases                                                                            |    接続文字列の設定         |
|    PHP                 |    0.15.0 から 1.0.0                         |    GitHub リリース: https://github.com/Azure/azure-storage-php/releases   <br>Composer 経由でインストールする (下記参照)                                                                                                   |    接続文字列の設定         |
|    Python              |    0.30.0 から 1.0.0                         |    GitHub リリース: https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    サービス インスタンスの宣言    |
|    Ruby                |    0.12.1 から 1.0.1                         |    RubyGems パッケージ:<br>共通: https://rubygems.org/gems/azure-storage-common/   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/      <br>GitHub リリース: https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>サポートされている Azure クライアント ライブラリ

Data Box BLOB ストレージでは、クライアント ライブラリとエンドポイント サフィックスに特定の要件があります。 Data Box BLOB ストレージのエンドポイントは、最新バージョンの Azure Blob Storage REST API と完全に対応しているわけではありません。[Azure Data Box 1.8 以降でサポートされているバージョン](#supported-api-versions)に関するセクションをご覧ください。 したがって、ストレージのクライアント ライブラリについて、REST API と互換性のあるバージョンを把握しておく必要があります。

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 以降

| クライアント ライブラリ     |Data Box BLOB ストレージでサポートされているバージョン     | Link   |     エンドポイントの指定      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    NuGet パッケージ: https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>GitHub リリース: https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    app.config ファイル                 |
|    Java                |    6.1.0                                           |    Maven パッケージ: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub リリース: https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    接続文字列の設定         |
|    Node.js             |    2.7.0                                           |    NPM リンク: https://www.npmjs.com/package/azure-storage (実行: npm install azure-storage@2.7.0)   <br>GitHub リリース: https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    サービス インスタンスの宣言    |
|    C++                 |    3.1.0                                           |    NuGet パッケージ: https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>GitHub リリース: https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    接続文字列の設定         |
|    PHP                 |    1.0.0                                           |    GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>Composer 経由でインストールする (詳細については下記参照)                                                                                                             |    接続文字列の設定         |
|    Python              |    1.0.0                                           |    GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOB: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    サービス インスタンスの宣言    |
|    Ruby                |    1.0.1                                           |    RubyGems パッケージ:<br>共通: https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub リリース:<br>共通: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    接続文字列の設定         |



### <a name="install-php-client-via-composer---current"></a>Composer 経由で PHP クライアントをインストールする (現在)

Composer 経由でインストールするには (BLOB の例):
次のコードを含む composer.json という名前のファイルをプロジェクトのルートに作成します。

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

このプロジェクトのルートに `composer.phar` をダウンロードします。

実行: php composer.phar install。

### <a name="endpoint-declaration"></a>エンドポイントの宣言

Azure Data Box BLOB ストレージのエンドポイントには、リージョン名と Data Box ドメインの 2 つの部分があります。 Data Box BLOB ストレージ SDK の既定のエンドポイントは \<serial no. of the device>.microsoftdatabox.com です。  BLOB サービス エンドポイントの詳細については、[Data Box BLOB ストレージによる接続](data-box-deploy-copy-data-via-rest.md)に関するページを参照してください。
 
## <a name="examples"></a>例

### <a name="net"></a>.NET

Data Box BLOB ストレージの場合、エンドポイント サフィックスが `app.config` ファイルで指定されています。

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>次の手順

* [Azure Data Box をデプロイする](data-box-deploy-ordered.md)
