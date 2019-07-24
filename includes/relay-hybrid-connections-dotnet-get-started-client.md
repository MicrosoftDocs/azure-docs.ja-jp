---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 2d6836b2bf667e4170e67a95dc1daad72a769eb9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735997"
---
### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio で、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成します。

### <a name="add-the-relay-nuget-package"></a>Relay NuGet パッケージを追加する

1. 新しく作成したプロジェクトを右クリックしてから、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** を選択し、**Microsoft.Azure.Relay** を検索します。 検索結果から、"**Microsoft Azure Relay**" を選択します。 
3. **[インストール]** を選択してインストールを完了します。 ダイアログ ボックスを閉じます。

### <a name="write-code-to-send-messages"></a>メッセージを送信するコードを記述する

1. Program.cs ファイルの先頭にある既存の `using` ステートメントを、次の `using` ステートメントに置き換えます。
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. ハイブリッド接続の詳細に関する定数を `Program` クラスに追加します。 中かっこ内のプレースホルダーを、ハイブリッド接続の作成時に取得した値に置き換えます。 必ず完全修飾名前空間名を使用してください。
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. 次のメソッドを `Program` クラスに追加します。
   
    ```csharp
    private static async Task RunAsync()
    {
        Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
        // Create a new hybrid connection client.
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Initiate the connection.
        var relayConnection = await client.CreateConnectionAsync();
   
        // Run two concurrent loops on the connection. One 
        // reads input from the console and writes it to the connection 
        // with a stream writer. The other reads lines of input from the 
        // connection with a stream reader and writes them to the console. 
        // Entering a blank line shuts down the write task after 
        // sending it to the server. The server then cleanly shuts down
        // the connection, which terminates the read task.
   
        var reads = Task.Run(async () => {
            // Initialize the stream reader over the connection.
            var reader = new StreamReader(relayConnection);
            var writer = Console.Out;
            do
            {
                // Read a full line of UTF-8 text up to newline.
                string line = await reader.ReadLineAsync();
                // If the string is empty or null, you are done.
                if (String.IsNullOrEmpty(line))
                    break;
                // Write to the console.
                await writer.WriteLineAsync(line);
            }
            while (true);
        });
   
        // Read from the console and write to the hybrid connection.
        var writes = Task.Run(async () => {
            var reader = Console.In;
            var writer = new StreamWriter(relayConnection) { AutoFlush = true };
            do
            {
                // Read a line from the console.
                string line = await reader.ReadLineAsync();
                // Write the line out, also when it's empty.
                await writer.WriteLineAsync(line);
                // Quit when the line is empty,
                if (String.IsNullOrEmpty(line))
                    break;
            }
            while (true);
        });
   
        // Wait for both tasks to finish.
        await Task.WhenAll(reads, writes);
        await relayConnection.CloseAsync(CancellationToken.None);
    }
    ```
4. `Program` クラスの `Main` メソッドに次のコード行を追加します。
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs は次のようになります。
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
                // Create a new hybrid connection client.
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Initiate the connection.
                var relayConnection = await client.CreateConnectionAsync();
   
                // Run two concurrent loops on the connection. One 
                // reads input from the console and then writes it to the connection 
                // with a stream writer. The other reads lines of input from the 
                // connection with a stream reader and then writes them to the console. 
                // Entering a blank line shuts down the write task after 
                // sending it to the server. The server then cleanly shuts down
                // the connection, which terminates the read task.
   
                var reads = Task.Run(async () => {
                    // Initialize the stream reader over the connection.
                    var reader = new StreamReader(relayConnection);
                    var writer = Console.Out;
                    do
                    {
                        // Read a full line of UTF-8 text up to newline.
                        string line = await reader.ReadLineAsync();
                        // If the string is empty or null, you are done.
                        if (String.IsNullOrEmpty(line))
                            break;
                        // Write to the console.
                        await writer.WriteLineAsync(line);
                    }
                    while (true);
                });
   
                // Read from the console and write to the hybrid connection.
                var writes = Task.Run(async () => {
                    var reader = Console.In;
                    var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                    do
                    {
                        // Read a line from the console.
                        string line = await reader.ReadLineAsync();
                        // Write the line out, also when it's empty.
                        await writer.WriteLineAsync(line);
                        // Quit when the line is empty.
                        if (String.IsNullOrEmpty(line))
                            break;
                    }
                    while (true);
                });
   
                // Wait for both tasks to finish.
                await Task.WhenAll(reads, writes);
                await relayConnection.CloseAsync(CancellationToken.None);
            }
        }
    }
    ```

