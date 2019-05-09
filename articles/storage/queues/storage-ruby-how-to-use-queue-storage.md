---
title: Ruby から Queue Storage を使用する方法 | Microsoft Docs
description: Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。 コード サンプルは Ruby で記述されています。
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 7ebb4326a8ec8a3382a5488ce3b966526bef446a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456274"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby から Queue ストレージを使用する方法
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要
このガイドでは、Microsoft Azure Queue ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Ruby Azure API を使用して記述されています。
キュー メッセージの**挿入**、**ピーク**、**取得**、**削除**と、**キューの作成と削除**の各シナリオについて説明します。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成
Ruby アプリケーションを作成します。 手順については、「[App Service on Linux での Ruby アプリの作成](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)」を参照してください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure ストレージを使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する
1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする
任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** と **AZURE\_STORAGE\_ACCESS_KEY** を読み取ります。 これらの環境変数が設定されていない場合は、 **Azure::QueueService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の [設定] ブレードで、 **[アクセス キー]** をクリックします。
4. 表示される [アクセス キー] ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。 
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。 

## <a name="how-to-create-a-queue"></a>方法:キューを作成する
次のコードは、 **Azure::QueueService** オブジェクトを作成し、これによってキューを操作できるようにします。

```ruby
azure_queue_service = Azure::QueueService.new
```

**create_queue()** メソッドを使用して、指定した名前のキューを作成します。

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>方法:メッセージをキューに挿入する
キューにメッセージを挿入するには、**create_message()** メソッドを使用し、新しいメッセージを作成してキューに追加します。

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする
**peek\_messages()** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 既定では、**peek\_messages()** は 1 つのメッセージを対象としてピークします。 ピークするメッセージ数を指定することもできます。

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする
キューからのメッセージの削除は、2 段階の手順で実行できます。

1. **list\_messages()** を呼び出すと、既定では、キュー内の次のメッセージを取得します。 取得するメッセージ数を指定することもできます。 **list\_messages()** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 パラメーターとして、表示タイムアウトを秒単位で指定します。
2. また、キューからのメッセージの削除を完了するには、**delete_message()** を呼び出す必要があります。

2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **delete\_message()** を呼び出します。

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する
キュー内のメッセージの内容をインプレースで変更できます。 次のコードでは、**update_message()** メソッドを使用してメッセージを更新します。 このメソッドは、キュー メッセージの PopReceipt と、メッセージがキューに配置される日時を表す UTC 日付/時刻値を含むタプルを返します。

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>方法:メッセージのデキュー用の追加オプション
キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

1. メッセージのバッチを取得できます。
2. コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

次のコード例では、**list\_messages()** メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。 その後、各メッセージを出力して削除します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する
キュー内のメッセージの概数を取得できます。 **get\_queue\_metadata()** メソッドを使用して、おおよそのメッセージ数とキューのメタデータを返すように Queue サービスに要求します。

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する
キューと、キューに含まれているすべてのメッセージを削除するには、キュー オブジェクトに対して **delete\_queue()** メソッドを呼び出します。

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>次の手順
これで、Queue ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

* [Azure Storage チーム ブログ](https://blogs.msdn.com/b/windowsazurestorage/)
* GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ

この記事で説明されている Azure Queue サービスと、「[Service Bus キューの使用方法](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)」で説明されている Azure Service Bus キューの比較については、「[Azure キューと Service Bus キューの比較](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)」をご覧ください。
