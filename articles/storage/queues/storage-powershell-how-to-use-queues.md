---
title: PowerShell を使用し、Azure Queue Storage を操作する | Microsoft Docs
description: PowerShell を使用し、Azure Queue Storage を操作する方法
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 9992673ab36d5b4b2cc1ca18a5108107c14a1eb1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488953"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure PowerShell を使用し、Azure Queue Storage を操作する

Azure キュー ストレージは、HTTP または HTTPS を介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 詳細については、[Azure Queue の概要](storage-queues-introduction.md)に関するページをご覧ください。 このハウツー記事では、Queue Storage の一般的な操作について取り上げます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * キューを作成する
> * キューを取得する
> * メッセージを追加する
> * メッセージを読む
> * メッセージを削除する
> * キューを削除する

このハウツーには、Azure PowerShell モジュール Az バージョン 0.7 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

キューのデータ プレーン用の PowerShell コマンドレットはありません。 メッセージの追加、読み取り、削除などのデータ プレーン操作を実行するには、PowerShell で公開されるとおりに、.NET ストレージ クライアント ライブラリを使用する必要があります。 メッセージ オブジェクトを作成し、AddMessage などのコマンドを使用して、そのメッセージに対して操作を実行できます。 この記事では、その方法について説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>場所の一覧を取得する

使用する場所がわからない場合、利用できる場所を一覧表示できます。 一覧が表示されたら、使用する場所を見つけます。 この演習では **eastus** を使用します。 将来使用するために、これを変数 **location** に保存します。

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 将来使用するために、リソース グループ名を変数に保存します。 この例では、*howtoqueuesrg* という名前のリソース グループが *eastus* リージョンに作成されます。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>ストレージ アカウントの作成

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) を使用して、ローカル冗長ストレージ (LRS) で標準の汎用ストレージ アカウントを作成します。 使用されるストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントで作業するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>キューを作成する

次の例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) コマンドレットを呼び出して、"queuename" という名前のキューを作成します。

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Azure Queue サービスでの名前付け規則の詳細については、「 [キューおよびメタデータの名前付け](https://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。

## <a name="retrieve-a-queue"></a>キューを取得する

あるストレージ アカウント内の特定のキューまたはすべてのキューの一覧を照会して取得できます。 次の例では、ストレージ アカウントの全部のキューと特定のキューを取得する方法を示しています。いずれのコマンドでも [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) コマンドレットが使用されます。

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する

キュー内の実際のメッセージに影響を与える操作では、PowerShell で公開されるとおりに、.NET ストレージ クライアント ライブラリを使用します。 キューにメッセージを追加するには、メッセージ オブジェクト [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](https://msdn.microsoft.com/library/azure/jj732474.aspx) クラスの新しいインスタンスを作成します。 次に、 [AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出します。 CloudQueueMessage は、文字列 (UTF-8 形式) またはバイト配列から作成できます。

次の例は、メッセージをキューに追加する方法を示しています。

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage,$($queue.CloudQueue.GetType().Assembly.FullName)" `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

[Azure Storage Explorer](https://storageexplorer.com) を使用する場合、Azure アカウントに接続し、ストレージ アカウントのキューを表示し、キューにドリルダウンし、そのキューに関するメッセージを表示できます。 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>キューのメッセージを読み、その後、削除する

メッセージは、先入れ先出しをできるだけ試すという方式で読まれます。 最初に届いたメッセージが最初に読まれるという保証はありません。 キューのメッセージを読むと、そのキューを見ているその他すべてのプロセスでそのメッセージが見えなくなります。 この措置によって、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合でも、コードの別のインスタンスで同じメッセージを取得し、もう一度処理できます。  

この**非表示タイムアウト**によって、メッセージが見えなくなる時間が定義されます。この時間を過ぎると、再び表示され、処理できます。 既定値は 30 秒です。 

コードは 2 つの手順でキューのメッセージを読みます。 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) メソッドを呼び出すと、キュー内の次のメッセージが取得されます。 **GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 キューからのメッセージの削除を完了するには、[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) メソッドを呼び出します。 

次の例では、3 つのキュー メッセージを読み、それから 10 秒 (非表示タイムアウト) 待ちます。 その後、3 つのメッセージを再び読み、読んだら **DeleteMessage** を呼び出し、メッセージを削除します。 メッセージの削除後にキューを読もうとすると、$queueMessage が NULL として返されます。

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>キューを削除する

キューとキューに含まれるすべてのメッセージを削除するには、Remove-AzStorageQueue コマンドレットを呼び出します。 次の例は、Remove-AzStorageQueue コマンドレットを使用し、この演習で使用したキューを削除する方法を示しています。

```powershell
# Delete the queue 
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この演習で作成したすべてのアセットを削除するには、リソース グループを削除します。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この場合、作成されたストレージ アカウントとリソース グループ自体が削除されます。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このハウツー記事では、次のような、PowerShell による基本的な Queue Storage 管理について説明しました。

> [!div class="checklist"]
> * キューを作成する
> * キューを取得する
> * メッセージを追加する
> * 次のメッセージを読む
> * メッセージを削除する 
> * キューを削除する

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage コマンドレット

* [Storage PowerShell コマンドレット](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー

* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
