---
title: PHP から Azure Storage Table service API または Azure Cosmos DB Table API を使用する
description: PHP から Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079633"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>PHP から Azure Storage Table service API または Azure Cosmos DB Table API を使用する方法
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

この記事では、テーブルの作成、データの格納、データに対する CRUD 操作の実行を行う方法について説明します。 Azure Table service または Azure Cosmos DB Table API のいずれかを選択してください。 サンプルは PHP で記述されており、[Azure Storage Table PHP クライアント ライブラリ][download]を使います。 紹介するシナリオは、**テーブルの作成と削除**、**テーブルのエンティティの挿入、削除、および照会** などです。 Azure Table service の詳細については、「[次のステップ](#next-steps)」を参照してください。

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure Storage アカウントを作成する**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Table API アカウントを作成する**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>PHP アプリケーションの作成

Storage Table service API または Azure Cosmos DB Table API にアクセスする PHP アプリケーションを作成するための唯一の要件は、コード内から azure-storage-table SDK for PHP のクラスを参照することです。 アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使う Storage Table service または Azure Cosmos DB の機能は、PHP アプリケーション内からローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

## <a name="get-the-client-library"></a>クライアント ライブラリを入手する

1. プロジェクトのルートに composer.json という名前のファイルを作成して、次のコードを追加します。
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. [composer.phar](https://getcomposer.org/composer.phar) をルートにダウンロードします。 
3. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。
   ```
   php composer.phar install
   ```
   または、GitHub で [Azure Storage Table PHP Client Library](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) に移動し、ソース コードを複製します。

## <a name="add-required-references"></a>必要な参照を追加する

Storage Table service API または Azure Cosmos DB API を使うには、次のことが必要です。

* [require_once][require_once] ステートメントを使ってオートローダー ファイルを参照する
* 使うクラスを参照する

次の例では、オートローダー ファイルをインクルードし、**TableRestProxy** クラスを参照する方法を示します。

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

下のすべてのサンプルに `require_once` ステートメントが入っていますが、サンプルの実行に必要なクラスのみが参照されます。

## <a name="add-your-connection-string"></a>接続文字列を追加する

Azure ストレージ アカウントまたは Azure Cosmos DB Table API アカウントのいずれかに接続できます。 使用しているアカウントの種類に基づいて接続文字列を取得します。

### <a name="add-a-storage-table-service-connection"></a>Storage Table service の接続を追加する

Storage Table service クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 Storage Table service の接続文字列の形式は次のとおりです。

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>ストレージ エミュレーターの接続を追加する

エミュレーター ストレージにアクセスするには:

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB の接続を追加する

Azure Cosmos DB テーブル クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 Azure Cosmos DB の接続文字列の形式は次のとおりです。

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Azure Table service クライアントまたは Azure Cosmos DB クライアントを作成するには、**TableRestProxy** クラスを使う必要があります。 次のようにすることができます。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使って複数の外部ソースに対して接続文字列を確認する
  * 既定では 1 つの外部ソース (環境変数) のみサポートされています。
  * `ConnectionStringSource` クラスを拡張して新しいソースを追加できます。

ここで概説している例では、接続文字列が直接渡されます。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>テーブルを作成する

**TableRestProxy** オブジェクトの **createTable** メソッドを使用してテーブルを作成できます。 テーブルの作成時、Table service のタイムアウトを設定できます (Table service のタイムアウトの詳細については、「[Table service 操作のタイムアウトの設定][table-service-timeouts]」を参照してください)。

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

テーブル名の制限については、「[Table サービス データ モデルについて][table-data-model]」を参照してください。

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

エンティティをテーブルに追加するには、新しい **Entity** オブジェクトを作成し、**TableRestProxy->insertEntity** に渡します。 エンティティの作成時には `PartitionKey` と `RowKey` を指定する必要があることに注意してください。 これらにはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。 システムは `PartitionKey` を使って多くのストレージ ノードにテーブルのエンティティを自動的に配布します。 `PartitionKey` が同じエンティティは同じノードで格納されています (同じノードで格納されている複数のエンティティに対する処理は、異なるノードにまたがって格納されているエンティティに対する処理よりもパフォーマンスは高くなります)。`RowKey` は特定のパーティション内のエンティティの一意の ID です。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

テーブルのプロパティと型については、「[Table サービス データ モデルについて][table-data-model]」を参照してください。

**TableRestProxy** クラスには、ほかにもエンティティを挿入する 2 つのメソッド、**insertOrMergeEntity** と **insertOrReplaceEntity** が用意されています。 これらのメソッドを使用するには、新しい **Entity** を作成し、いずれかのメソッドにパラメーターとして渡します。 各メソッドは、渡されたエンティティが存在しない場合に、そのエンティティを挿入します。 エンティティが既に存在する場合、**insertOrMergeEntity** はプロパティ値が既に存在するなら更新し、存在しないなら新しいプロパティを追加します。一方、**insertOrReplaceEntity** は既存のエンティティを完全に置き換えます。 次の例は、**insertOrMergeEntity** を使用する方法を示しています。 `PartitionKey` が "tasksSeattle" で `RowKey` が "1" であるエンティティがまだ存在しない場合は挿入されます。 ただし、既に挿入されている場合 (前の例を参照)、`DueDate` プロパティが更新され、`Status` プロパティが追加されます。 `Description` プロパティと `Location` プロパティも更新されますが、値は実際には変更されないままになります。 これら後者の 2 つのプロパティは例に示しているように追加されますが、ターゲット エンティティに存在しているため、それらの既存の値は変更されないままになります。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する

**TableRestProxy-&gt;getEntity`RowKey` メソッドを使用して、その** と `PartitionKey` を照会することで、1 つのエンティティを取得できます。 次の例では、パーティション キー `tasksSeattle` と行キー `1` を **getEntity** メソッドに渡しています。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する

エンティティのクエリはフィルターを使用して作成します (詳細については「[テーブルとエンティティのクエリ][filters]」を参照してください)。 パーティション内のすべてのエンティティを取得するには、フィルター "PartitionKey eq *partition_name*" を使用します。 次の例では、フィルターを **queryEntities** メソッドに渡すことで、`tasksSeattle` パーティション内のすべてのエンティティを取得する方法を示しています。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>パーティション内のエンティティのサブセットを取得する

前の例で示している同じパターンを使用してパーティション内のエンティティのサブセットを取得できます。 取得するエンティティのサブセットは、使用するフィルターによって決まります (詳細については、「[テーブルとエンティティのクエリ][filters]」を参照してください)。次の例では、フィルターを使用して、`Location` で指定した場所で、`DueDate` で指定した日付より前のエンティティをすべて取得する方法を示しています。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを取得する

クエリを使用してエンティティのプロパティのサブセットを取得できます。 *プロジェクション* と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。 取得するプロパティを指定するには、プロパティの名前を **Query->addSelectField** メソッドに渡します。 このメソッドを複数回呼び出して、ほかのプロパティを追加できます。 **TableRestProxy->queryEntities** の実行後、返されるエンティティには選択したプロパティのみ格納されています。 (テーブル エンティティのサブセットが返されるようにする場合は、前のクエリで示したようにフィルターを使用します)。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>エンティティを更新する

既存のエンティティは、エンティティの **Entity->setProperty** および **Entity->addProperty** メソッドを使った後、**TableRestProxy->updateEntity** を呼び出すことで更新できます。 次の例では、エンティティを取得してから、1 つのプロパティの変更、別のプロパティの削除、新しいプロパティの追加を行っています。 プロパティの値を **null** に設定して、プロパティを削除できることに注意してください。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティを削除するには、テーブル名、およびエンティティの `PartitionKey` と `RowKey` を **TableRestProxy->deleteEntity** メソッドに渡します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

コンカレンシーのチェック用に、削除するエンティティの Etag を設定できます。そのためには、**DeleteEntityOptions-&gt;setEtag** メソッドを使い、**DeleteEntityOptions** オブジェクトを 4 番目のパラメーターとして **deleteEntity** に渡します。

## <a name="batch-table-operations"></a>バッチ テーブル処理

**TableRestProxy->batch** メソッドを使用すると、1 つの要求で複数の処理を実行できます。 ここで示しているパターンでは、処理を **BatchRequest** オブジェクトに追加し、**BatchRequest** オブジェクトを **TableRestProxy->batch** メソッドに渡しています。 処理を **BatchRequest** オブジェクトに追加するには、次のいずれかのメソッドを複数回呼び出すことができます。

* **addInsertEntity** (insertEntity 処理を追加)
* **addUpdateEntity** (updateEntity 処理を追加)
* **addMergeEntity** (mergeEntity 処理を追加)
* **addInsertOrReplaceEntity** (insertOrReplaceEntity 処理を追加)
* **addInsertOrMergeEntity** (insertOrMergeEntity 処理を追加)
* **addDeleteEntity** (deleteEntity 処理を追加)

次の例では、1 つの要求で **insertEntity** 処理と **deleteEntity** 処理を実行する方法を示しています。 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

テーブル バッチ処理の詳細については、「[エンティティ グループ トランザクションの実行][entity-group-transactions]」を参照してください。

## <a name="delete-a-table"></a>テーブルを削除する

最後に、テーブルを削除するには、テーブル名を **TableRestProxy->deleteTable** メソッドに渡します。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>次のステップ

これで、Azure Table service と Azure Cosmos DB の基本を学習できました。さらに詳しく学習するには、次のリンク先をご覧ください。

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。

* [PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
