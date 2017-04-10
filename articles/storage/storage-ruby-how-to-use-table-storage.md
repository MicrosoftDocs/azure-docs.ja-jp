---
title: "Ruby から Azure Table Storage を使用する方法 | Microsoft Docs"
description: "NoSQL データ ストアである Azure Table Storage を使用して構造化データをクラウドに格納します。"
services: storage
documentationcenter: ruby
author: mmacy
manager: timlt
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: e1df2fcf4478ef7f58c5686e85abd6ae94b5a2d3


---
# <a name="how-to-use-azure-table-storage-from-ruby"></a>Ruby から Azure Table Storage を使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概要
このガイドでは、Azure Table サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Ruby API を使用して記述されています。 紹介するシナリオは、 **テーブルの作成と削除、テーブルのエンティティの挿入とクエリ実行**などです。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成
Ruby アプリケーションの作成方法については、「[Azure VM での Ruby on Rails Web アプリケーション](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)」をご覧ください。

## <a name="configure-your-application-to-access-storage"></a>アプリケーションのストレージへのアクセスの構成
Azure Storage を使用するには、Ruby azure パッケージをダウンロードして使用する必要があります。このパッケージには、Storage REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する
1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「 **gem install azure** 」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする
任意のテキスト エディターを使用して、Storage を使用する Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage の接続文字列の設定
azure モジュールは、Azure のストレージ アカウントに接続するために必要な情報として、環境変数 **AZURE\_STORAGE\_ACCOUNT** と **AZURE\_STORAGE\_ACCESS\_KEY** を読み取ります。 これらの環境変数が設定されていない場合は、 **Azure::TableService** を使用する前に、次のコードを使用してアカウント情報を指定する必要があります。

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Azure ポータルでクラシックまたは Resource Manager ストレージ アカウントからこれらの値を取得するには:

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 使用するストレージ アカウントを表示します。
3. 右側の [設定] ブレードで、 **[アクセス キー]**をクリックします。
4. 表示される [アクセス キー] ブレードに、アクセス キー 1 とアクセス キー 2 が表示されます。 このいずれかを使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

Azure ポータルでクラシック ストレージ アカウントからこれらの値を取得するには:

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にログインします。
2. 使用するストレージ アカウントを表示します。
3. ナビゲーション ウィンドウの下部にある **[アクセス キーの管理]** をクリックします。
4. ポップアップ ダイアログに、ストレージ アカウント名、プライマリ アクセス キー、およびセカンダリ アクセス キーが表示されます。 アクセス キーには、プライマリとセカンダリのどちらでも使用できます。
5. コピー アイコンをクリックしてキーをクリップボードにコピーします。

## <a name="create-a-table"></a>テーブルを作成する
**Azure::TableService** オブジェクトを使用すると、テーブルとエンティティを操作できます。 テーブルを作成するには、**create\_table()** メソッドを使用します。 次の例では、テーブルを作成し、既に存在している場合はエラーを出力します。

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティを追加するには、エンティティのプロパティを定義するハッシュ オブジェクトを最初に作成します。 すべてのエンティティについて、**PartitionKey** と **RowKey** を指定する必要があることに注意してください。 これらはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。 Azure Storage では、テーブルのエンティティを多数のストレージ ノードに自動的に配布するために **PartitionKey** を使用します。 **PartitionKey** が同じエンティティは同じノードに格納されます。 **RowKey** は、エンティティが属するパーティション内のエンティティの一意の ID です。

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>エンティティを更新する
既存のエンティティを更新するには、複数のメソッドがあります。

* **update\_entity():** 既存のエンティティを、置換することで更新します。
* **merge\_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。
* **insert\_or\_merge\_entity():** 既存のエンティティを、置換することで更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。
* **insert\_or\_replace\_entity():** 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、**update\_entity()** を使用してエンティティを更新する方法を示します。

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

**update\_entity()** と **merge\_entity()** では、更新されるエンティティが存在しない場合、更新操作は失敗します。 したがって、既に存在しているかどうかに関係なくエンティティを格納するには、代わりに **insert\_or\_replace\_entity()** または **insert\_or\_merge\_entity()** を使用する必要があります。

## <a name="work-with-groups-of-entities"></a>エンティティのグループを操作する
状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。 このためには、まず **Batch** オブジェクトを作成し、次に **TableService** の **execute\_batch()** メソッドを使用します。 次の例では、RowKey が 2 および 3 である 2 つのエンティティをバッチで送信する方法を示します。 これは、同じ PartitionKey を持つエンティティでのみ機能することに注意してください。

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>エンティティを照会する
テーブル内のエンティティを照会するには、**get\_entity()** メソッドを使用して、テーブル名、**PartitionKey** および **RowKey** を渡します。

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>エンティティのセットを照会する
テーブル内のエンティティのセットを照会するには、クエリ ハッシュ オブジェクトを作成し、**query\_entities()** メソッドを使用します。 次の例では、同じ **PartitionKey**を持つエンティティをすべて取得します。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> 結果セットが大きすぎて単一のクエリで返すことができない場合は、継続トークンが返されます。この継続トークンを使用して、後続のページを取得できます。
>
>

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 "プロジェクション" と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 select 句を使用して、クライアントに渡すプロパティの名前を指定します。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティを削除するには、**delete\_entity()** メソッドを使用します。 目的のエンティティを含んでいるテーブルの名前と、エンティティの PartitionKey および RowKey を渡す必要があります。

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>テーブルを削除する
テーブルを削除するには、**delete\_table()** メソッドを使用して、削除するテーブルの名前を渡します。

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>次のステップ
さらに複雑なストレージ タスクの詳細については、次のリンク先をご覧ください。

* [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
* [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) リポジトリ




<!--HONumber=Dec16_HO2-->


