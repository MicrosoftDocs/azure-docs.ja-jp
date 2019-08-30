---
title: Azure Queue Storage と Visual Studio 接続済みサービスの概要 (ASP.NET) | Microsoft Docs
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio の ASP.NET プロジェクトで Azure Queue Storage の使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 19cf2dd912968d0a5df8743c1e720776b8a949f0
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515991"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Azure Queue Storage と Visual Studio 接続済みサービスの概要 (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要

Azure Queue Storage は、アプリケーション コンポーネント間のクラウド メッセージングを備えています。 拡張性を重視してアプリケーションを設計する場合、通常、アプリケーション コンポーネントを個別に拡張できるように分離します。 Queue Storage は、アプリケーション コンポーネントがクラウド、デスクトップ、オンプレミスのサーバー、モバイル デバイスのいずれで実行されている場合でも、アプリケーション コンポーネント間の通信に非同期メッセージングを提供します。 Queue Storage ではまた、非同期タスクの管理とプロセス ワークフローの構築もサポートします。

このチュートリアルでは、Azure Queue Storage エンティティを使用していくつかの一般的なシナリオの ASP.NET コードを記述する方法を示します。 これらのシナリオでは、Azure キューの作成や、キュー メッセージの追加、変更、読み取り、削除などの一般的なタスクについて説明します。

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage アカウント](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC コントローラーを作成する 

1. **ソリューション エクスプローラー**で**コントローラー**を右クリックし、コンテキスト メニューで **[追加]、[コントローラー]** の順に選択します。

    ![ASP.NET MVC アプリケーションへのコントローラーの追加](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. **[スキャフォールディングの追加]** ダイアログ ボックスで **[MVC 5 コントローラー - 空]** を選択し、 **[追加]** を選択します。

    ![MVC コントローラーの種類を指定する](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. **[コントローラーの追加]** ダイアログで、コントローラーに *QueuesController* という名前を付けて、 **[追加]** を選択します。

    ![MVC コントローラー指定](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. 次の *using* ディレクティブを `QueuesController.cs` ファイルに追加します。

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>キューを作成する

次の手順では、キューの作成方法を説明します。

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。 

1. **ActionResult** を返す **CreateQueue** というメソッドを追加します。

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateQueue** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. 目的のキュー名への参照を表す **CloudQueue** オブジェクトを取得します。 **CloudQueueClient.GetQueueReference** メソッドでは、キュー ストレージに対する要求は行われません。 キューが存在するかどうかにかかわらず、参照が返されます。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. キューがまだない場合は、**CloudQueue.CreateIfNotExists** メソッドを呼び出してキューを作成します。 **CloudQueue.CreateIfNotExists** メソッドは、キューが存在しないため正常に作成された場合 **true** を返します。 それ以外の場合は、**false** が返されます。    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. キュー名で **ViewBag** を更新します。

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**CreateQueue**」と入力し、 **[追加]** を選択します。

1. `CreateQueue.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. アプリケーションを実行して **[キューの作成]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![キューの作成](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    前述したように、**CloudQueue.CreateIfNotExists** メソッドは、キューが存在しないため作成された場合にのみ **true** を返します。 そのため、キューが存在するときにアプリを実行した場合、メソッドは **false** を返します。 アプリを複数回実行するには、アプリを再実行する前にキューを削除する必要があります。 キューの削除は、**CloudQueue.Delete** メソッドを使用して行うことができます。 また、[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) または [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用してキューを削除することもできます。  

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する

[キューを作成](#create-a-queue)したら、そのキューにメッセージを追加できます。 このセクションでは、*test-queue* のキューにメッセージを追加する方法について説明します。 

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。

1. **ActionResult** を返す **AddMessage** というメソッドを追加します。

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **AddMessage** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. キューへの参照を表す **CloudQueueContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. キューに追加するメッセージを表す **CloudQueueMessage** オブジェクトを作成します。 **CloudQueueMessage** オブジェクトは、文字列 (UTF-8 形式) またはバイト配列から作成できます。

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. **CloudQueue.AddMessage** メソッドを呼び出して、メッセージをキューに追加します。

    ```csharp
    queue.AddMessage(message);
    ```

1. **ViewBag** プロパティを 2 つ作成し、ビュー内で表示されるように設定します。

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**AddMessage**」と入力し、 **[追加]** を選択します。

1. `AddMessage.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. アプリケーションを実行して **[メッセージの追加]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![メッセージの追加](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

「[キューからメッセージを削除せずに読み取る](#read-a-message-from-a-queue-without-removing-it)」と「[キューからメッセージを読み取って削除する](#read-and-remove-a-message-from-a-queue)」の 2 つのセクションでは、キューからメッセージを読み取る方法を説明します。    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>キューからメッセージを削除せずに読み取る

次のセクションでは、キューに格納されたメッセージをピークする (最初のメッセージを削除せずに読み取る) 方法を説明します。  

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。

1. **ActionResult** を返す **PeekMessage** というメソッドを追加します。

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **PeekMessage** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. キューへの参照を表す **CloudQueueContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.PeekMessage** メソッドを呼び出して、キュー内の最初のメッセージをキューから削除せずに読み取ります。 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. キュー名と読まれたメッセージの 2 つの値で **ViewBag** を更新します。 **CloudQueueMessage** オブジェクトは、オブジェクトの値を取得するためのプロパティを 2 つ公開しています。**CloudQueueMessage.AsBytes** と **CloudQueueMessage.AsString** です。 **AsString** (この例で使用) は文字列を返し、一方で **AsBytes** はバイト配列を返します。

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**PeekMessage**」と入力し、 **[追加]** を選択します。

1. `PeekMessage.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. アプリケーションを実行して **[メッセージのピーク]** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![メッセージのピーク](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>キューからメッセージを読み取って削除する

このセクションでは、キューからメッセージを読み取って削除する方法を説明します。   

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。

1. **ActionResult** を返す **ReadMessage** というメソッドを追加します。

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. キューへの参照を表す **CloudQueueContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.GetMessage** メソッドを呼び出して、キュー内の最初のメッセージを読み取ります。 **CloudQueue.GetMessage** メソッドは、メッセージの処理中に他のコードによって変更または削除されないように、メッセージを読み取る他のコードに対してそのメッセージを (既定で) 30 秒間非表示にします。 メッセージを非表示にする時間を変更するには、**CloudQueue.GetMessage** メソッドに渡す **visibilityTimeout** パラメーターを変更します。

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. **CloudQueueMessage.Delete** メソッドを呼び出して、キューからメッセージを削除します。

    ```csharp
    queue.DeleteMessage(message);
    ```

1. メッセージが削除された **ViewBag** と、キュー名を更新します。

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**ReadMessage**」と入力し、 **[追加]** を選択します。

1. `ReadMessage.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. アプリケーションを実行して **[Read/Delete message]\(メッセージの読み取り/削除\)** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![メッセージの読み取りおよび削除](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>キューの長さを取得する

このセクションでは、キューの長さ (メッセージ数) を取得する方法を説明します。 

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。

1. **ActionResult** を返す **GetQueueLength** というメソッドを追加します。

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. キューへの参照を表す **CloudQueueContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.FetchAttributes** メソッドを呼び出して、キューの属性 (長さを含む) を取得します。 

    ```csharp
    queue.FetchAttributes();
    ```

1. **CloudQueue.ApproximateMessageCount** プロパティにアクセスして、キューの長さを取得します。
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. そのキュー名を持つ **ViewBag** と、その長さを更新します。

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**GetQueueLength**」と入力し、 **[追加]** を選択します。

1. `GetQueueLengthMessage.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. アプリケーションを実行して **[Get queue length]\(キューの長さの取得\)** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![キューの長さを取得する](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>キューを削除する
このセクションでは、キューを削除する方法について説明します。 

> [!NOTE]
> 
> このセクションでは、[開発環境の設定手順](#set-up-the-development-environment)を完了していることを前提にしています。 

1. `QueuesController.cs` ファイルを開きます。

1. **ActionResult** を返す **DeleteQueue** というメソッドを追加します。

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **DeleteQueue** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。( *&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Queue サービス クライアントを表す **CloudQueueClient** オブジェクトを取得します。
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. キューへの参照を表す **CloudQueueContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.Delete** メソッドを呼び出して、**CloudQueue** オブジェクトによって表されるキューを削除します。

    ```csharp
    queue.Delete();
    ```

1. そのキュー名を持つ **ViewBag** と、その長さを更新します。

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、 **[キュー]** を右クリックし、コンテキスト メニューで **[追加] > [ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「**DeleteQueue**」と入力し、 **[追加]** を選択します。

1. `DeleteQueue.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. **ソリューション エクスプローラー**で、 **[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. アプリケーションを実行して **[Get queue length]\(キューの長さの取得\)** を選択し、次のスクリーンショットと同様の結果が表示されることを確認します。
  
    ![キューの削除](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>次の手順
Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。

  * [Azure Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [テーブル ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
