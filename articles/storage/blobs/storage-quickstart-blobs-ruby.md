---
title: Azure クイック スタート - Ruby を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Ruby 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: seguler
ms.openlocfilehash: 47143a76c2b57bc889b74f29d709785d0c19451a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008221"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>クイック スタート:Ruby を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、Ruby を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次の追加の前提条件がインストールされていることを確認してください。

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [Ruby 向けの Azure Storage ライブラリ](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage) (rubygem パッケージを使用) 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)は、基本的な Ruby アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Ruby のサンプル アプリケーションを開くには、storage-blobs-ruby-quickstart フォルダーを検索し、example.rb ファイルを開きます。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
アプリケーションでは、ストレージ アカウント名とアカウント キーを指定して、お使いのアプリケーションの `BlobService` インスタンスを作成する必要があります。 お使いの IDE のソリューション エクスプローラーから `example.rb` ファイルを開きます。 **accountname** および **accountkey** の値を、ご自分のアカウント名とキーに置き換えます。 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、"ドキュメント" フォルダーにテスト ファイルを作成します。 サンプル プログラムは、Blob Storage にテスト ファイルをアップロードし、コンテナー内の BLOB を一覧表示し、新しい名前を付けてファイルをダウンロードします。 

サンプルを実行します。 次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
任意のキーを押して続行すると、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 続行する前に、"ドキュメント" フォルダーで 2 つのファイルをチェックします。 それらを開いて、同じであるかどうか確認します。

[Azure Storage Explorer](https://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、example.rb ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する
最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* Azure Storage の **BlobService** オブジェクトのインスタンスを作成して、接続資格情報を設定します。 
* アクセスしているコンテナーを表す **Container** オブジェクトを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

Cloud BLOB コンテナーを作成した後は、関心がある特定の BLOB を参照する **Block** BLOB オブジェクトを作成して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、Azure Storage クライアントのインスタンスを設定して、BLOB サービス オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、ローカル ドライブ上でディレクトリ名とファイル名を結合することにより、ファイルの完全なパスを取得します。 **create\_block\_blob()** メソッドを使用して、指定したパスにファイルをアップロードできます。 

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを **file\_path\_to\_file** として、BLOB の名前を **local\_file\_name** として格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

ブロック BLOB のコンテンツの部分更新を実行するには、**create\_block\_list()** メソッドを使用します。 ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 追加 BLOB は、単一のライター モデルで使用する必要があります。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**list\_blobs()** メソッドを使用して、コンテナー内のファイルの一覧を取得できます。 次のコードは、BLOB の一覧を取得し、ループ処理して、コンテナー内に見つかった BLOB の名前を表示します。  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>BLOB のダウンロード

**get\_blob()** メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。 次のコードは、前のセクションでアップロードされた BLOB をダウンロードします。 "_DOWNLOADED" は、ローカル ディスク上で両方のファイルを確認できるように、BLOB の名前にサフィックスとして追加されます。 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイックスタートでアップロードした BLOB が不要になった場合は、**delete\_container()** メソッドを使ってコンテナー全体を削除できます。 作成されたファイルが不要になった場合は、**delete\_blob()** メソッドを使ってファイルを削除します。

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>BLOB を使用する Ruby アプリケーションを開発するためのリソース

Blob Storage を使用する Ruby 開発については、以下の追加リソースを参照してください。

- GitHub で Azure Storage 用の [Ruby クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-ruby)を確認し、ダウンロードします。
- Ruby クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob)を確認します。

## <a name="next-steps"></a>次の手順
 
このクイックスタートでは、Ruby を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](./storage-ruby-how-to-use-blob-storage.md)


Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
