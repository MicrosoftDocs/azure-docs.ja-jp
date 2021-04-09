---
title: 'クイックスタート: Azure Blob Storage クライアント ライブラリ - Ruby'
description: Azure Blob Storage にストレージ アカウントとコンテナーを作成します。 Ruby 用ストレージ クライアント ライブラリを使用して、BLOB の作成、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96781098"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>クイックスタート: Ruby 用 Azure Blob Storage クライアント ライブラリ

Ruby を使用して、Microsoft Azure Blob Storage で BLOB の作成、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行う方法について学習します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次の追加の前提条件がインストールされていることを確認してください。

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby 用 Azure Storage ライブラリ](https://github.com/azure/azure-storage-ruby) ([RubyGem パッケージを使用](https://rubygems.org/gems/azure-storage-blob))

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git)は、基本的な Ruby アプリケーションです。

アプリケーションのコピーを開発環境にダウンロードするには、[Git](https://git-scm.com/) を使います。 このコマンドでは、リポジトリをローカル コンピューターにクローンします。

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

*storage-blobs-ruby-quickstart* フォルダーに移動し、コード エディターで *example.rb* ファイルを開きます。

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

ストレージ アカウント名とアカウント キーを指定して、お使いのアプリケーションの [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) インスタンスを作成します。

*example.rb* ファイル内の次のコードでは、新しい [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) オブジェクトをインスタンス化します。 *accountname* および *accountkey* の値を、ご自分のアカウント名とキーに置き換えます。

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルでは、Blob Storage にコンテナーを作成し、コンテナーに新しい BLOB を作成します。さらに、コンテナー内の BLOB を一覧表示し、その BLOB をローカル ファイルにダウンロードします。

サンプルを実行します。 アプリケーションを実行した出力の例を次に示します。

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Enter キーを押して続行すると、サンプル プログラムによってストレージ コンテナーとローカル ファイルが削除されます。 続行する前に、"*ドキュメント*" フォルダーにファイルがダウンロードされていることを確認してください。

[Azure Storage Explorer](https://storageexplorer.com) を使って、対象のストレージ アカウント内のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。

ファイルを確認した後、Enter キーを押してテスト ファイルを削除し、デモを終了します。 *example.rb* ファイルを開いて、コードを確認します。

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、そのしくみを理解できるように、サンプル コードを見ていきましょう。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に行うことは、Blob Storage へのアクセスと管理に使用されるオブジェクトのインスタンスを作成することです。 これらのオブジェクトは、相互に関連した形で構築されます。 各オブジェクトは、一覧内の次のオブジェクトによって使用されます。

- Azure Storage の [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) オブジェクトのインスタンスを作成して、接続資格情報を設定します。
- アクセスするコンテナーを表す [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) オブジェクトを作成します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

コンテナー オブジェクトを作成した後は、関心がある特定の BLOB を参照する [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) BLOB オブジェクトを作成することができます。 この [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) オブジェクトを使用して、BLOB を作成、ダウンロード、およびコピーします。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」 (コンテナー、BLOB、およびメタデータの名前付けと参照) を参照してください。

次のサンプル コードでは、次の操作を行います。

- 新しいコンテナーを作成する
- BLOB がパブリックになるように、コンテナーにアクセス許可を設定する。 コンテナー名は *quickstartblobs* で、一意の ID が追加されます。

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>コンテナー内に BLOB を作成する

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 BLOB を作成するには、[create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) メソッドを呼び出して、BLOB のデータを渡します。

次の例では、先ほど作成したコンテナー内に、*QuickStart_* の後に一意の ID が続き、ファイル拡張子が *.txt* の BLOB を作成します。

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

ブロック BLOB の最大サイズは 4.7 TB であり、スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS 仮想マシンのバックアップ用の VHD ファイルに使われます。 追加 BLOB は、通常、ファイルに書き込んでからさらに情報を追加し続ける場合などの、ログ記録に使用されます。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) メソッドを使用してコンテナー内のファイルの一覧を取得します。 次のコードでは、BLOB の一覧を取得し、その名前を表示します。

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>BLOB をダウンロードする

[get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。 次のコードでは、前のセクションで作成した BLOB をダウンロードします。

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする

Blob が不要になった場合は、[delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) を使用して削除します。 [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) メソッドを使用して、コンテナー全体を削除します。 コンテナーを削除すると、コンテナーに格納されている BLOB もすべて削除されます。

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>BLOB を使用する Ruby アプリケーションを開発するためのリソース

Ruby 開発については、以下の追加リソースを参照してください。

- GitHub で Azure Storage 用の [Ruby クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-ruby)を確認し、ダウンロードします。
- Ruby クライアント ライブラリを使用して記述された [Azure のサンプル](/samples/browse/?products=azure&languages=ruby)を確認します。
- [サンプル:Ruby での Azure Storage 操作の概要](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Ruby を使って Azure Blob Storage とローカル ディスクとの間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、ストレージ アカウントの概要に進んでください。

> [!div class="nextstepaction"]
> [ストレージ アカウントの概要](../common/storage-account-overview.md)

Storage Explorer と BLOB について詳しくは、「[Storage Explorer を使用して Azure Blob Storage リソースを管理する](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。
