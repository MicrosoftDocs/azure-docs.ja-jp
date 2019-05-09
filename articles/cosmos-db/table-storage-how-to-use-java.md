---
title: Java から Azure Table Storage または Azure Cosmos DB Table API を使用する方法
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 6b8b2d2d035183861f367c9425ec54d1c9babf34
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286552"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Java から Azure Table Storage または Azure Cosmos DB Table API を使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>概要
この記事では、Azure Table Storage サービスと Azure Cosmos DB を使用する一般的なシナリオの実行方法について説明します。 サンプルは Java で記述され、 [Azure Storage SDK for Java][Azure Storage SDK for Java]を利用しています。 テーブルの**作成**、**一覧表示**、および**削除**と、テーブル内のエンティティの**挿入**、**照会**、**変更**、および**削除**の各シナリオについて説明します。 テーブルの詳細については、「 [次のステップ](#next-steps) 」のセクションを参照してください。

> [!NOTE]
> SDK は、Android デバイスで Azure Storage を使用する開発者向けに用意されています。 詳細については、「[Azure Storage SDK for Android に関するページ][Azure Storage SDK for Android]｣をご覧ください。
>

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成
このガイドで使用するストレージ機能は、Java アプリケーション内でローカルで実行することも、Azure の Web ロールまたは worker ロールで動作するコード内で実行することもできます。

この記事のサンプルを使用するには、Java Development Kit (JDK) をインストールし、Azure サブスクリプションに Azure ストレージ アカウントまたは Azure Cosmos DB アカウントを作成する必要があります。 その次に、開発システムが、GitHub の [Azure Storage SDK for Java][Azure Storage SDK for Java] リポジトリに示されている最小要件と依存関係を満たしていることを確認します。 システムがそれらの要件を満たしている場合は、指示に従って、そのリポジトリからシステムに Azure Storage Libraries for Java をダウンロードしてインストールできます。 それらのタスクが完了すると、この記事の例を使用した Java アプリケーションを作成できます。

## <a name="configure-your-application-to-access-table-storage"></a>テーブル ストレージにアクセスするようにアプリケーションを構成する
Azure Storage API または Azure Cosmos DB Table API を使用してテーブルにアクセスする Java ファイルの先頭には、次の import ステートメントを追加します。

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の追加
Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。 クライアント アプリケーションの実行時、ストレージ接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、[Azure ポータル](https://portal.azure.com)に表示されるストレージ アカウントの名前とプライマリ アクセス キーを使用します。 

この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Azure Cosmos DB Table API 接続文字列の追加
Azure Cosmos DB アカウントは、接続文字列を使用して、テーブルのエンドポイントと資格情報を格納します。 クライアント アプリケーションの実行時、Azure Cosmos DB 接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、[Azure Portal](https://portal.azure.com) に表示される Azure Cosmos DB アカウントの名前とプライマリ アクセス キーを使用します。 

この例では、Azure Cosmos DB 接続文字列を保持する静的フィールドを宣言する方法が示されています。

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Azure 上のロール内で実行されるアプリケーションでは、この文字列をサービス構成ファイルである *ServiceConfiguration.cscfg* に格納でき、**RoleEnvironment.getConfigurationSettings** メソッドの呼び出しを使用してアクセスできます。 次の例では、サービス構成ファイル内の **StorageConnectionString** という名前の *Setting* 要素から接続文字列を取得しています。

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

接続文字列は、プロジェクトの config.properties ファイルに格納することもできます。

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

次のサンプルでは、これらのメソッドのいずれかを使用してストレージ接続文字列を取得していることを前提としています。

## <a name="create-a-table"></a>テーブルを作成する
**CloudTableClient** オブジェクトを使うと、テーブルとエンティティの参照オブジェクトを取得できます。 次のコードは、**CloudTableClient** オブジェクトを作成し、これを使って新しい **CloudTable** オブジェクトを作成します。この CloudTable オブジェクトは、"people" という名前のテーブルを表します 

> [!NOTE]
> **CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳しくは、[Azure ストレージ クライアント SDK リファレンス]の **CloudStorageAccount** をご覧ください。
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>テーブルを一覧表示する
テーブルの一覧を取得するには、 **CloudTableClient.listTables()** メソッドを呼び出して、テーブル名の反復可能な一覧を取得します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティは、 **TableEntity**を実装するカスタム クラスを使用して Java オブジェクトにマップされます。 コードがシンプルになるように、**TableServiceEntity** クラスでは **TableEntity** を実装し、リフレクションを使うことで、プロパティを、それらのプロパティの名前が付いた getter および setter メソッドにマップしています。 エンティティをテーブルに追加するには、最初に、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

エンティティに関連するテーブル操作には **TableOperation** オブジェクトが必要です。 このオブジェクトを使って、エンティティに対して実行する操作を定義します。定義した操作は、**CloudTable** オブジェクトを使って実行できます。 次のコードでは、顧客データの格納用に **CustomerEntity** クラスの新しいインスタンスを作成しています。 次に、このコードでは **TableOperation.insertOrReplace** を呼び出し、テーブルへのエンティティの挿入用に **TableOperation** オブジェクトを作成して、そのオブジェクトを新しい **CustomerEntity** に関連付けています。 最後に、このコードでは **CloudTable** オブジェクトの **execute** メソッドを呼び出し、"people" テーブルと新しい **TableOperation** を指定しています。それにより、新しいユーザー エンティティを "people" テーブルに挿入する (そのエンティティが既に存在する場合は置き換える) 要求がストレージ サービスに送信されるようになっています。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作でエンティティのバッチをテーブル サービスに挿入できます。 次のコードでは、**TableBatchOperation** オブジェクトを作成し、3 つの挿入操作を追加しています。 追加する各挿入操作では、新しいエンティティ オブジェクトを作成してその値を設定してから、**TableBatchOperation** オブジェクトの **insert** メソッドを呼び出して、エンティティを新しい挿入操作に関連付けています。 次に、このコードでは **CloudTable** オブジェクトの **execute** を呼び出して、"people" テーブルと **TableBatchOperation** オブジェクトを指定しています。それにより、テーブル操作のバッチがストレージ サービスに 1 つの要求で送信されるようになっています。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

バッチ操作に関しては、次の事項にも留意してください。

* 最大 100 個の挿入、削除、マージ、置換、挿入/マージ、挿入/置換の操作を任意の組み合わせで 1 つのバッチで実行できます。
* 取得をバッチ操作として実行することもできますが、バッチ内の唯一の操作であることが必要です。
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。
* バッチ操作のデータ ペイロードは 4 MB に制限されています。

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のエンティティを照会する場合は、 **TableQuery**を使用できます。 **TableQuery.from** を呼び出して、特定のテーブルに対するクエリを作成し、指定した型の結果が返るようにします。 次のコードは、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 **TableQuery.generateFilterCondition** はクエリのフィルターを作成するためのヘルパー メソッドです。 **TableQuery.from** メソッドによって返された参照の **where** を呼び出して、フィルターをクエリに適用します。 クエリが **CloudTable** オブジェクトの **execute** の呼び出しを使用して実行されると、指定した **CustomerEntity** 型の結果が **Iterator** に格納されて返されます。 その後、返された **Iterator** を for each ループ内で使用して、結果を処理できます。 このコードは、クエリ結果の各エントリのフィールドをコンソールに出力します。

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する
パーティション内の一部のエンティティのみ照会する場合は、フィルター内で比較演算子を使用して範囲を指定できます。 次のコードは、2 つのフィルターを組み合わせて、行キー (名) がアルファベットの "E" までの文字で始まる、"Smith" というパーティション内のすべてのエントリを取得します。 その後で、クエリ結果が出力されます。 このガイドのバッチ挿入に関するセクションでテーブルに追加したエンティティを使用すると、この場合は 2 つのエンティティ (Ben Smith と Denise Smith) だけが返されます。Jeff Smith は返されません。

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードでは、**TableOperation.retrieve** を呼び出し、パーティション キーと行キーのパラメーターを使って、顧客 "Jeff Smith" を指定しています。同じ操作は、**TableQuery** を作成してフィルターを使うことでも可能です。 この取得操作が実行されると、1 つのコレクションではなく、1 つのエンティティのみ返されます。 **getResultAsType** メソッドは、結果を設定先の型である **CustomerEntity** オブジェクト型にキャストします。 この型がクエリに指定した型と互換性がない場合は、例外がスローされます。 パーティション キーおよび行キーが正確に一致するエンティティがない場合は、null 値が返されます。 クエリでパーティション キーと行キーの両方を指定することが、Table service から単一のエンティティを取得するための最速の方法です。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>エンティティを変更する
エンティティを変更するには、そのエンティティをテーブル サービスから取得し、エンティティ オブジェクトに変更を加えて、その変更を置換またはマージ操作でテーブル サービスに戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 挿入の場合のように **TableOperation.insert** を呼び出すのではなく、このコードは **TableOperation.replace** を呼び出します。 このアプリケーションがエンティティを取得した後で別のアプリケーションが変更を加えていない限り、 **CloudTable.execute** メソッドはテーブル サービスを呼び出し、このエンティティは置き換えられます。 別のアプリケーションが変更を加えた場合は、例外がスローされるので、このエンティティを取得して変更し、もう一度保存する必要があります。 このオプティミスティック コンカレンシーの再試行パターンは、分散したストレージ システムでは一般的です。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 次のコードのクエリは、**select** メソッドを使って、テーブル内のエンティティの電子メール アドレスだけを返します。 結果は **EntityResolver** によって **String** コレクションへのプロジェクション (サーバーから返されるエンティティの型変換) が行われます。 プロジェクションの詳細については、[Azure テーブル: アップサートおよびクエリ プロジェクションの概要][Azure Tables: Introducing Upsert and Query Projection] に関するページを参照してください。 プロジェクションはローカル ストレージ エミュレーターではサポートされていません。したがって、このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>エンティティの挿入または置換を行う
エントリをテーブルに追加するときは、多くの場合、そのエントリがテーブル内に既に存在しているかどうかを把握していません。 エンティティの挿入または置換操作では、エンティティが存在しない場合にそのエンティティを挿入し、エンティティが存在する場合はその既存のエンティティを置き換えるという操作を 1 つの要求で処理することができます。 これまでの例に対して、次のコードは "Walter Harp" のエンティティを挿入または置換します。 新しいエンティティを作成すると、このコードは **TableOperation.insertOrReplace** メソッドを呼び出します。 その後、**CloudTable** オブジェクトの **execute** を呼び出し、テーブルとそのテーブルの挿入および置換操作をパラメーターとして渡します。 エンティティの一部のみ更新するには、**TableOperation.insertOrMerge** メソッドを代わりに使えます。 挿入または置換はローカル ストレージ エミュレーターではサポートされていません。したがって、このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。 挿入または置換、および挿入またはマージの詳細については、この [Azure テーブル: アップサートおよびクエリ プロジェクションの概要][Azure Tables: Introducing Upsert and Query Projection] に関するページを参照してください。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。 エンティティを取得した後、削除するエンティティを指定して **TableOperation.delete** を呼び出します。 その後、**CloudTable** オブジェクトの **execute** を呼び出します。 次のコードは、ユーザー エンティティを取得して削除します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコードは、ストレージ アカウントからテーブルを削除します。 削除した後、約 40 秒間はテーブルを再作成できません。 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>次の手順

* [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* [Azure Storage SDK for Java][Azure Storage SDK for Java]
* [Azure ストレージ クライアント SDK リファレンス][Azure ストレージ クライアント SDK リファレンス]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage チームのブログ][Azure Storage チームのブログ]

詳細については、「[Azure for Java developers (Java 開発者向けの Azure)](/java/azure)」を参照してください。

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure ストレージ クライアント SDK リファレンス]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
