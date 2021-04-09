---
title: Ruby から Queue Storage を使用する方法 - Azure Storage
description: Azure Queue Storage を使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。 コード サンプルは Ruby で記述されています。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587664"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby から Queue Storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要

このガイドでは、Microsoft Azure Queue ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Ruby Azure API を使用して記述されています。 キュー メッセージの **挿入**、**ピーク**、**取得**、**削除** と、**キューの作成と削除** の各シナリオについて説明します。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成

Ruby アプリケーションを作成します。 手順については、「[App Service on Linux での Ruby アプリケーションの作成](../../app-service/quickstart-ruby.md)」を参照してください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成

Azure Storage を使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する

1. PowerShell (Windows)、ターミナル (Mac)、Bash (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「`gem install Azure`」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする

任意のテキスト エディターを使用して、ストレージを使用する Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage 接続文字列の設定

Azure モジュールは、Azure Storage アカウントに接続するために必要な情報として、環境変数 `AZURE_STORAGE_ACCOUNT` と `AZURE_STORAGE_ACCESS_KEY` を読み取ります。 これらの環境変数が設定されていない場合は、`Azure::QueueService` を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の **[設定]** ブレードで、 **[アクセス キー]** をクリックします。
4. 表示される **[アクセス キー]** ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

## <a name="how-to-create-a-queue"></a>方法:キューを作成する

次のコードは、 `Azure::QueueService` オブジェクトを作成し、これによってキューを操作できるようにします。

```ruby
azure_queue_service = Azure::QueueService.new
```

`create_queue()` メソッドを使用して、指定した名前のキューを作成します。

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>方法:メッセージをキューに挿入する

キューにメッセージを挿入するには、`create_message()` メソッドを使用し、新しいメッセージを作成してキューに追加します。

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする

`peek_messages()` メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 `peek_messages()` の既定では、1 つのメッセージを対象としてピークします。 ピークするメッセージ数を指定することもできます。

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする

キューからのメッセージの削除は、2 段階の手順で実行できます。

1. `list_messages()` を呼び出すと、既定では、キュー内に次のメッセージが取得されます。 取得するメッセージ数を指定することもできます。 `list_messages()` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 パラメーターとして、表示タイムアウトを秒単位で指定します。
2. また、キューからのメッセージの削除を完了するには、`delete_message()` を呼び出す必要があります。

2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 ご自分のコードで、メッセージが処理された直後に `delete_message()` を呼び出します。

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。 次のコードでは、`update_message()` メソッドを使用してメッセージを更新します。 このメソッドは、キュー メッセージの PopReceipt と、メッセージがキューに配置される日時を表す UTC `DateTime` 値を含むタプルを返します。

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>方法: メッセージのデキュー用の追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。

1. メッセージのバッチを取得できます。
2. コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

次のコード例では、`list_messages()` メソッドを使用して、1 回の呼び出しで 15 個のメッセージを取得します。 その後、各メッセージを出力して削除します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する

キュー内のメッセージの概数を取得できます。 `get_queue_metadata()` メソッドは、おおよそのメッセージ数とその他のキューのメタデータを返します。

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの `delete_queue()` メソッドを呼び出します。

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>次のステップ

これで、Queue Storage の基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

- [Azure Storage チームのブログ](/archive/blogs/windowsazurestorage/)
- GitHub の [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ

この記事で説明されている Azure Queue Storage と、「[Service Bus キューの使用方法](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)」で説明されている Azure Service Bus キューの比較については、「[Storage キューと Service Bus キューの比較](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)」をご覧ください
