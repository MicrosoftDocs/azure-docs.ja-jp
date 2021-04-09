---
title: 'クイックスタート: Azure Queue Storage クライアント ライブラリ v12 - .NET'
description: .NET 用 Azure Queue Storage クライアント ライブラリ v12 を使用して、キューを作成し、キューにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って削除する方法について説明します。 キューを削除する方法についても説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 22038e4145acabc067083177fcf297464972ad58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589527"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>クイックスタート: .NET 用 Azure Queue Storage クライアント ライブラリ v12

.NET 用 Azure Queue Storage クライアント ライブラリ バージョン 12 を使用してみましょう。 Azure Queue Storage は、後で取得して処理するために多数のメッセージを格納するためのサービスです。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

.NET 用 Azure Queue Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

- キューを作成する
- メッセージをキューに追加する
- キュー内のメッセージを表示する
- キュー内のメッセージを更新する
- キューからメッセージを受信する
- キューからメッセージを削除する
- キューを削除する

その他のリソース:

- [API リファレンス ドキュメント](/dotnet/api/azure.storage.queues)
- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [サンプル](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- Azure Storage アカウント - [ストレージ アカウントを作成する](../common/storage-account-create.md)
- 使用するオペレーティング システム用の最新の [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。 ランタイムではなく、必ず SDK を入手してください。

## <a name="setting-up"></a>設定

このセクションでは、.NET 用 Azure Queue Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

`QueuesQuickstartV12` という名前の .NET Core アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`QueuesQuickstartV12` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "hello world" C# プロジェクトが作成されます。

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. 新しく作成した `QueuesQuickstartV12` ディレクトリに切り替えます。

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Queue Storage クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. エディターで `Program.cs` ファイルを開きます
1. `Console.WriteLine("Hello, World");` ステートメントを削除します
1. `using` ディレクティブを追加します
1. [非同期コードをサポート](/dotnet/csharp/whats-new/csharp-7#async-main)するように `Main` メソッドの宣言を更新します

コードは次のとおりです。

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。 Queue Storage には、次の 3 種類のリソースがあります。

- ストレージ アカウント
- ストレージ アカウント内のキュー
- キュー内のメッセージ

次の図に、これらのリソースの関係を示します。

![Queue storage のアーキテクチャ図](./media/storage-queues-introduction/queue1.png)

これらのリソースとやり取りするには、以下の .NET クラスを使用します。

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` を使用すると、ストレージ アカウント内のすべてのキューを管理できます。
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` クラスを使用すると、個々のキューとそのメッセージを管理および操作できます。
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` クラスは、キューに対して [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) を呼び出したときに返される個々のオブジェクトを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、.NET 用 Azure Queue Storage クライアント ライブラリを使用して以下の操作を実行する方法を示します。

- [接続文字列を取得する](#get-the-connection-string)
- [キューを作成する](#create-a-queue)
- [メッセージをキューに追加する](#add-messages-to-a-queue)
- [キュー内のメッセージを表示する](#peek-at-messages-in-a-queue)
- [キュー内のメッセージを更新する](#update-a-message-in-a-queue)
- [キューからメッセージを受信する](#receive-messages-from-a-queue)
- [キューからメッセージを削除する](#delete-messages-from-a-queue)
- [キューを削除する](#delete-a-queue)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードは、ストレージ アカウントの接続文字列を取得します。 この接続文字列は、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数に格納されます。

このコードを `Main` メソッド内に追加します。

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>キューを作成する

新しいキューの名前を決定します。 次のコードは、キュー名に GUID 値を追加して、確実に一意になるようにします。

> [!IMPORTANT]
> キュー名に使用できるのは小文字、数字、ハイフンのみであり、名前の先頭は文字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。 また、名前は 3 から 63 文字で指定する必要があります。 詳細については、「[キューおよびメタデータの名前付け](/rest/api/storageservices/naming-queues-and-metadata)」を参照してください。

[`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) クラスのインスタンスを作成します。 次に、[`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) メソッドを呼び出して、ストレージ アカウントにキューを作成します。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>メッセージをキューに追加する

以下のコード スニペットでは、[`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) メソッドを呼び出してキューにメッセージを非同期的に追加します。 さらに、`SendMessageAsync` の呼び出しから返された [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) を保存します。 この receipt は、後でプログラムの中でメッセージを更新する際に使用します。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>キュー内のメッセージを表示する

キュー内のメッセージをクイック表示するには、[`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) メソッドを呼び出します。 このメソッドは、キューの先頭からメッセージを 1 つ以上取得しますが、メッセージの可視性は変更しません。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>キュー内のメッセージを更新する

メッセージの内容を更新するには、[`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) メソッドを呼び出します。 メッセージの表示タイムアウトと内容は、このメソッドで変更できます。 メッセージの内容には UTF-8 でエンコードされた文字列を指定してください。最大サイズは 64 KB です。 先ほどこのコードの中で保存した `SendReceipt` の値を、新しいメッセージの内容と共に渡します。 `SendReceipt` の値によって、更新するメッセージが識別されます。

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

[`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) メソッドを呼び出して、先ほど追加したメッセージをダウンロードします。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>キューからメッセージを削除する

メッセージが処理された後、キューからメッセージを削除します。 ここでの処理は、単にメッセージをコンソールに表示するだけです。

ユーザーからの入力を待ってメッセージを処理、削除するために、`Console.ReadLine` を呼び出してアプリを一時停止させます。 リソースが正しく作成されたことを [Azure portal](https://portal.azure.com) で確認してから、それらを削除してください。 明示的に削除されなかったメッセージは、最終的にキューに再表示され、別の機会に処理されることになります。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>キューを削除する

次のコードでは、[`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync) メソッドを使用してキューを削除することにより、アプリによって作成されたリソースがクリーンアップされます。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>コードの実行

このアプリは、3 つのメッセージを作成して Azure のキューに追加します。 コードでは、キュー内のメッセージを一覧表示した後にそれらを取得して削除してから、最後にキューを削除します。

コンソール ウィンドウで、お使いのアプリケーションのディレクトリに移動し、アプリケーションをビルドして実行します。

```console
dotnet build
```

```console
dotnet run
```

アプリの出力は、次の例のようになります。

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

メッセージを受信する前にアプリが一時停止したら、[Azure portal](https://portal.azure.com) でストレージ アカウントを確認してください。 キューにメッセージが存在することを確認します。

`Enter` キーを押してメッセージを受信し、削除します。 確認を求められたら、もう一度 `Enter` キーを押してキューを削除し、デモを終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、非同期 .NET コードを使用して、キューを作成し、そこにメッセージを追加する方法について説明しました。 その後、メッセージの表示、取得、削除について説明しました。 最後に、メッセージ キューを削除する方法を説明しました。

チュートリアル、サンプル、クイック スタートなどのドキュメントについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [.NET および .NET Core 開発者向けの Azure](/dotnet/azure/)

- 詳細については、「[.NET 用 Azure Storage ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)」を参照してください。
- その他の Azure Queue Storage サンプル アプリについては、「[Azure Queue Storage client library for .NET samples (.NET 用 Azure Queue Storage クライアント ライブラリのサンプル)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)」を参照してください。
- .NET Core の詳細については、「[Get started with .NET in 10 minutes (10 分で .NET を使い始める)](https://www.microsoft.com/net/learn/get-started/)」を参照してください。
