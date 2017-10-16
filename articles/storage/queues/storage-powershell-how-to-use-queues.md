---
title: "PowerShell を使用し、Azure Queue Storage を操作する | Microsoft Docs"
description: "チュートリアル - PowerShell を使用し、Azure Queue Storage を操作する"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 357d8db329a6a3c782753804d681029fdb07b5f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure PowerShell を使用し、Azure Queue Storage を操作する

Azure Queue Storage は、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 詳細については、[Azure Queue の概要](storage-queues-introduction.md)に関するページをご覧ください。 このチュートリアルでは、Queue Storage の一般的な操作について取り上げます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * キューを作成する
> * キューを取得する
> * メッセージを追加する
> * メッセージを読む
> * メッセージを削除する 
> * キューを削除する

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>場所の一覧を取得する

使用する場所がわからない場合、利用できる場所を一覧表示できます。 一覧が表示されたら、使用する場所を見つけます。 このチュートリアルでは **eastus** を使用します。 将来使用するために、これを変数 **location** に保存します。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 将来使用するために、リソース グループ名を変数に保存します。 この例では、*howtoqueuesrg* という名前のリソース グループが *eastus* リージョンに作成されます。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>[ストレージ アカウントの作成]

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を利用し、ローカル冗長ストレージ (LRS) で標準の汎用ストレージ アカウントを作成します。 使用されるストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントで作業するとき、資格情報を繰り返し入力する代わりに、このコンテキストを参照します。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>キューを作成する

次の例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。 次に、[New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) コマンドレットを呼び出して、"queuename" という名前のキューを作成します。

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Azure Queue サービスでの名前付け規則の詳細については、「 [キューおよびメタデータの名前付け](http://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。

## <a name="retrieve-a-queue"></a>キューを取得する

あるストレージ アカウント内の特定のキューまたはすべてのキューの一覧を照会して取得できます。 次の例では、ストレージ アカウントの全部のキューと特定のキューを取得する方法を示しています。いずれのコマンドでも [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) コマンドレットが使用されます。

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する

キューにメッセージを追加するには、まず [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) クラスの新しいインスタンスを作成します。 次に、 [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出します。 CloudQueueMessage は、文字列 (UTF-8 形式) またはバイト配列から作成できます。

次の例は、メッセージをキューに追加する方法を示しています。

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

[Azure Storage Explorer](http://storageexplorer.com) を使用する場合、Azure アカウントに接続し、ストレージ アカウントのキューを表示し、その 1 つにドリルダウンし、そのキューに関するメッセージを表示できます。 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>キューのメッセージを読み、その後、削除する

メッセージは、先入れ先出しをできるだけ試すという方式で読まれます。 最初に届いたメッセージが最初に読まれるという保証はありません。 キューのメッセージを読むと、そのキューを見ているその他すべてのプロセスでそのメッセージが見えなくなります。 この措置によって、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合でも、コードの別のインスタンスで同じメッセージを取得し、もう一度処理できます。  

この**非表示時間**によって、メッセージが見えなくなる時間が定義されます。この時間を過ぎると、再び表示され、処理できます。 既定値は 30 秒です。 

コードは 2 つの手順でキューのメッセージを読みます。 [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) メソッドを呼び出すと、キュー内の次のメッセージが取得されます。 **GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 キューからのメッセージの削除を完了するには、[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) メソッドを呼び出します。 

次の例では、3 つのキュー メッセージを読み、それから 10 秒 (非表示時間) 待ちます。 その後、3 つのメッセージを再び読み、読んだら **DeleteMessage** を呼び出し、メッセージを削除します。 メッセージの削除後にキューを読もうとすると、$queueMessage が NULL として返されます。

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>キューを削除する
キューとキューに含まれるすべてのメッセージを削除するには、Remove-AzureStorageQueue コマンドレットを呼び出します。 次の例は、Remove-AzureStorageQueue コマンドレットを使用し、この演習で使用したキューを削除する方法を示しています。

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この演習で作成したすべてのアセットを削除するには、リソース グループを削除します。それによって、そのグループに含まれるすべてのリソースが削除されます。 この場合、作成されたストレージ アカウントとリソース グループ自体が削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のような、PowerShell による基本的な Queue Storage 管理について説明しました。

> [!div class="checklist"]
> * キューを作成する
> * キューを取得する
> * メッセージを追加する
> * 次のメッセージを読む
> * メッセージを削除する 
> * キューを削除する

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage コマンドレット
* [Storage PowerShell コマンドレット](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー
* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。