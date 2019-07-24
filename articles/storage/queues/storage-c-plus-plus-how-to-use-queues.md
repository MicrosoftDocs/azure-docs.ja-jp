---
title: Queue Storage を使用する方法 (C++) | Microsoft Docs
description: Azure で Queue ストレージ サービスを使用する方法について説明します。 サンプルは C++ で記述されています。
services: storage
author: cbrooksmsft
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.subservice: queues
ms.openlocfilehash: 08c865e5383e4095a22aa6c10c8b181f916d1d30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999426"
---
# <a name="how-to-use-queue-storage-from-c"></a>C++ から Queue ストレージを使用する方法
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要
このガイドでは、Azure キュー ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは C++ で記述され、 [C++ 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)を利用しています。 キュー メッセージの**挿入**、**ピーク**、**取得**、**削除**と、**キューの作成と削除**の各シナリオについて説明します。

> [!NOTE]
> このガイドは、C++ 用 Azure ストレージ クライアント ライブラリ 1.0.0 以上のバージョンを対象としています。 推奨されるバージョンはストレージ クライアント ライブラリ 2.2.0 です。これは、[NuGet](https://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp/) 経由で入手できます。
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ アプリケーションの作成
このガイドでは、C++ アプリケーション内で実行できるストレージ機能を使用します。

そのためには、C++ 用 Azure ストレージ クライアント ライブラリをインストールし、Azure サブスクリプションに Azure ストレージ アカウントを作成する必要があります。

C++ 用 Azure ストレージ クライアント ライブラリをインストールする場合、次の方法を使用できます。

* **Linux:**[C++ 用 Azure Storage クライアント ライブラリの README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) ページに記載されている手順に従います。
* **Windows:** Visual Studio で、**[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックします。 [NuGet パッケージ マネージャー コンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) に次のコマンドを入力し、 **Enter**キーを押します。

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Queue ストレージにアクセスするようにアプリケーションを構成する
Azure Storage API を使用してキューにアクセスする C++ ファイルの先頭には、次の include ステートメントを追加します。  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定
Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。 クライアント アプリケーションの実行時、ストレージ接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、[Azure Portal](https://portal.azure.com) に表示されるストレージ アカウントの名前とストレージ アクセス キーを使用します。 ストレージ アカウントとストレージ アクセス キーの詳細については、「[Azure ストレージ アカウントについて](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」を参照してください。 この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

ローカルの Windows コンピューターでアプリケーションをテストするには、[Azure SDK](https://azure.microsoft.com/downloads/) と共にインストールされた、Microsoft Azure [ストレージ エミュレーター](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)を使用できます。 ストレージ エミュレーターは、ローカルの開発マシンで、Azure で使用できる BLOB、Queue、および Table service をシミュレートするユーティリティです。 次の例では、ローカルのストレージ エミュレーターに接続文字列を保持する静的フィールドを宣言する方法を示しています。  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure ストレージ エミュレーターを起動するには、**[スタート]** ボタンをクリックするか、**Windows** キーを押します。 「**Azure ストレージ エミュレーター**」と入力し、アプリケーションの一覧から **[Microsoft Azure ストレージ エミュレーター]** を選択します。

次のサンプルでは、これら 2 つのメソッドのいずれかを使用してストレージ接続文字列を取得するとします。

## <a name="retrieve-your-connection-string"></a>接続文字列の取得
**cloud_storage_account** クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、 **parse** メソッド使用します。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>方法:キューを作成する
**cloud_queue_client** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。 次のコードでは、**cloud_queue_client** オブジェクトを作成します。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

**cloud_queue_client** オブジェクトを使用して、使用するキューへの参照を取得します。 キューが存在しない場合は作成できます。

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>方法:メッセージをキューに挿入する
既存のキューにメッセージを挿入するには、最初に新しい **cloud_queue_message** を作成します。 次に、**add_message** メソッドを呼び出します。 **cloud_queue_message** は、文字列または**バイト**配列から作成できます。 次のコードでは、キューが存在しない場合は作成し、メッセージ "Hello, World" を挿入します。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする
**peek_message** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する
キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。 これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。 この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。 通常は、さらに再試行回数を保持し、メッセージの再試行回数が n 回を超えた場合はメッセージを削除するようにします。 こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>方法:次のメッセージをデキューする
コードでは、2 つの手順でキューからメッセージをデキューします。 **get_message** を呼び出すと、キュー内の次のメッセージを取得します。 **get_message** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 また、キューからのメッセージの削除を完了するには、**delete_message** を呼び出す必要があります。 このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **delete_message** を呼び出します。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>方法:追加オプションを利用してメッセージをデキューする
キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、**get_messages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、**for** ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。 この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**get_messages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する
キュー内のメッセージの概数を取得できます。 **download_attributes** メソッドは、メッセージ数などのキューの属性を取得するように Queue サービスに要求します。 **approximate_message_count** メソッドは、キューのメッセージの概数を取得します。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する
キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **delete_queue_if_exists** メソッドを呼び出します。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>次の手順
これで、Queue ストレージの基本を学習できました。Azure Storage の詳細については、次のリンク先を参照してください。

* [C++ から BLOB ストレージを使用する方法](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [C++ から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++ での Azure Storage のリソース一覧の取得](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [C++ 用ストレージ クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-cpp)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
