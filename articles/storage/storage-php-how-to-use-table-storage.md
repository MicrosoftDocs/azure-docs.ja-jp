---
title: "PHP から Table Storage を使用する方法 | Microsoft Docs"
description: "PHP から Table サービスを使用して、テーブルを作成および削除する方法、テーブルのエンティティを挿入、削除、照会する方法について説明します。"
services: storage
documentationcenter: php
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 61658fdafb6d2ead155c2d1b5c2563771c93ed0f


---
# <a name="how-to-use-table-storage-from-php"></a>PHP から Table ストレージを使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overview
このガイドでは、Azure Table サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは PHP で記述され、[Azure SDK for PHP][download] を利用しています。 紹介するシナリオは、 **テーブルの作成と削除、テーブルのエンティティの挿入、削除、および照会**などです。 Azure Table サービスの詳細については、「 [次のステップ](#next-steps) 」を参照してください。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP アプリケーションの作成
Azure Table サービスにアクセスする PHP アプリケーションを作成するための要件は、コード内から Azure SDK for PHP のクラスを参照することのみです。 アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。

このガイドで使用する Table サービス機能は、PHP アプリケーション内からローカルで呼び出すことも、Azure の Web ロール、worker ロール、または Web サイト上で実行されるコード内で呼び出すこともできます。

## <a name="get-the-azure-client-libraries"></a>Azure クライアント ライブラリの入手
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Table サービスにアクセスするようにアプリケーションを構成する
Azure Table サービス API を使用するには、次の要件があります。

1. [require_once][require_once] ステートメントを使ってオートローダー ファイルを参照する
2. 使用する可能性のあるクラスを参照する

次の例では、オートローダー ファイルをインクルードし、 **ServicesBuilder** クラスを参照する方法を示しています。

> [!NOTE]
> この記事の例では、Composer を使用して Azure 向け PHP クライアント ライブラリがインストールされていることを前提としています。 ライブラリを手動でインストールした場合は、 <code>WindowsAzure.php</code> オートローダー ファイルを参照する必要があります。
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

下のすべてのサンプルに `require_once` ステートメントが入っていますが、サンプルの実行に必要なクラスのみが参照されます。

## <a name="set-up-an-azure-storage-connection"></a>Azure のストレージ接続文字列の設定
Azure Table サービス クライアントをインスタンス化するには、まず有効な接続文字列が必要です。 Table サービスの接続文字列の形式は次のとおりです。

ライブ サービスにアクセスする場合:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

エミュレーター ストレージにアクセスする場合:

```php
UseDevelopmentStorage=true
```

いずれの Azure サービス クライアントを作成するにも、 **ServicesBuilder** クラスを使用する必要があります。 そのための方法は次のとおりです。

* 接続文字列を直接渡す
* **CloudConfigurationManager (CCM)** を使用して複数の外部ソースに対して接続文字列を確認する
  * 既定では&1; つの外部ソース (環境変数) のみサポートされています。
  * **ConnectionStringSource** クラスを継承して新しいソースを追加できます。

ここで概説している例では、接続文字列を直接渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>テーブルを作成する
**TableRestProxy** オブジェクトの **createTable** メソッドを使用してテーブルを作成できます。 テーブルの作成時、Table サービスのタイムアウトを設定できます  (Table サービスのタイムアウトの詳細については、「[Table サービス操作のタイムアウトの設定][table-service-timeouts]」を参照)。

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

テーブル名の制限については、「[Table サービス データ モデルについて][table-data-model]」をご覧ください。

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティをテーブルに追加するには、新しい **Entity** オブジェクトを作成し、**TableRestProxy->insertEntity** に渡します。 エンティティの作成時には `PartitionKey` と `RowKey` を指定する必要があることに注意してください。 これらにはエンティティの一意の識別子であり、他のエンティティのプロパティよりはるかに高速に照会できる値です。 システムでは `PartitionKey` が使用されて多くのストレージ ノードにテーブルのエンティティが自動的に配布されます。 `PartitionKey` が同じエンティティは同じノードで格納されています (同じノードで格納されている複数のエンティティに対する処理は、異なるノードにまたがって格納されているエンティティに対する処理よりもパフォーマンスは高くなります)。`RowKey` は特定のパーティション内のエンティティの一意の ID です。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

テーブルのプロパティと型については、「[Table サービス データ モデルについて][table-data-model]」をご覧ください。

**TableRestProxy** クラスには、ほかにもエンティティを挿入する&2; つのメソッド、**insertOrMergeEntity** と **insertOrReplaceEntity** が用意されています。 これらのメソッドを使用するには、新しい **Entity** を作成し、いずれかのメソッドにパラメーターとして渡します。 各メソッドは、渡されたエンティティが存在しない場合に、そのエンティティを挿入します。 エンティティが既に存在する場合、**insertOrMergeEntity** はプロパティ値が既に存在するなら更新し、存在しないなら新しいプロパティを追加します。一方、**insertOrReplaceEntity** は既存のエンティティを完全に置き換えます。 次の例は、**insertOrMergeEntity** を使用する方法を示しています。 `PartitionKey` が "tasksSeattle" で `RowKey` が "1" であるエンティティがまだ存在しない場合は挿入されます。 ただし、既に挿入されている場合 (前の例を参照)、`DueDate` プロパティが更新され、`Status` プロパティが追加されます。 `Description` プロパティと `Location` プロパティも更新されますが、値は実際には変更されないままになります。 これら後者の&2; つのプロパティは例に示しているように追加されますが、ターゲット エンティティに存在しているため、それらの既存の値は変更されないままになります。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
**TableRestProxy->getEntity`RowKey` メソッドを使用して、その ** と `PartitionKey` を照会することで、1 つのエンティティを取得できます。 次の例では、パーティション キー `tasksSeattle` と行キー `1` を **getEntity** メソッドに渡しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
エンティティのクエリはフィルターを使用して作成します (詳細については「[テーブルとエンティティのクエリ][filters]」を参照)。 パーティション内のすべてのエンティティを取得するには、フィルター "PartitionKey eq *partition_name*" を使用します。 次の例では、フィルターを **queryEntities** メソッドに渡すことで、`tasksSeattle` パーティション内のすべてのエンティティを取得する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
前の例で示している同じパターンを使用してパーティション内のエンティティのサブセットを取得できます。 取得するエンティティのサブセットは、使用するフィルターによって決まります (詳細については、「[テーブルとエンティティのクエリ][filters]」を参照)。次の例では、フィルターを使用して、`Location` で指定した場所で、`DueDate` で指定した日付より前のエンティティをすべて取得する方法を示しています。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
クエリを使用してエンティティのプロパティのサブセットを取得できます。 *プロジェクション*と呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。 取得するプロパティを指定するには、プロパティの名前を **Query->addSelectField** メソッドに渡します。 このメソッドを複数回呼び出して、ほかのプロパティを追加できます。 **TableRestProxy->queryEntities** の実行後、返されるエンティティには選択したプロパティのみ格納されています。 (テーブル エンティティのサブセットが返されるようにする場合は、前のクエリで示したようにフィルターを使用します)。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
既存のエンティティは、エンティティの **Entity->setProperty** および **Entity->addProperty** メソッドを使用した後、**TableRestProxy->updateEntity** を呼び出すことで更新できます。 次の例では、エンティティを取得してから、1 つのプロパティの変更、別のプロパティの削除、新しいプロパティの追加を行っています。 プロパティの値を **null**に設定して、プロパティを削除できることに注意してください。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティを削除するには、テーブル名、およびエンティティの `PartitionKey` と `RowKey` を **TableRestProxy->deleteEntity** メソッドに渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

同時実行のチェック用に、削除するエンティティの Etag を設定できることに注意してください。そのためには、**DeleteEntityOptions->setEtag** メソッドを使用して、**DeleteEntityOptions** オブジェクトを&4; 番目のパラメーターとして **deleteEntity** に渡します。

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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

テーブル バッチ処理の詳細については、「[エンティティ グループ トランザクションの実行][entity-group-transactions]」をご覧ください。

## <a name="delete-a-table"></a>テーブルを削除する
最後に、テーブルを削除するには、テーブル名を **TableRestProxy->deleteTable** メソッドに渡します。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>次のステップ
これで、Azure Table サービスの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

* [Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/)

詳細については、 [PHP デベロッパー センター](/develop/php/)も参照してください。

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx



<!--HONumber=Dec16_HO2-->


