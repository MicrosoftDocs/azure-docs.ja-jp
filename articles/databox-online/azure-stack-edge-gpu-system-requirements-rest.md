---
title: Microsoft Azure Stack Edge BLOB ストレージの要件 | Microsoft Docs
description: Azure Stack Edge BLOB ストレージ向けにサポートされている API、SDK、クライアント ライブラリのバージョンについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575857"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge BLOB ストレージの要件

この記事では、Azure Stack Edge BLOB ストレージでサポートされている Azure の API、Azure クライアント ライブラリ、およびツールのバージョンの一覧を示します。 Azure Stack Edge BLOB ストレージには、Azure と整合性のあるセマンティクスによって BLOB 管理機能が用意されています。 この記事では、Azure Storage サービスと Azure Stack Edge BLOB ストレージとの間で確認されている相違点もまとめています。

Azure Stack Edge BLOB ストレージに接続する前にこの情報をよく確認し、必要に応じて再度参照することをお勧めします。

## <a name="storage-differences"></a>ストレージの相違点

|     機能                                             |     Azure Storage                                     |     Azure Stack Edge BLOB ストレージ |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File ストレージ                                   |    クラウド ベースの SMB ファイル共有のサポート              |    サポートされていません      |
|    ストレージ アカウントの種類                                 |    汎用アカウントと Azure Blob ストレージ アカウント    |    汎用 v1 のみ|
|    BLOB 名                                            |    1,024 文字 (2,048 バイト)                     |    880 文字 (1,760 バイト)|
|    ブロック BLOB の最大サイズ                              |    4.75 TB (100 MB X 50,000 ブロック)                   |    Azure Stack Edge の場合、4.75 TB (100 MB x 50,000 ブロック)|
|    ページ BLOB の最大サイズ                               |    8 TB                                               |    1 TB (テラバイト)                   |
|    ページ BLOB のページ サイズ                                  |    512 バイト                                          |    4 KB                   |

## <a name="supported-api-versions"></a>サポートされる API バージョン

Azure Storage サービス API の次のバージョンが Azure Stack Edge BLOB ストレージでサポートされています。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 以降

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>サポートされている Azure クライアント ライブラリ

Azure Stack Edge ストレージの場合、クライアント ライブラリとエンドポイント サフィックスに特定の要件があります。 Azure Stack Edge BLOB ストレージのエンドポイントは、Azure Blob Storage REST API の最新バージョンと完全に対応しているわけではありません。[Azure Stack Edge でサポートされている API のバージョン](#supported-api-versions)に関するセクションをご覧ください。 したがって、ストレージのクライアント ライブラリについて、REST API と互換性のあるバージョンを把握しておく必要があります。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 以降

Azure Stack Edge BLOB ストレージの場合、次の Azure クライアント ライブラリ バージョンがサポートされています。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Composer 経由で PHP クライアントをインストールする (現在)

Composer 経由で PHP クライアントをインストールするには、次のようにします。

1. 次のコードを含む composer.json という名前のファイルをプロジェクトのルートに作成します (例では、Azure Storage Blob サービスが使用されています)。

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. このプロジェクトのルートに `composer.phar` をダウンロードします。

3. 実行: php composer.phar install。


## <a name="endpoint-declaration"></a>エンドポイントの宣言

Azure Stack Edge BLOB ストレージ SDK では、エンドポイント サフィックス (`<device serial number>.microsoftdatabox.com`) によって Azure Stack Edge ドメインが識別されます。 Blob service エンドポイントの詳細については、「[Azure Stack Edge Pro GPU でストレージ アカウントを使用してデータを転送する](azure-stack-edge-j-series-deploy-add-storage-accounts.md)」を参照してください。


## <a name="examples"></a>例

### <a name="net"></a>.NET

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが `app.config` ファイルで指定されています。

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが宣言のインスタンスで指定されています。

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Azure Stack Edge BLOB ストレージの場合、エンドポイント サフィックスが接続文字列の設定で指定されています。

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>次の手順

* [GPU 搭載の Azure Stack Edge Pro の配置を準備する](azure-stack-edge-gpu-deploy-prep.md)
