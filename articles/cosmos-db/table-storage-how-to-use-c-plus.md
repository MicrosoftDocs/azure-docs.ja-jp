---
title: C++ で Azure Table Storage と Azure Cosmos DB Table API を使用する方法
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 40b84a56f93ad670a26eb876a18820e0d4037f63
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033979"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>C++ で Azure Table Storage と Azure Cosmos DB Table API を使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>概要
このガイドでは、Azure Table Storage サービスまたは Azure Cosmos DB Table API を使用して、一般的なシナリオを実行する方法について説明します。 サンプルは C++ で記述され、 [C++ 用 Azure ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)を利用しています。 紹介するシナリオは、**テーブルの作成と削除**、**テーブル エンティティの操作**などです。

> [!NOTE]
> このガイドは、C++ 用 Azure ストレージ クライアント ライブラリ 1.0.0 以上のバージョンを対象としています。 推奨されるバージョンはストレージ クライアント ライブラリ 2.2.0 です。これは、[NuGet](https://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp/) 経由で入手できます。
> 

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++ アプリケーションの作成
このガイドでは、C++ アプリケーション内で実行できるストレージ機能を使用します。 そのためには、C++ 用 Azure ストレージ クライアント ライブラリをインストールし、Azure サブスクリプションに Azure ストレージ アカウントを作成する必要があります。  

C++ 用 Azure ストレージ クライアント ライブラリをインストールする場合、次の方法を使用できます。

* **Linux:**[C++ 用 Azure ストレージ クライアント ライブラリの README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) ページに記載されている手順に従います。  
* **Windows:** Visual Studio で、**[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックします。 [NuGet パッケージ マネージャー コンソール](/nuget/tools/package-manager-console) に次のコマンドを入力し、Enter キーを押します。  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>Table のクライアント ライブラリへのアクセスの構成
Azure Storage API を使用してテーブルにアクセスする C++ ファイルの先頭には、次の include ステートメントを追加します。  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Azure Storage クライアントまたは Cosmos DB クライアントでは、接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントや資格情報を保存します。 クライアント アプリケーションを実行する場合は、ストレージ接続文字列または Azure Cosmos DB 接続文字列を、適切な形式で入力する必要があります。

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定
 *AccountName* と *AccountKey* の値には、[Azure Portal](https://portal.azure.com) で一覧表示されるストレージ アカウントの名前とストレージ アカウントに対するアクセス キーを使用します。 ストレージ アカウントとアクセス キーについて詳しくは、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」を参照してください。 この例では、Azure Storage 接続文字列を保持する静的フィールドを宣言する方法が示されています。  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB 接続文字列の設定
*Account Name*、*Primary Key*、*Endpoint* の値には、[Azure Portal](https://portal.azure.com) で一覧表示される Azure Cosmos DB アカウントの名前、主キー、エンドポイントを使用します。 この例では、Azure Cosmos DB 接続文字列を保持する静的フィールドを宣言する方法が示されています。

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


ローカルの Windows ベースのコンピューターでアプリケーションをテストするには、[Azure SDK](https://azure.microsoft.com/downloads/) と共にインストールされた、Azure [ストレージ エミュレーター](../storage/common/storage-use-emulator.md)を使用できます。 ストレージ エミュレーターは、ローカルの開発マシンで、Azure BLOB、Queue、および Table service をシミュレートするユーティリティです。 次の例では、ローカルのストレージ エミュレーターに接続文字列を保持する静的フィールドを宣言する方法を示しています。  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure のストレージ エミュレーターを起動するには、 **[スタート]** ボタンをクリックするか、Windows キーを押します。 「**Azure ストレージ エミュレーター**」と入力し、アプリケーションの一覧から **[Microsoft Azure ストレージ エミュレーター]** を選択します。  

次のサンプルでは、これら 2 つのメソッドのいずれかを使用してストレージ接続文字列を取得するとします。  

## <a name="retrieve-your-connection-string"></a>接続文字列の取得
**cloud_storage_account** クラスを使用してストレージ アカウント情報を表すことができます。 ストレージ接続文字列からストレージ アカウント情報を取得するには、 **parse** メソッド使用します。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

次に、**cloud_table_client** クラスへの参照を取得します。これにより、Table ストレージ サービス内に格納されているテーブルとエンティティの参照オブジェクトを取得できます。 次のコードは、前に取得したストレージ アカウント オブジェクトを使用して、**cloud_table_client** オブジェクトを作成します。  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>テーブルを作成する
**cloud_table_client** オブジェクトを使用すると、テーブルとエンティティの参照オブジェクトを取得できます。 次のコードは、**cloud_table_client** オブジェクトを作成し、これを使用して新しいテーブルを作成します。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティをテーブルに追加するには、新しい **table_entity** オブジェクトを作成し、**table_operation::insert_entity** に渡します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。一方、多様なパーティション キーを使用すると、並列操作の拡張性が向上します。 詳しくは、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage/common/storage-performance-checklist.md)」をご覧ください。

次のコードでは、ユーザー データの格納用に **table_entity** の新しいインスタンスを作成しています。 次に、**table_operation::insert_entity** を呼び出し、テーブルへのエンティティの挿入用に **table_operation** オブジェクトを作成して、それに新しいテーブル エンティティを関連付けています。 最後に、**cloud_table** オブジェクトに対して execute メソッドを呼び出します。 新しい **table_operation** により、新しいユーザー エンティティを "people" テーブルに挿入する要求が Table service に送信されます。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作でエンティティのバッチを Table service に挿入できます。 次のコードでは、**table_batch_operation** オブジェクトを作成し、3 つの挿入操作を追加しています。 追加する各挿入操作では、新しいエンティティ オブジェクトを作成してその値を設定してから、**table_batch_operation** オブジェクトの insert メソッドを呼び出して、エンティティを新しい挿入操作に関連付けています。 その後、**cloud_table.execute** を呼び出して操作を実行します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

バッチ操作に関しては、次の事項にも留意してください。  

* 最大 100 個の挿入、削除、マージ、置換、挿入/マージ、挿入/置換の操作を任意の組み合わせで 1 つのバッチで実行できます。  
* 取得をバッチ操作として実行することもできますが、バッチ内の唯一の操作であることが必要です。  
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。  
* バッチ操作のデータ ペイロードは 4 MB に制限されています。  

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、**table_query** オブジェクトを使用します。 次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。  

> [!NOTE]
> 現在、これらの方法は Azure Cosmos DB の C++ 用にはサポートされていません。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

この例のクエリでは、フィルター条件に一致するすべてのエンティティが表示されます。 テーブルの規模が大きく、そのテーブル エンティティを頻繁にダウンロードする必要がある場合は、Azure Storage BLOB にデータを保存することをお勧めします。

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する
パーティション内の一部のエンティティのみを照会する場合は、パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。 次のコード例は、2 つのフィルターを使用して、行キー (名) がアルファベットの 'E' より前の文字で始まる、'Smith' というパーティション内のすべてのエンティティを取得し、クエリ結果を出力します。  

> [!NOTE]
> 現在、これらの方法は Azure Cosmos DB の C++ 用にはサポートされていません。

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、**table_operation::retrive_entity** を使用して、"Jeff Smith" というユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみであり、**table_result** の戻り値です。 クエリでパーティション キーと行キーの両方を指定することが、Table service から単一のエンティティを取得するための最速の方法です。  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

## <a name="replace-an-entity"></a>エンティティを置換する
エンティティを置換するには、そのエンティティを Table service から取得し、エンティティ オブジェクトを変更して、変更を Table service に戻して保存します。 次のコードは、既存のユーザーの電話番号と電子メール アドレスを変更します。 **table_operation::insert_entity** を呼び出す代わりに、このコードでは、**table_operation::replace_entity** を使用します。 これにより、サーバーでエンティティが完全に置換されます。ただし、エンティティを取得した後にサーバーでエンティティが変更された場合は、操作が失敗します。 このエラーは、取得と更新の間にアプリケーションの別のコンポーネントによって行われた変更が意図せず上書きされるのを防ぐためのものです。 このエラーを正しく処理するには、もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、再び **table_operation::replace_entity** 操作を実行します。 次のセクションでは、この動作をオーバーライドする方法を説明します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

## <a name="insert-or-replace-an-entity"></a>エンティティを挿入または置換する
**table_operation::replace_entity** 操作は、サーバーからエンティティを取得した後にエンティティが変更されていると失敗します。 さらに、**table_operation::replace_entity** が成功するためには、先にエンティティをサーバーから取得する必要があります。 しかし、サーバーにエンティティが存在するかどうかわからないが、現在格納されている値は不適切であるため、すべての値を上書きする必要がある場合もあります。 これを実現するには、**table_operation::insert_or_replace_entity** 操作を使用します。 この操作は、最終更新日時に関係なく、エンティティが存在しない場合は挿入し、存在する場合は置換します。 次のコード例では、先ほどと同じように Jeff Smith のユーザー エンティティを取得していますが、今度は **table_operation::insert_or_replace_entity** を使用してサーバーに保存しています。 そのため、取得操作と更新操作の間に行われたエンティティの更新は上書きされます。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 次のコードのクエリは、**table_query::set_select_columns** メソッドを使用して、テーブル内のエンティティの電子メール アドレスだけを返します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> すべてのプロパティを取得するよりも、エンティティからいくつかのプロパティを照会する方が効率的です。
> 
> 

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。 エンティティを取得したら、削除するエンティティを指定して **table_operation::delete_entity** を呼び出します。 **cloud_table.execute** メソッドを呼び出します。 次のコードは、"Jeff" の行キーと "Smith" のパーティション キーを持つエンティティを取得して削除します。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 削除されたテーブルは、削除後の一定期間は再作成できなくなります。  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
    {
        std::cout << "Table deleted!";
    }
    else
    {
        std::cout << "Table didn't exist";
    }
```

## <a name="troubleshooting"></a>トラブルシューティング
* Visual Studio 2017 Community エディションでのビルド エラー

  インクルード ファイル storage_account.h と table.h が原因でプロジェクトにビルド エラーが発生する場合は、**/permissive-** コンパイラ スイッチを削除します。 
  - **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[プロパティ]** を選択します。
  - **[プロパティ ページ]** ダイアログ ボックスで、**[構成プロパティ]** を展開し、**[C/C++]** を展開し、**[言語]** を選択します。
  - **[準拠モード]** を **[いいえ]** に設定します。
   
## <a name="next-steps"></a>次の手順
Azure Storage および Azure Cosmos DB の Table API について詳しくは、次のリンクを参照してください。 

* [Table API の概要](table-introduction.md)
* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* [C++ での Azure Storage のリソース一覧の取得](../storage/common/storage-c-plus-plus-enumeration.md)
* [C++ 用ストレージ クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-cpp)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
