---
title: C++ - Azure からオブジェクト (BLOB) ストレージを使用する方法 | Microsoft Docs
description: Azure BLOB (オブジェクト) ストレージを使用して、非構造化データをクラウドに格納します。
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 32601fc15e6b6a25a1005b6d95499d4aae4fb0d2
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461177"
---
# <a name="how-to-use-blob-storage-from-c"></a>C++ から BLOB ストレージを使用する方法

このガイドでは、Azure Blob Storage を使用して一般的なシナリオを実行する方法について説明します。 BLOB のアップロード、一覧表示、ダウンロード、および削除方法を示す例を紹介します。 サンプルは C++ で記述され、 [C++ 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)を利用しています。   

Blob Storage の詳細については、「[Azure Blob Storage の概要](storage-blobs-introduction.md)」を参照してください。

> [!NOTE]
> このガイドは、C++ 用 Azure ストレージ クライアント ライブラリ 1.0.0 以上のバージョンを対象としています。 Microsoft では、[NuGet](https://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp) 経由で使用できる C++ 用のストレージ クライアント ライブラリの最新バージョンを使用することをお勧めします。

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ アプリケーションの作成
このガイドでは、C++ アプリケーション内で実行できるストレージ機能を使用します。  

そのためには、C++ 用 Azure ストレージ クライアント ライブラリをインストールし、Azure サブスクリプションに Azure ストレージ アカウントを作成する必要があります。   

C++ 用 Azure ストレージ クライアント ライブラリをインストールする場合、次の方法を使用できます。

* **Linux:** [C++ 用 Azure Storage クライアント ライブラリの README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) ページに記載されている手順に従います。  
* **Windows:** Visual Studio で、 **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックします。 [NuGet パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) に次のコマンドを入力し、 **Enter**キーを押します。  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>BLOB ストレージにアクセスするようにアプリケーションを構成する
Azure Storage API を使用して BLOB にアクセスする C++ ファイルの先頭には、次の include ステートメントを追加します。  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure のストレージ接続文字列の設定
Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。 クライアント アプリケーションの実行時、ストレージ接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、[Azure Portal](https://portal.azure.com) に表示されるストレージ アカウントの名前とストレージ アクセス キーを使用します。 ストレージ アカウントとストレージ アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。 この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

ローカルの Windows コンピューターでアプリケーションをテストするには、[Azure SDK](https://azure.microsoft.com/downloads/) と共にインストールされた、Microsoft Azure [ストレージ エミュレーター](../storage-use-emulator.md)を使用できます。 ストレージ エミュレーターは、ローカルの開発マシンで、Azure で使用できる BLOB、Queue、および Table service をシミュレートするユーティリティです。 次の例では、ローカルのストレージ エミュレーターに接続文字列を保持する静的フィールドを宣言する方法を示しています。

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure ストレージ エミュレーターを起動するには、 **[スタート]** ボタンをクリックするか、**Windows** キーを押します。 「**Azure ストレージ エミュレーター**」と入力し、アプリケーションの一覧から **[Microsoft Azure ストレージ エミュレーター]** を選択します。  

次のサンプルでは、これら 2 つのメソッドのいずれかを使用してストレージ接続文字列を取得するとします。  

## <a name="retrieve-your-storage-account"></a>ストレージ アカウントを取得する
**cloud_storage_account** クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、 **parse** メソッド使用します。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

次に、**cloud_blob_client** クラスへの参照を取得します。これにより、BLOB ストレージ内に格納されているコンテナーと BLOB を表すオブジェクトを取得できます。 次のコードは、前に取得したストレージ アカウント オブジェクトを使用して、**cloud_blob_client** オブジェクトを作成します。  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>方法:コンテナーを作成する
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

この例は、コンテナーがない場合に、コンテナーを作成する方法を示しています。  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。 コンテナー内のファイル (BLOB) をだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できますが、変更または削除できるのは、適切なアクセス キーを持っているユーザーだけです。  

## <a name="how-to-upload-a-blob-into-a-container"></a>方法:コンテナーに BLOB をアップロードする
Azure BLOB ストレージでは、ブロック BLOB とページ BLOB がサポートされています。 ほとんどの場合は、ブロック BLOB を使用することをお勧めします。  

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。 BLOB の参照を取得したら、**upload_from_stream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。 次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

**upload_from_file** メソッドを使用して、ブロック BLOB にファイルをアップロードすることもできます。

## <a name="how-to-list-the-blobs-in-a-container"></a>方法:コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。 次に、コンテナーの **list_blobs** メソッドを使用して、その中の BLOB やディレクトリを取得できます。 返された **list_blob_item** の豊富なプロパティとメソッドのセットにアクセスするには、**list_blob_item.as_blob** メソッドを呼び出して **cloud_blob** オブジェクトを取得するか、**list_blob.as_directory** メソッドを呼び出して cloud_blob_directory オブジェクトを取得する必要があります。 次のコードは、 **my-sample-container** コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

一覧取得操作の詳細については、「 [C++ での Azure Storage のリソース一覧の取得](../storage-c-plus-plus-enumeration.md)」を参照してください。

## <a name="how-to-download-blobs"></a>方法:BLOB をダウンロードする
BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **download_to_stream** メソッドを呼び出します。 次の例は、**download_to_stream** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

代わりに、**download_to_file** メソッドを使用して、ファイルに BLOB の内容をダウンロードすることもできます。
**download_text** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>方法:BLOB を削除する
BLOB を削除するには、まず BLOB の参照を取得し、次にその **delete_blob** メソッドを呼び出します。  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>次の手順
これで、BLOB ストレージの基本を学習できました。Azure Storage の詳細については、次のリンク先を参照してください。  

* [C++ から Queue ストレージを使用する方法](../storage-c-plus-plus-how-to-use-queues.md)
* [C++ から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++ での Azure Storage のリソース一覧の取得](../storage-c-plus-plus-enumeration.md)
* [C++ 用ストレージ クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-cpp)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](../storage-use-azcopy.md)

