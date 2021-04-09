---
title: C++ での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使用してファイル データを格納する C++ アプリケーションおよびサービスを開発する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb74ab16e51fbb3a157757353d5743e889f993dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629701"
---
# <a name="develop-for-azure-files-with-c"></a>C++ での Azure Files 用の開発

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>このチュートリアルについて

このチュートリアルでは、Azure Files で基本的な操作を実行する方法を紹介します。 C++ で記述されたサンプルを利用し、共有とディレクトリを作成し、ファイルをアップロード、一覧表示、削除する方法を紹介します。 初めて Azure Files を使用する場合は、次のセクションの概念に目を通すと、サンプルを理解するのに役立ちます。

* Azure ファイル共有を作成および削除する
* ディレクトリを作成および削除する
* Azure ファイル共有のファイルとディレクトリを列挙する
* ファイルのアップロード、ダウンロード、および削除
* Azure ファイル共有のクォータ (最大サイズ) を設定する
* 共有で定義されている共有アクセス ポリシーを使用するファイルの Shared Access Signature (SAS キー) を作成する

> [!Note]  
> Azure Files には SMB 経由でアクセスできるため、標準の C++ I/O クラスおよび関数を使用して Azure ファイル共有にアクセスする単純なアプリケーションを記述できます。 この記事では、[File REST API](/rest/api/storageservices/file-service-rest-api) を使用して Azure Files と通信する Azure Storage C++ SDK を使用するアプリケーションを記述する方法について説明します。

## <a name="create-a-c-application"></a>C++ アプリケーションの作成

サンプルをビルドするには、C++ 用 Azure Storage クライアント ライブラリ 2.4.0 をインストールする必要があります。 また、Azure ストレージ アカウントを作成しておく必要があります。

C++ 用 Azure Storage クライアント 2.4.0 をインストールする場合、次の方法のいずれかを使用できます。

* **Linux:** [C++ 用 Azure ストレージ クライアント ライブラリの README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) ページに記載されている手順に従います。
* **Windows:** Visual Studio で、 **[ツール] &gt; [NuGet パッケージ マネージャー] &gt; [パッケージ マネージャー コンソール]** をクリックします。 [NuGet パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) に次のコマンドを入力し、 **Enter** キーを押します。
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files を使用するようにアプリケーションを設定する

Azure Files を操作する C++ ソース ファイルの先頭に次の include ステートメントを追加します。

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定

ファイル ストレージを使用するには、Azure ストレージ アカウントに接続する必要があります。 最初の手順は、ストレージ アカウントに接続するために使用する接続文字列を構成することです。 そのために静的変数を定義しましょう。

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Azure ストレージ アカウントに接続する

**cloud_storage_account** クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、 **parse** メソッド使用します。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure ファイル共有内のすべてのファイルとディレクトリは、**Share** という名前のコンテナー内に存在します。 ストレージ アカウントには、アカウントの容量が許す限りの共有を置くことができます。 共有とそのコンテンツにアクセスするには、Azure Files クライアントを使う必要があります。

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Azure Files クライアントを使用すると、共有への参照を取得できます。

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

共有を作成するには、**cloud_file_share** オブジェクトの **create_if_not_exists** メソッドを使用します。

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

この時点で、**share** は「**my-sample-share**」という名前の共有の参照を保持します。

## <a name="delete-an-azure-file-share"></a>Azure ファイル共有を削除する

cloud_file_share オブジェクトで **delete_if_exists** メソッドを呼び出すと共有が削除されます。 次がそのサンプル コードです。

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>ディレクトリを作成する

ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することができます。 Azure Files では、自分のアカウントで許可されるだけのディレクトリを作成できます。 次のコードは、ルート ディレクトリの下に **my-sample-directory** という名前のディレクトリを作成し、さらに **my-sample-subdirectory** という名前のサブディレクトリを作成します。

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

ディレクトリの削除は単純な作業です。ただし、注意するべきことは、ファイルや他のディレクトリが含まれるディレクトリは削除できないということです。

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure ファイル共有のファイルとディレクトリを列挙する

**cloud_file_directory** 参照で **list_files_and_directories** を呼び出すと、共有内のファイルとディレクトリの一覧を簡単に取得できます。 返された **list_file_and_directory_item** の豊富なプロパティとメソッドのセットにアクセスするには、**list_file_and_directory_item.as_file** メソッドを呼び出して **cloud_file** オブジェクトを取得するか、**list_file_and_directory_item.as_directory** メソッドを呼び出して **cloud_file_directory** オブジェクトを取得する必要があります。

次のコードは、共有のルート ディレクトリ内の各アイテムの URI を取得して出力する方法を示しています。

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>ファイルをアップロードする

Azure ファイル共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。 このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルをアップロードするための最初の手順は、ファイルを置くディレクトリの参照を取得することです。 これを行うには、share オブジェクトの **get_root_directory_reference** メソッドを呼び出します。

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

共有のルート ディレクトリの参照が与えられたので、このディレクトリにファイルをアップロードできます。 この例では、それぞれファイル、テキスト、およびストリームからファイルをアップロードします。

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>ファイルをダウンロードする

ファイルをダウンロードするには、まずファイルの参照を取得し、**download_to_stream** メソッドを呼び出して、ローカル ファイルに保存できるストリーム オブジェクトにファイルの内容を転送します。 代わりに、**download_to_file** メソッドを使用して、ローカル ファイルにファイルの内容をダウンロードすることもできます。 また、**download_text** メソッドを使用して、ファイルの内容をテキスト文字列としてダウンロードすることもできます。

次の例は、**download_to_stream** メソッドと **download_text** メソッドを使用して、前のセクションで作成したファイルをダウンロードする方法を示しています。

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>ファイルを削除する

Azure Files でのもう 1 つの一般的な操作はファイルの削除です。 次のコードでは、ルート ディレクトリに保存されている my-sample-file-3 という名前のファイルを削除します。

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Azure ファイル共有のクォータ (最大サイズ) を設定する

ファイル共有のクォータ (最大サイズ) をギガバイト単位で設定することができます。 また、共有に現在格納されているデータの量も確認できます。

共有のクォータを設定することにより、共有に格納するファイルの合計サイズを制限できます。 共有上のファイルの合計サイズが共有に設定されたクォータを超過すると、クライアントは既存ファイルのサイズを増やせなくなったり、新しいファイルを作成できなくなったりします。ただし、これらのファイルが空である場合は除きます。

次の例では、共有の現在の使用状況を確認する方法と、共有のクォータを設定する方法を示します。

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>ファイルまたはファイル共有の Shared Access Signature の生成

ファイル共有または個々のファイルの Shared Access Signature (SAS) を生成することができます。 また、ファイル共有に共有アクセス ポリシーを作成して、Shared Access Signature を管理することもできます。 共有アクセス ポリシーを作成することをお勧めします。これにより、侵害されそうな場合に SAS を取り消すことができます。

次の例では、共有上に共有アクセス ポリシーを作成し、そのポリシーを使用して共有上のファイルの SAS に制約を指定します。

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>次のステップ

Azure Storage についてさらに学習するには、次のリソースを参照してください。

* [C++ 用ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp)
* [C++ での Azure Storage ファイル サービスのサンプル](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)