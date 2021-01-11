---
title: チュートリアル - .NET での Azure Storage キューの操作
description: .NET から Azure Queue サービスを使用して、キューの作成のほか、メッセージの挿入、取得、削除を行う方法のチュートリアル。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 7474cfbd0182797bd62e97979e83e2aeb5244cbc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008796"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>チュートリアル:.NET での Azure ストレージ キューの操作

Azure Queue ストレージではクラウドベースのキューが実装され、分散アプリケーションのコンポーネント間の通信を可能にします。 各キューには、送信側コンポーネントにより追加し、受信側コンポーネントにより処理できるメッセージの一覧が保持されます。 キューを利用することで、アプリケーションを需要に合わせてすぐにスケーリングできます。 この記事では、Azure ストレージ キューを使用するための基本手順について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Azure のストレージ アカウントの作成
> - アプリを作成する
> - Azure クライアント ライブラリを追加する
> - 非同期コードのサポートを追加する
> - キューを作成する
> - キューにメッセージを挿入する
> - メッセージをデキューする
> - 空のキューを削除する
> - コマンドライン引数を確認する
> - アプリのビルドと実行

## <a name="prerequisites"></a>前提条件

- プラットフォームに依存しない [Visual Studio Code](https://code.visualstudio.com/download) エディターの無料コピーを入手します。
- [.NET Core SDK](https://dotnet.microsoft.com/download) バージョン 3.1 以降をダウンロードし、インストールします。
- 現行の Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

まず、Azure ストレージ アカウントを作成します。 ストレージ アカウント作成の詳細な手順が必要な場合、「[ストレージ アカウントの作成](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)」というクイック スタートをご覧ください。 前提条件の無料 Azure アカウントを作成した後に別途行う手順となります。

## <a name="create-the-app"></a>アプリを作成する

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

   次の出力のような結果が表示されます。

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-the-azure-client-libraries"></a>Azure クライアント ライブラリを追加する

1. `dotnet add package` コマンドを使用して、Azure Storage クライアント ライブラリをプロジェクトに追加します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   コンソール ウィンドウのプロジェクト フォルダーから次のコマンドを実行します。

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   コンソール ウィンドウのプロジェクト フォルダーから以下のコマンドを実行します。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>using ステートメントを追加する

1. プロジェクト ディレクトリのコマンド ラインで「`code .`」と入力し、現在のディレクトリで Visual Studio Code を開きます。 コマンドライン ウィンドウは開いたままにします。 後で他にもコマンドを実行します。 ビルドとデバッグに必要な C# アセットを追加するように求められた場合、 **[はい]** をクリックします。

1. **Program.cs** ソース ファイルを開いて、`using System;` ステートメントの直後に次の名前空間を追加します。 このアプリでは、Azure Storage に接続し、キューを使用するとき、これらの名前空間からの型が使用されます。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. **Program.cs** ファイルを保存します。

## <a name="add-support-for-asynchronous-code"></a>非同期コードのサポートを追加する

このアプリではクラウド リソースが使用されているため、コードは非同期で実行されます。

1. 非同期で実行されるように **Main** メソッドを更新します。 **void** を **async Task** 戻り値に変更します。

   ```csharp
   static async Task Main(string[] args)
   ```

1. **Program.cs** ファイルを保存します。

## <a name="create-a-queue"></a>キューを作成する

Azure API を呼び出す前に、Azure portal から資格情報を取得する必要があります。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>アプリに接続文字列を追加する

ストレージ アカウントにアクセスできるよう、アプリに接続文字列を追加します。

1. Visual Studio Code に戻ります。

1. **Main** メソッドで、`Console.WriteLine("Hello World!");` コードを次の行に置き換えます。環境変数から接続文字列を取得するものです。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. キュー オブジェクトを作成する次のコードを **Main** に追加します。このキュー オブジェクトを後で送信メソッドと受信メソッドに渡します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. ファイルを保存します。

## <a name="insert-messages-into-the-queue"></a>キューにメッセージを挿入する

キューにメッセージを送信する新しいメソッドを作成します。

1. 次の **InsertMessageAsync** メソッドを **Program** クラスに追加します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   このメソッドには、キューの参照が渡されます。 まだキューが存在しない場合は、[CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) を呼び出すことで新しいキューが作成されます。 その後、[SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) を呼び出して *newMessage* をキューに追加します。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   このメソッドには、キューの参照が渡されます。 まだキューが存在しない場合は、[CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) を呼び出すことで新しいキューが作成されます。 次に、[AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) を呼び出すことでキューに *newMessage* が追加されます。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **(省略可)** 既定では、メッセージの最大 Time to Live は 7 日に設定されます。 メッセージの有効期限には、任意の正の数値を指定できます。 次のコード スニペットは、有効期限の "*ない*" メッセージを追加します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    有効期限のないメッセージを追加するには、**SendMessageAsync** への呼び出しで `Timespan.FromSeconds(-1)` を使用します。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    有効期限のないメッセージを追加するには、**AddMessageAsync** への呼び出しで `Timespan.FromSeconds(-1)` を使用します。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. ファイルを保存します。

キュー メッセージは、UTF-8 エンコードを使用して、XML 要求と互換性のある形式にする必要があります。 メッセージの許容される最大サイズは 64 KB です。 メッセージにバイナリ データが含まれている場合は、メッセージを [Base64 でエンコード](/dotnet/api/system.convert.tobase64string)してください。

## <a name="dequeue-messages"></a>メッセージをデキューする

キューからメッセージを取得する新しいメソッドを作成します。 メッセージが正常に受け取られたら、何度も処理されないよう、キューから削除しておくことが重要です。

1. **RetrieveNextMessageAsync** という新しいメソッドを **Program** クラスに追加します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   このメソッドは、[ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) を呼び出してキューからメッセージを受け取ります。第 1 パラメーターに 1 を渡すと、キューにおける次のメッセージだけが取得されます。 メッセージが受け取られたら、[DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) を呼び出し、キューからそれを削除します。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   このメソッドは、[GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) を呼び出し、キューからメッセージを受け取ります。 メッセージが受け取られたら、[DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) を呼び出し、キューからそれを削除します。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. ファイルを保存します。

## <a name="delete-an-empty-queue"></a>空のキューを削除する

プロジェクトの終わりに、作成したリソースを引き続き必要とするかどうかを判断することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 キューが存在するが空の場合、削除して良いかどうかをユーザーに確認してください。

1. **RetrieveNextMessageAsync** メソッドを拡張し、空のキューを削除するプロンプトを追加します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. ファイルを保存します。

## <a name="check-for-command-line-arguments"></a>コマンドライン引数を確認する

コマンドラインの引数がアプリに渡されている場合、それらがキューに追加するメッセージであると想定してください。 引数を結合し、文字列を作ります。 前に追加した **InsertMessageAsync** メソッドを呼び出し、この文字列をメッセージ キューに追加します。

コマンドラインの引数がない場合、取得操作を試行します。 **RetrieveNextMessageAsync** メソッドを呼び出し、キューの次のメッセージを取得します。

最後に、**Console.ReadLine** を呼び出し、ユーザーの入力を待ってから終了します。

1. コマンドラインの引数を確認し、ユーザーの入力を待つよう、**Main** メソッドを拡張します。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. ファイルを保存します。

## <a name="complete-code"></a>完成したコード

このプロジェクトの完成したコードは次のようになります。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. プロジェクト ディレクトリのコマンド ラインから、次の dotnet コマンドを実行し、プロジェクトをビルドします。

   ```console
   dotnet build
   ```

1. プロジェクトが正常にビルドされたら、次のコマンドを実行し、最初のメッセージをキューに追加します。

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

1. コマンドラインの引数なしでアプリを実行し、キューの最初のメッセージを受け取り、削除します。

   ```console
   dotnet run
   ```

1. すべてのメッセージが削除されるまでアプリの実行を続けます。 さらに 1 回実行すると、キューが空であるというメッセージとキューを削除するためのプロンプトが表示されます。

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

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

1. キューを作成する
1. メッセージをキューに追加し、キューから削除する
1. Azure ストレージ キューを削除する

詳細については Azure キューのクイックスタートでご確認ください。

> [!div class="nextstepaction"]
> [キューのクイックスタート (ポータル)](storage-quickstart-queues-portal.md)

- [キューのクイックスタート (.NET)](storage-quickstart-queues-dotnet.md)
- [キューのクイックスタート (Java)](storage-quickstart-queues-java.md)
- [キューのクイックスタート (Python)](storage-quickstart-queues-python.md)
- [キューのクイックスタート (JavaScript)](storage-quickstart-queues-nodejs.md)
