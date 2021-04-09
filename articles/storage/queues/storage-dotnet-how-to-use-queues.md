---
title: .NET を使用して Azure Queue Storage の使用を開始する - Azure Storage
description: Azure Queue Storage を使用すると、アプリケーション コンポーネント間での信頼性の高い非同期メッセージングが提供されます。 クラウド メッセージングにより、アプリケーション コンポーネントのスケールを個別に変更できます。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585753"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>.NET を使用して Azure Queue Storage の使用を開始する

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概要

Azure Queue Storage を使用すると、アプリケーション コンポーネント間のクラウド メッセージングが提供されます。 スケールを考慮したアプリケーションを設計では、多くの場合、アプリケーション コンポーネントは分離されるため、個別にスケーリングすることができます。 Queue Storage を使用すれば、アプリケーション コンポーネントがクラウド、デスクトップ、オンプレミスのサーバー、またはモバイル デバイス上のいずれで実行されている場合でも、アプリケーション コンポーネント間に非同期メッセージングを提供できます。 また、非同期タスクの管理とプロセス ワークフローの構築も Queue Storage でサポートされています。

### <a name="about-this-tutorial"></a>このチュートリアルについて

このチュートリアルでは、Azure Queue Storage を使用していくつかの一般的なシナリオの .NET コードを記述する方法を示します。 紹介するシナリオは、キューの作成と削除、およびキュー メッセージの追加、読み取り、削除です。

**推定所要時間:** 45 分

### <a name="prerequisites"></a>前提条件

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Azure ストレージ アカウント](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>開発環境を設定する

次に、このガイドのコード例を試すことができるように、Visual Studio で開発環境を設定します。

### <a name="create-a-windows-console-application-project"></a>Windows コンソール アプリケーション プロジェクトの作成

Visual Studio で、新しい Windows コンソール アプリケーションを作成します。 次の手順では、Visual Studio 2019 でコンソール アプリケーションを作成する方法を説明します。 この手順は Visual Studio の他のバージョンでも同様です。

1. **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
2. **[プラットフォーム]**  >  **[Windows]** の順に選択します
3. **[コンソール アプリ (.NET Framework)]** を選択します。
4. **[次へ]** を選択します
5. **[プロジェクト名]** フィールドに、アプリケーションの名前を入力します
6. **[作成]**

このチュートリアルのすべてのコード例は、コンソール アプリケーションの `Program.cs` ファイルの `Main()` メソッドに追加できます。

Azure クラウド サービス、Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなど、どの種類の .NET アプリケーションでも Azure Storage クライアント ライブラリを使用できます。 このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet を使用した必要なパッケージのインストール

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

このチュートリアルを完了するには、プロジェクトで以下の 4 つのパッケージを参照する必要があります。

- [.NET 用 Azure.Core ライブラリ](https://www.nuget.org/packages/azure.core/): このパッケージには、最新の .NET Azure SDK クライアント ライブラリ用の共有プリミティブ、抽象化、およびヘルパーが用意されています。
- [.NET 用 Azure.Storage.Common クライアント ライブラリ](https://www.nuget.org/packages/azure.storage.common/): このパッケージには、他の Azure Storage クライアント ライブラリによって共有されるインフラストラクチャが用意されています。
- [.NET 用 Azure.Storage.Queues クライアント ライブラリ](https://www.nuget.org/packages/azure.storage.queues/): このパッケージを使用すると、Azure Queue Storage を操作して、クライアントからアクセスされる可能性のあるメッセージを格納できます。
- [.NET 用 System.Configuration.ConfigurationManager ライブラリ](https://www.nuget.org/packages/system.configuration.configurationmanager/): このパッケージには、クライアント アプリケーション用の構成ファイルへのアクセス権が用意されています。

NuGet を使用してこれらのパッケージを取得できます。 次の手順に従います。

1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。
1. **[参照]** を選択します。
1. `Azure.Storage.Queues` をオンラインで検索し、 **[インストール]** を選択して Azure Storage クライアント ライブラリとその依存関係をインストールします。 これにより、キュー ライブラリの依存関係である Azure.Storage.Common と Azure.Core libraries もインストールされます。
1. `System.Configuration.ConfigurationManager` をオンラインで検索し、 **[インストール]** を選択して Configuration Manager をインストールします。

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

このチュートリアルを完了するには、プロジェクトで以下の 3 つのパッケージを参照する必要があります。

- [.NET 用 Microsoft.Azure.Storage.Common クライアント ライブラリ](https://www.nuget.org/packages/microsoft.azure.storage.common/): このパッケージを使用すると、ストレージ アカウント内のデータ リソースにプログラムでアクセスできます。
- [.NET 用 Microsoft Azure Queue Storage クライアント ライブラリ](https://www.nuget.org/packages/microsoft.azure.storage.queue/): このクライアント ライブラリを使用すると、Azure Queue Storage を操作して、クライアントからアクセスされる可能性のあるメッセージを格納できます。
- [.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/microsoft.azure.configurationmanager/):このパッケージには、アプリケーションの実行場所に関係なく、構成ファイルの接続文字列を解析するためのクラスが用意されています。

NuGet を使用してこれらのパッケージを取得できます。 次の手順に従います。

1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。
1. **[参照]** を選択します。
1. `Microsoft.Azure.Storage.Queue` をオンラインで検索し、 **[インストール]** を選択して Azure Storage クライアント ライブラリとその依存関係をインストールします。 これによって、キュー ライブラリの依存関係である Microsoft.Azure.Storage.Common ライブラリもインストールされます。
1. `Microsoft.Azure.ConfigurationManager` をオンラインで検索し、 **[インストール]** を選択して Azure Configuration Manager をインストールします。

---

### <a name="determine-your-target-environment"></a>ターゲット環境の決定

このガイドの例を実行するための環境オプションとして次の 2 つがあります。

- クラウド内の Azure ストレージ アカウントに対してコードを実行できます。
- Azurite ストレージ エミュレーターに対してコードを実行できます。 Azurite は、クラウド内の Azure Storage アカウントをエミュレートするローカル環境です。 Azurite を使用すると、アプリケーションの開発中にコードのテストとデバッグを無料で実行できます。 エミュレーターでは既知のアカウントとキーを使用します。 詳細については、「[ローカルでの Azure Storage の開発とテストに Azurite エミュレーターを使用する](../common/storage-use-azurite.md)」を参照してください。

> [!NOTE]
> ストレージ エミュレーターをターゲットにすると、Azure Storage に関連する利用料金の発生を回避できます。 ただし、クラウド内の Azure ストレージ アカウントをターゲットとしても、このチュートリアルを実行するための利用料金はごくわずかです。

## <a name="get-your-storage-connection-string"></a>ストレージ接続文字列の取得

.NET 用 Azure Storage クライアント ライブラリでは、ストレージ接続文字列を使用して、ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を構成できます。 詳細については、「[ストレージ アカウントのアクセス キーの管理](../common/storage-account-keys-manage.md)」を参照してください。

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal で資格情報をコピーする

サンプル コードで、ストレージ アカウントへのアクセスを承認する必要があります。 承認するには、ストレージ アカウントの資格情報を接続文字列形式でアプリケーションに提供します。 ストレージ アカウントの資格情報を表示するには、次のように操作します。

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 自分のストレージ アカウントを探します。
3. ストレージ アカウントの概要の **[設定]** セクションで、 **[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。
4. **[Key1]** の **[接続文字列]** の値を見つけて **[コピー]** ボタンをクリックし、接続文字列をコピーします。 すぐ後の手順で、接続文字列の値を環境変数に追加します。

    ![Azure portal から接続文字列をコピーする方法を示すスクリーンショット](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

接続文字列の詳細については、[Azure Storage の接続文字列の構成](../common/storage-configure-connection-string.md)に関するページを参照してください。

> [!NOTE]
> ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 ストレージ アカウント キーは常に慎重に保護してください。 このキーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキスト ファイルに保存したりしないでください。 キーが侵害されたと思われる場合は、Azure Portal を使用してキーを再生成してください。

ストレージ接続文字列を管理するには、構成ファイルの中に保持することをお勧めします。 接続文字列を構成するには、Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。 ここに示す `<appSettings>` 要素の内容を追加します。 `connection-string` を、ポータルのストレージ アカウントからコピーした値に置き換えます。

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

たとえば、構成設定は次のようになります。

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Azurite ストレージ エミュレーターをターゲットとする場合、既知のアカウントの名前とキーにマップされるショートカットを使用できます。 この場合、接続文字列の設定は次のようになります。

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>using ディレクティブを追加する

次の `using` ディレクティブを `Program.cs` ファイルの先頭に追加します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Queue Storage クライアントを作成する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

[`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) クラスを使用すると、Queue Storage に格納されているキューを取得できます。 サービス クライアントを作成する方法の 1 つを次に示します。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

[`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) クラスを使用すると、Queue Storage に格納されているキューを取得できます。 サービス クライアントを作成する方法の 1 つを次に示します。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

これで、Queue Storage に対してデータの読み取りと書き込みを実行するコードを記述する準備が整いました。

## <a name="create-a-queue"></a>キューを作成する

この例では、キューを作成する方法を示します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>メッセージをキューに挿入する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

既存のキューにメッセージを挿入するには、[`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) メソッドを呼び出します。 メッセージには、文字列 (UTF-8 形式) またはバイト配列のいずれかを指定できます。 次のコードでは、キューが作成され (それが存在しない場合)、メッセージが挿入されます。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

既存のキューにメッセージを挿入するには、最初に新しい [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) を作成します。 次に [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) メソッドを呼び出します。 `CloudQueueMessage` は、文字列 (UTF-8 形式) またはバイト配列で作成できます。 キューを作成し (それが存在しない場合)、メッセージ `Hello, World` を挿入するコードを次に示します。既存のキューにメッセージを挿入するには、最初に新しい [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) を作成します。 次に [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) メソッドを呼び出します。 `CloudQueueMessage` は、文字列 (UTF-8 形式) またはバイト配列で作成できます。 キューを作成し (それが存在しない場合)、メッセージ `Hello, World` を挿入するコードを次に示します。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>次のメッセージをピークする

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

[`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) メソッドを呼び出せば、キュー内のメッセージをキューから削除せずにクイック表示することができます。 `maxMessages` パラメーターの値を渡さない場合、既定では 1 つのメッセージがピークされます。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

[`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにクイック表示することができます。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。 これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。 この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。 通常は、さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合はメッセージを削除するようにします。 こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>次のメッセージをデキューする

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

キューからのメッセージのデキューは、2 つの手順で行います。 [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) を呼び出すと、キュー内の次のメッセージが取得されます。 `ReceiveMessages` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 キューからのメッセージの削除を完了するには、[`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) を呼び出すことも必要です。 このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 ご自分のコードで、メッセージが処理された直後に `DeleteMessage` を呼び出します。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

コードでは、2 つの手順でキューからメッセージをデキューします。 [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) を呼び出すと、キュー内の次のメッセージが取得されます。 `GetMessage` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 キューからのメッセージの削除を完了するには、[`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) を呼び出すことも必要です。 このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 ご自分のコードで、メッセージが処理された直後に `DeleteMessage` を呼び出します。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Async-Await パターンを一般的なキュー ストレージ API で使用する

この例では、Async-Await パターンを一般的なキュー ストレージ API で使用する方法を示します。 このサンプルは、特定のメソッドの非同期バージョンをそれぞれ呼び出しています。これは、各メソッドの `Async` 接尾辞によって確認できます。 非同期のメソッド使用時、Async-Await パターンを使用すると、呼び出しが完了するまでローカルでの実行が中断されます。 この動作により、現在のスレッドで別の作業を実行できるようになるため、パフォーマンスのボトルネックを回避し、アプリケーションの全体的な応答性を向上させることができます。 .NET での Async-Await パターンの使用方法の詳細については、 [Async と Await (C# と Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>メッセージをデキューするためのその他のオプションを使用する

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

次のコード例では、[`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、`foreach` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。 この 5 分間は、すべてのメッセージに対して同時に開始されます。そのため、`ReceiveMessages` の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

次のコード例では、[`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、`foreach` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。 この 5 分間は、すべてのメッセージに対して同時に開始されます。そのため、`GetMessages` の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>キューの長さを取得する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

キュー内のメッセージの概数を取得できます。 [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) メソッドからは、メッセージ数を含むキューのプロパティが返されます。 [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) プロパティには、キュー内のメッセージの概数が格納されます。 この数は、キュー内の実際のメッセージ数より少なくなることはありませんが、多くなる可能性があります。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

キュー内のメッセージの概数を取得できます。 [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) メソッドからは、メッセージ数をはじめとするキューの属性が返されます。 [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) プロパティを使用することで、Queue Storage を呼び出さずに、`FetchAttributes` メソッドによって取得された最後の値を返します。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>キューを削除する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

キューおよびそれに格納されているすべてのメッセージを削除するには、キュー オブジェクトの [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) メソッドを呼び出します。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

キューおよびそれに格納されているすべてのメッセージを削除するには、キュー オブジェクトの [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) メソッドを呼び出します。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>次のステップ

これで、Queue Storage の基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

- 利用可能な API の詳細については、Queue Storage のリファレンス ドキュメントを参照してください。
  - [.NET 用 Azure Storage クライアント ライブラリ リファレンス](/dotnet/api/overview/azure/storage)
  - [Azure Storage REST API リファレンス](/rest/api/storageservices/)
- Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  - 構造化データを格納する方法については、[.NET を使用して Azure Table Storage を使用する方法](../../cosmos-db/tutorial-develop-table-dotnet.md)に関するページを参照してください。
  - 非構造化データを格納する方法については、「[.NET を使用して Azure Blob Storage を使用する](../blobs/storage-quickstart-blobs-dotnet.md)」を参照してください。
  - リレーショナル データを格納する方法については、「[.NET (C#) を使用して SQL Database に接続する](../../azure-sql/database/connect-query-dotnet-core.md)」を参照してください。
- Azure Storage で作業するために記述したコードを簡略化する方法については、「 [Azure WebJobs SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。
