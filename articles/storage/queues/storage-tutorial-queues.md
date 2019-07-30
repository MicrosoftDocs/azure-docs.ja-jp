---
title: チュートリアル - Azure ストレージ キューの使用 - Azure Storage
description: Azure Queue サービスを使用して、キューの作成のほか、メッセージの挿入、取得、削除を行う方法のチュートリアル。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 08ef140eb860637cc0c09619abe7051cc007e99f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540288"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>チュートリアル:Azure ストレージ キューの操作

Azure Queue ストレージではクラウドベースのキューが実装され、分散アプリケーションのコンポーネント間の通信を可能にします。 各キューには、送信側コンポーネントにより追加し、受信側コンポーネントにより処理できるメッセージの一覧が保持されます。 キューを利用することで、アプリケーションを需要に合わせてすぐにスケーリングできます。 この記事では、Azure ストレージ キューを使用するための基本手順について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Azure のストレージ アカウントの作成
> - アプリケーションの作成
> - 非同期コードのサポートを追加する
> - キューを作成する
> - キューにメッセージを挿入する
> - メッセージをデキューする
> - 空のキューを削除する
> - コマンドライン引数を確認する
> - アプリのビルドと実行

## <a name="prerequisites"></a>前提条件

- プラットフォームに依存しない [Visual Studio Code](https://code.visualstudio.com/download) エディターの無料コピーを入手します。
- [.NET Core SDK](https://dotnet.microsoft.com/download) をダウンロードし、インストールします。
- 現行の Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

まず、Azure ストレージ アカウントを作成します。 ストレージ アカウント作成の詳細な手順が必要な場合、「[ストレージ アカウントの作成](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)」というクイック スタートをご覧ください。

## <a name="create-the-app"></a>アプリケーションの作成

**QueueApp** という名前の .NET Core アプリケーションを作成します。 わかりやすくするために、このアプリではメッセージの送受信ともキューを介して行います。

1. コンソール ウィンドウ (CMD、PowerShell、Azure CLI など) で、`dotnet new` コマンドを使用し、**QueueApp** という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。**Program.cs**。

   ```console
   dotnet new console -n QueueApp
   ```

2. 新しく作成された **QueueApp** フォルダーに切り替え、アプリをビルドし、すべて問題なく動作していることを確認します。

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   次のような結果が表示されます。

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>非同期コードのサポートを追加する

このアプリではクラウド リソースが使用されているため、コードは非同期で実行されます。 ただし、C# の **async** と **await** は、C# 7.1 までは **Main** メソッドで有効なキーワードではありませんでした。 **csproj** ファイルのフラグを介し、そのコンピューターに簡単に切り替えることができます。

1. プロジェクト ディレクトリのコマンド ラインで「`code .`」と入力し、現在のディレクトリで Visual Studio Code を開きます。 コマンドライン ウィンドウは開いたままにします。 後で他にもコマンドを実行します。 ビルドとデバッグに必要な C# アセットを追加するように求められた場合、 **[はい]** をクリックします。

2. エディターで **QueueApp.csproj** ファイルを開きます。

3. ビルド ファイルで最初の **PropertyGroup** に `<LangVersion>7.1</LangVersion>` を追加します。 インストールしてある .NET のバージョンによっては **TargetFramework** が異なる場合があります。そのため、必ず **LangVersion** タグのみを追加してください。

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. **QueueApp.csproj** ファイルを保存します。

5. **Program.cs** ソース ファイルを開き、非同期で実行されるように **Main** メソッドを更新します。 **void** を **async Task** 戻り値に変更します。

   ```csharp
   static async Task Main(string[] args)
   ```

6. **Program.cs** ファイルを保存します。

## <a name="create-a-queue"></a>キューを作成する

1. `dotnet add package` コマンドを使用して、**Microsoft.Azure.Storage.Common** および **Microsoft.Azure.Storage.Queue** パッケージをプロジェクトにインストールします。 コンソール ウィンドウのプロジェクト フォルダーから以下の dotnet コマンドを実行します。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. **Program.cs** ファイルの一番上で、`using System;` ステートメントの直後に次の名前空間を追加します。 このアプリでは、Azure Storage に接続し、キューを使用するとき、これらの名前空間からの型が使用されます。

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. **Program.cs** ファイルを保存します。

### <a name="get-your-connection-string"></a>接続文字列を取得する

このクライアント ライブラリでは、接続を確立するために接続文字列が使用されます。 接続文字列は、Azure portal のストレージ アカウントの **[設定]** セクションにあります。

1. Web ブラウザーで、[Azure portal](https://portal.azure.com/) にサインインします。

2. Azure Portal のストレージ アカウントに移動します。

3. **[アクセス キー]** を選択します。

4. **[接続文字列]** フィールドの右にある **[コピー]** ボタンをクリックします。

![接続文字列](media/storage-tutorial-queues/get-connection-string.png)

この接続文字列の形式を次に示します。

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>アプリに接続文字列を追加する

ストレージ アカウントにアクセスできるよう、アプリに接続文字列を追加します。

1. Visual Studio Code に戻ります。

2. **Program** クラスで、`private const string connectionString =` メンバーを追加し、接続文字列を保持します。

3. 前に Azure portal でコピーした文字列値を等号の後ろに貼り付けます。 **connectionString** 値は自分のアカウントに固有となります。

4. **Main** から "Hello World" コードを削除します。 コードは次のようになるはずですが、接続文字列の値は独自のものになります。

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. **CloudQueue** オブジェクトを作成するように **Main** を更新します。このオブジェクトは後に送信と受信のメソッドに渡されます。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. ファイルを保存します。

## <a name="insert-messages-into-the-queue"></a>キューにメッセージを挿入する

キューにメッセージを送信する新しいメソッドを作成します。 **Program** クラスに次のメソッドを追加します。 このメソッドによりキュー参照が取得されます。キューがない場合、[CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) を呼び出すことで新しいキューが作成されます。 次に、[AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) を呼び出すことでキューにメッセージが追加されます。

1. 次の **SendMessageAsync** メソッドを **Program** クラスに追加します。

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. ファイルを保存します。

メッセージは、UTF-8 エンコードの XML 要求に格納できる形式でなければならず、最大 64 KB のサイズまで許容されます。 メッセージにバイナリ データが含まれている場合は、メッセージを Base64 でエンコードすることをお勧めします。

既定では、メッセージの最大 Time to Live は 7 日に設定されます。 メッセージの有効期限には、任意の正の数値を指定できます。 有効期限のないメッセージを追加するには、**AddMessageAsync** への呼び出しで `Timespan.FromSeconds(-1)` を使用します。

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>メッセージをデキューする

**ReceiveMessageAsync** という名前の新しいメソッドを作成します。 このメソッドは、[GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) を呼び出し、キューからメッセージを受け取ります。 メッセージが正常に受け取られたら、何度も処理されないよう、キューから削除しておくことが重要です。 メッセージが受け取られたら、[DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) を呼び出し、キューからそれを削除します。

1. 次の **ReceiveMessageAsync** メソッドを **Program** クラスに追加します。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. ファイルを保存します。

## <a name="delete-an-empty-queue"></a>空のキューを削除する

プロジェクトの終わりに、作成したリソースを引き続き必要とするかどうかを判断することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 キューが存在するが空の場合、削除して良いかどうかをユーザーに確認してください。

1. **ReceiveMessageAsync** メソッドを拡張し、空のキューを削除するプロンプトを追加します。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. ファイルを保存します。

## <a name="check-for-command-line-arguments"></a>コマンドライン引数を確認する

コマンドラインの引数がアプリに渡されている場合、それらがキューに追加するメッセージであると想定してください。 引数を結合し、文字列を作ります。 前に追加した **SendMessageAsync** を呼び出し、この文字列をメッセージ キューに追加します。

コマンドラインの引数がない場合、取得操作を実行します。 **ReceiveMessageAsync** メソッドを呼び出し、キューの最初のメッセージを取得します。

最後に、**Console.ReadLine** を呼び出し、ユーザーの入力を待ってから終了します。

1. コマンドラインの引数を確認し、ユーザーの入力を待つよう、**Main** メソッドを拡張します。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. ファイルを保存します。

## <a name="complete-code"></a>完成したコード

このプロジェクトの完成したコードは次のようになります。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. プロジェクト ディレクトリのコマンド ラインから、次の dotnet コマンドを実行し、プロジェクトをビルドします。

   ```console
   dotnet build
   ```

2. プロジェクトが正常にビルドされたら、次のコマンドを実行し、最初のメッセージをキューに追加します。

   ```console
   dotnet run First queue message
   ```

次のように出力されます。

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. コマンドラインの引数なしでアプリを実行し、キューの最初のメッセージを受け取り、削除します。

   ```console
   dotnet run
   ```

4. すべてのメッセージが削除されるまでアプリの実行を続けます。 さらに 1 回実行すると、キューが空であるというメッセージとキューを削除するためのプロンプトが表示されます。

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

1. キューを作成する
2. メッセージをキューに追加し、キューから削除する
3. Azure ストレージ キューを削除する

詳細については Azure キューのクイック スタートでご確認ください。

> [!div class="nextstepaction"]
> [キューのクイック スタート](storage-quickstart-queues-portal.md)
