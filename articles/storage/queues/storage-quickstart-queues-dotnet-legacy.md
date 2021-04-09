---
title: クイック スタート:.NET 用 Azure Storage v11 を使用してキューを管理する
description: このクイック スタートでは、.NET 用 Azure Storage クライアント ライブラリを使用して、キューを作成し、そこにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って処理する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 23703dc507aa909aea4711289a4d7d5c5e6a170e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588201"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>クイック スタート:.NET 用 Azure Storage SDK v11 を使用してキューを管理する

このクイックスタートでは、.NET 用 Azure Storage クライアント ライブラリ v11 を使用して、キューを作成し、そこにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って処理する方法について説明します。

> [!NOTE]
> このクイックスタートでは、Azure Queue Storage クライアント ライブラリのレガシ バージョンを使用します。 最新バージョンで始めるには、「[クイックスタート: .NET 用 Azure Queue Storage クライアント ライブラリ v12](storage-quickstart-queues-dotnet.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次に、ご使用のオペレーティング システム用の .NET Core 2.0 をダウンロードしてインストールします。 Windows を実行している場合は、Visual Studio をインストールして、.NET Framework を使用してもかまいません。 オペレーティング システムで使用するエディターをインストールすることもできます。

### <a name="windows"></a>Windows

- [.NET Core for Windows](https://www.microsoft.com/net/download/windows) または [.NET Framework](https://www.microsoft.com/net/download/windows) (Visual Studio for Windows に付属) をインストールする
- [Visual Studio for Windows](https://www.visualstudio.com/) をインストールする。 .NET Core を使用する場合、Visual Studio のインストールは任意です。

.NET Core と .NET Framework の選択については、[サーバー アプリ用 .NET Core と .NET Framework の選択](/dotnet/standard/choosing-core-framework-server)に関するページを参照してください。

### <a name="linux"></a>Linux

- [Linux 用 .NET Core](https://www.microsoft.com/net/download/linux) をインストールする
- オプションで、[Visual Studio Code](https://www.visualstudio.com/) および [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールする

### <a name="macos"></a>macOS

- [macOS 用 .NET Core](https://www.microsoft.com/net/download/macos) をインストールする。
- オプションで、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) をインストールする

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使うサンプル アプリケーションは、基本的なコンソール アプリケーションです。 [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart) でサンプル アプリケーションを調べることができます。

アプリケーションのコピーを開発環境にダウンロードするには、[Git](https://git-scm.com/) を使います。

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

このコマンドは、ローカルの Git フォルダーにリポジトリを複製します。 Visual Studio ソリューションを開くには、`storage-queues-dotnet-quickstart` フォルダーを開き、`storage-queues-dotnet-quickstart.sln` をダブルクリックします。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションを実行するには、ストレージ アカウントの接続文字列を指定する必要があります。 サンプル アプリケーションは、環境変数から接続文字列を読み取り、それを使って、Azure Storage に対する要求を承認します。

接続文字列をコピーした後、アプリケーションを実行しているローカル マシンの新しい環境変数にそれを書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動する必要があります。

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

### <a name="macos"></a>macOS

次のように .bash_profile を編集し、環境変数を追加します。

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

## <a name="run-the-sample"></a>サンプルを実行する

サンプル アプリケーションでは、キューを作成して、そこにメッセージを追加します。 アプリケーションでは、最初にキューから削除することなくメッセージをピークした後、メッセージを取得して、キューから削除します。

### <a name="windows"></a>Windows

Visual Studio をエディターとして使用している場合は、`F5` キーを押して実行することができます。

それ以外の場合は、お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

### <a name="linux"></a>Linux

お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

### <a name="macos"></a>macOS

お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

サンプル アプリケーションの出力は次の例のようになります。

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを調べて、そのしくみを理解できるようにします。

### <a name="try-parsing-the-connection-string"></a>接続文字列を解析してみる

サンプルでは最初に、ストレージ アカウントを指す [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) オブジェクトを作成するために解析できる接続文字列が環境変数に含まれていることを確認します。 接続文字列が有効であることを確認するため、サンプルでは [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) メソッドが使用されます。 `TryParse` が成功すると、`storageAccount` 変数が初期化され、`true` が返されます。

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>キューを作成する

サンプルでは、最初にキューを作成して、そこにメッセージを追加します。

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>メッセージを追加する

次に、サンプルでは、キューの末尾にメッセージを追加します。

メッセージは、UTF-8 エンコードの XML 要求に格納できる形式でなければならず、最大 64 KB のサイズまで許容されます。 メッセージにバイナリ データが含まれている場合は、メッセージを Base64 でエンコードすることをお勧めします。

既定では、メッセージの最大 Time to Live は 7 日に設定されます。 メッセージの有効期限には、任意の正の数値を指定できます。

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

有効期限のないメッセージを追加するには、[`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) への呼び出しで `Timespan.FromSeconds(-1)` を使用します。

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>キューからメッセージをピークする

サンプルでは、キューからメッセージをピークする方法が示されています。 メッセージをピークすると、メッセージの内容を読み取ることができます。 ただし、メッセージは、後で別のクライアントがメッセージを取得して処理できるように、他のクライアントにも見える状態のままになります。

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>メッセージをデキューする

サンプルでは、メッセージをデキューする方法も示されています。 メッセージをデキューすると、キューの先頭からメッセージが取得されて、メッセージは一時的に他のクライアントから認識されないようになります。 既定では、メッセージを認識できない時間は 30 秒間です。 この間に、コードでメッセージを処理できます。 メッセージのデキューを終了するには、処理の後ですぐにメッセージを削除し、別のクライアントによって同じメッセージがデキューされないようにします。

ハードウェアまたはソフトウェアの障害のためのコードでメッセージを処理できない場合、認識不能期間が経過すると、メッセージは再び認識できるようになります。 別のクライアントが同じメッセージを取得し、再度試すことができます。

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする

サンプルでは、キューを削除することによって、作成したリソースをクリーンアップします。 キューを削除すると、その中のメッセージも削除されます。

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>キューを使用する .NET アプリケーションを開発するためのリソース

Azure Queue Storage を使用する .NET 開発については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- [.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)の最新バージョン用の NuGet パッケージをダウンロードします
  - [共通](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [キュー](https://www.nuget.org/packages/Azure.Storage.Queues/)
- GitHub で [.NET クライアント ライブラリのソース コード](https://github.com/Azure/azure-storage-net)を確認します。

### <a name="azure-storage-client-library-reference-and-samples"></a>Azure Storage クライアント ライブラリのリファレンスとサンプル

- .NET クライアント ライブラリの詳細については、[.NET 用 Azure Storage クライアント ライブラリのリファレンス](/dotnet/api/overview/azure/storage)を参照してください。
- .NET クライアント ライブラリを使用して記述された [Queue Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues)を確認します。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、.NET を使用して、キューにメッセージを追加する方法、キューからメッセージをピークする方法、メッセージをデキューして処理する方法を説明しました。

> [!div class="nextstepaction"]
> [アプリケーションと Azure Queue Storage の間で通信する](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- .NET Core の詳細については、「[Get started with .NET in 10 minutes (10 分で .NET を使い始める)](https://www.microsoft.com/net/learn/get-started/)」を参照してください。
