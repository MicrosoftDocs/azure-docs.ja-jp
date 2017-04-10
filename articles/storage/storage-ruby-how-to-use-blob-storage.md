---
title: "Ruby から Blob Storage (オブジェクト ストレージ) を使用する方法 | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
services: storage
documentationcenter: ruby
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: c63758346ebd8fa3d4c43e59c84bc2a6935e4bf7
ms.openlocfilehash: 7f7d0c52b2b50a360711477e8e0eafc07ddcf374


---
# <a name="how-to-use-blob-storage-from-ruby"></a>Ruby から BLOB ストレージを使用する方法
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要
Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

このガイドでは、BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Ruby API を使用して記述されています。 紹介するシナリオは、BLOB の**アップロード、一覧の取得、ダウンロード**、**削除**です。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成
Ruby アプリケーションを作成します。 手順については、「[Azure VM での Ruby on Rails Web アプリケーション](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)」をご覧ください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure Storage を使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する
1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする
任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** と **AZURE\_STORAGE\_ACCESS_KEY** を読み取ります。 これらの環境変数が設定されていない場合は、 **Azure::Blob::BlobService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の [設定] ブレードで、 **[アクセス キー]**をクリックします。
4. 表示される [アクセス キー] ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

Azure ポータルでクラシック ストレージ アカウントからこれらの値を取得するには:

1. [従来の Azure ポータル](https://manage.windowsazure.com)にログインします。
2. 使用するストレージ アカウントを表示します。
3. ナビゲーション ウィンドウの下部にある **[アクセス キーの管理]** をクリックします。
4. ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。 アクセス キーには、プライマリとセカンダリのどちらでも使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

## <a name="create-a-container"></a>コンテナーを作成する
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**Azure::Blob::BlobService** オブジェクトを使用して、コンテナーと BLOB を操作できます。 コンテナーを作成するには、**create\_container()** メソッドを使用します。

次のコード例では、コンテナーを作成し、既に存在している場合はエラーを出力します。

```ruby
azure_blob_service = Azure::Blob::BlobService.new
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

コンテナー内のファイルを公開する場合は、コンテナーのアクセス許可を設定できます。

<strong>create\_container()</strong> の呼び出しを変更して、次のように **:public\_access\_level** オプションを渡すだけです。

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

**:public\_access\_level** オプションに指定できる値は次のとおりです。

* **blob:** BLOB に対するパブリック読み取りアクセスを指定します。 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。 クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。
* **container:** コンテナーと BLOB のデータに対する完全パブリック読み取りアクセスを指定します。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

別の方法として、**set\_container\_acl()** メソッドを使用してパブリック アクセス レベルを指定することでも、コンテナーのパブリック アクセス レベルを変更できます。

次のコード例では、パブリック アクセス レベルを **container**に変更します。

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
BLOB にコンテンツをアップロードするには、**create\_block\_blob()** メソッドを使用して BLOB を作成し、ファイルまたは文字列を BLOB のコンテンツとして使用します。

次のコードでは、ファイル **test.png** を、コンテナー内の新しい BLOB として "image-blob" という名前でアップロードします。

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナーの一覧を取得するには、**list_containers()** メソッドを使用します。
コンテナー内の BLOB の一覧を取得するには、**list\_blobs()** メソッドを使用します。

次のコードでは、アカウントのすべてのコンテナーからすべての BLOB の URL を出力します。

```ruby
containers = azure_blob_service.list_containers()
containers.each do |container|
    blobs = azure_blob_service.list_blobs(container.name)
    blobs.each do |blob|
    puts blob.name
    end
end
```

## <a name="download-blobs"></a>BLOB をダウンロードする
BLOB をダウンロードするには、**get\_blob()** メソッドを使用してコンテンツを取得します。

次のコード例は、**get\_blob()** を使用して "image-blob" のコンテンツをダウンロードし、ローカル ファイルに書き込む方法を示しています。

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>BLOB を削除する
最後に、BLOB を削除するには、**delete\_blob()** メソッドを使用します。 次のコード例は、BLOB の削除方法を示しています。

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>次のステップ
さらに複雑なストレージ タスクの詳細については、次のリンク先をご覧ください。

* [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
* [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)




<!--HONumber=Dec16_HO4-->


