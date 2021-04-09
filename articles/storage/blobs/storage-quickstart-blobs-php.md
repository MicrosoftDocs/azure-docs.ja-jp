---
title: Azure クイック スタート - PHP を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: PHP を使用して Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します。 Azure Blob Storage 内のコンテナーのブロック BLOB のアップロード、ダウンロード、および一覧表示を行います。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: af35927abfa491891dffe2c2397d6daf8c1ccde2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543121"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>PHP を使用して Azure Blob Storage との間でオブジェクトを転送する
このクイック スタートでは、PHP を使って、Azure Blob Storage のコンテナー内のブロック BLOB をアップロード、ダウンロード、および一覧表示する方法を説明します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次の追加の前提条件がインストールされていることを確認してください。

* [PHP](https://php.net/downloads.php)
* [Azure Storage SDK for PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイック スタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git)は、基本的な PHP アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 PHP のサンプル アプリケーションを開くには、storage-blobs-php-quickstart フォルダーを検索し、phpqs.php ファイルを開きます。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
アプリケーションでは、ストレージ アカウント名とアカウント キーを指定して、お使いのアプリケーションの **BlobRestProxy** インスタンスを作成する必要があります。 アプリケーションを実行しているローカル マシンの環境変数内にこれらの識別子を格納することをお勧めします。 環境変数を作成するオペレーティング システムに応じて、以下のいずれかの例を使用します。 **youraccountname** および **youraccountkey** の値を、自分のアカウント名とキーに置き換えます。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>環境を構成する
ローカル git フォルダーのフォルダーを、PHP サーバーによって処理されているディレクトリに配置します。 その後、コマンド プロンプトを開き、同じディレクトリを対象にして、「`php composer.phar install`」と入力します。

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、"." フォルダーにテスト ファイルを作成します。 サンプル プログラムは、Blob Storage にテスト ファイルをアップロードし、コンテナー内の BLOB を一覧表示し、新しい名前を付けてファイルをダウンロードします。 

サンプルを実行します。 次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
表示されるボタンを押すと、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 続行する前に、サーバーのフォルダーで 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。

[Azure Storage Explorer](https://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、example.rb ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する
最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* Azure Storage の **BlobRestProxy** オブジェクトのインスタンスを作成して、接続資格情報をセットアップします。 
* お使いのストレージ アカウント内の BLOB サービスを参照する **BlobService** オブジェクトを作成します。 
* アクセスしているコンテナーを表す **Container** オブジェクトを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

**blobClient** コンテナー オブジェクトを作成した後は、関心がある特定の BLOB を参照する **Block** BLOB オブジェクトを作成することができます。 その後、アップロード、ダウンロード、コピーなどの操作を行うことができます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、Azure Storage クライアントのインスタンスをセットアップして、BLOB サービス オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、ローカル ドライブ上でディレクトリ名とファイル名を結合することにより、ファイルの完全なパスを取得します。 **createBlockBlob()** メソッドを使用して、指定したパスにファイルをアップロードできます。 

サンプル コードでは、ローカル ファイルを受け取り、Azure にアップロードします。 ファイルは **myfile** として格納されており、BLOB の名前はコードでは **fileToUpload** となっています。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

ブロック BLOB のコンテンツの部分更新を実行するには、**createblocklist()** メソッドを使用します。 ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 追加 BLOB は、単一のライター モデルで使用する必要があります。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**listBlobs()** メソッドを使用して、コンテナー内のファイルの一覧を取得できます。 次のコードは、BLOB の一覧を取得し、ループ処理して、コンテナー内に見つかった BLOB の名前を表示します。  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>BLOB のコンテンツを取得する

**getBlob()** メソッドを使用して、BLOB のコンテンツを取得します。 次のコードは、前のセクションでアップロードされた BLOB のコンテンツを表示します。

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイック スタートでアップロードした BLOB が不要になった場合は、**deleteContainer()** メソッドを使用してコンテナー全体を削除できます。 作成したファイルが不要になった場合は、**deleteBlob()** メソッドを使用してファイルを削除します。

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>BLOB を使用する PHP アプリケーションを開発するためのリソース

Blob Storage を使用する PHP 開発については、以下の追加リソースを参照してください。

- GitHub で Azure Storage 用の [PHP クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-php)を確認し、ダウンロードしてインストールします。
- PHP クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob)を確認します。

## <a name="next-steps"></a>次のステップ
 
このクイック スタートでは、PHP を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 PHP の操作の詳細を学習するには、PHP デベロッパー センターに進みます。

> [!div class="nextstepaction"]
> [PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)


Storage Explorer と BLOB について詳しくは、「[Storage Explorer を使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。