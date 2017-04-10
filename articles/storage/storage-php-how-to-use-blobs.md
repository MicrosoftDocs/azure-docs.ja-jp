---
title: "PHP から Blob Storage (オブジェクト ストレージ) を使用する方法 | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
documentationcenter: php
services: storage
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 2c356d7faafa8ef4591087b5b1f949b9374732be


---
# <a name="how-to-use-blob-storage-from-php"></a>PHP から BLOB ストレージを使用する方法
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要
Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

このガイドでは、Azure BLOB サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは PHP で記述され、[Azure SDK for PHP][download] を利用しています。 紹介するシナリオは、BLOB の**アップロード**、**一覧の取得**、**ダウンロード**、および**削除**です。 BLOB の詳細については、「 [次のステップ](#next-steps) 」のセクションを参照してください。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP アプリケーションの作成
Microsoft Azure BLOB サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から Microsoft Azure SDK for PHP のクラスを参照することのみです。 アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用するサービス機能は、PHP アプリケーション内でローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

## <a name="get-the-azure-client-libraries"></a>Azure クライアント ライブラリの入手
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>BLOB サービスにアクセスするようにアプリケーションを構成する
Azure BLOB サービス API を使用するには、次の要件があります。

1. [require_once] ステートメントを使ってオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、 **ServicesBuilder** クラスを参照する方法を示しています。

> [!NOTE]
> この記事の例では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。 ライブラリを手動でインストールした場合は、 `WindowsAzure.php` オートローダー ファイルを参照する必要があります。
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

下のすべてのサンプルに `require_once` ステートメントが入っていますが、サンプルの実行に必要なクラスのみが参照されます。

## <a name="set-up-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
Azure BLOB サービス クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 BLOB サービスの接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

ストレージ エミュレーターにアクセスする場合:

```php
UseDevelopmentStorage=true
```

いずれの Azure サービス クライアントを作成するにも、 **ServicesBuilder** クラスを使用する必要があります。 そのための方法は次のとおりです。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
  * 既定では&1; つの外部ソース (環境変数) のみサポートされています。
  * **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);
```

## <a name="create-a-container"></a>コンテナーを作成する
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**BlobRestProxy** オブジェクトの **createContainer** メソッドを使って BLOB コンテナーを作成できます。 コンテナーの作成時にコンテナーのオプションを設定できますが、この設定は必須ではありません (次の例では、コンテナーのアクセス コントロール リスト (ACL) とメタデータを設定する方法を示しています)。

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


// OPTIONAL: Set public access policy and metadata.
// Create container options object.
$createContainerOptions = new CreateContainerOptions();

// Set public access policy. Possible values are
// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
// CONTAINER_AND_BLOBS:
// Specifies full public read access for container and blob data.
// proxys can enumerate blobs within the container via anonymous
// request, but cannot enumerate containers within the storage account.
//
// BLOBS_ONLY:
// Specifies public read access for blobs. Blob data within this
// container can be read via anonymous request, but container data is not
// available. proxys cannot enumerate blobs within the container via
// anonymous request.
// If this value is not specified in the request, container data is
// private to the account owner.
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

**setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** の呼び出しにより、匿名の要求でコンテナーと BLOB データにアクセス可能になります。 **setPublicAccess(PublicAccessType::BLOBS_ONLY)** の呼び出しにより、匿名の要求で BLOB データにのみアクセス可能になります。 コンテナー ACL の詳細については、「[Set container ACL (REST API)][container-acl]」をご覧ください。

BLOB サービスのエラー コードの詳細については、「[BLOB サービスのエラー コード][error-codes]」をご覧ください。

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
BLOB としてファイルをアップロードするには、**BlobRestProxy->createBlockBlob** メソッドを使います。 この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。 次のコード例では、コンテナーが既に作成されていることを前提として、[fopen][fopen] を使用してストリームとしてファイルを開いています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

前の例では、ストリームとして BLOB をアップロードしていることに注意してください。 ただし BLOB は、[file\_get\_contents][file_get_contents] 関数などを使用して、文字列としてアップロードすることもできます。 前のサンプルを使用してこれを行うには `$content = fopen("c:\myfile.txt", "r");` を `$content = file_get_contents("c:\myfile.txt");` に変更します。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を列挙するには、**BlobRestProxy->listBlobs** メソッドを **foreach** ループで使って、結果をループ処理します。 次のコードでは、コンテナー内の各 BLOB の名前を出力として表示し、ブラウザーへの URI を表示します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


try    {
    // List blobs.
    $blob_list = $blobRestProxy->listBlobs("mycontainer");
    $blobs = $blob_list->getBlobs();

    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>BLOB をダウンロードする
BLOB をダウンロードするには、**BlobRestProxy->getBlob** メソッドを呼び出し、結果として返された **GetBlobResult** オブジェクトの **getContentStream** メソッドを呼び出します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

この例では、ストリーム リソースとして BLOB を取得していることに注意してください (既定の動作)。 ただし、[stream\_get\_contents][stream-get-contents] 関数を使用して、返されたストリームを文字列に変換できます。

## <a name="delete-a-blob"></a>BLOB を削除する
BLOB を削除するには、コンテナー名と BLOB 名を **BlobRestProxy->deleteBlob** に渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


try    {
    // Delete blob.
    $blobRestProxy->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>BLOB コンテナーを削除する
最後に、BLOB コンテナーを削除するには、コンテナー名を **BlobRestProxy->deleteContainer** に渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // Delete container.
    $blobRestProxy->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>次のステップ
これで、Azure BLOB サービスの基本を学習できました。さらに複雑なストレージ タスクについて学習するには、次のリンク先をご覧ください。

* [Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/)
* [PHP ブロック BLOB の例](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php)
* [PHP ページ BLOB の例](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)

詳細については、 [PHP デベロッパー センター](/develop/php/)も参照してください。

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents



<!--HONumber=Dec16_HO2-->


