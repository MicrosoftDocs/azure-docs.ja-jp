---
title: Phoenix Query Server REST SDK - Azure HDInsight
description: Azure HDInsight 上の Phoenix Query Server に対応した REST SDK をインストールし、使用します。
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: b7c94fd2372f1d995c50439b2bd5eaefb237e175
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651403"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/) は、[Apache HBase](apache-hbase-overview.md) の上に構築されたオープンソースの超並列リレーショナル データベース レイヤーです。 Phoenix では、[SQLLine](apache-hbase-phoenix-squirrel-linux.md) などの SSH ツールを利用して HBase で SQL に似たクエリを使うことができます。 Phoenix にはまた、Phoenix Query Server (PQS) と呼ばれる HTTP サーバーも用意されています。これは、クライアント通信のために JSON と Protocol Buffers の 2 つのトランスポート メカニズムをサポートするシン クライアントです。 Protocol Buffers が既定のメカニズムであり、JSON より効率的な通信を提供します。

この記事では、PQS REST SDK を使って、テーブルを作成する方法、行を個別にまたは一括でアップサートする方法、および SQL ステートメントでデータを選ぶ方法について説明します。 例では、[Apache Phoenix Query Server 用の Microsoft .NET ドライバー](https://www.nuget.org/packages/Microsoft.Phoenix.Client)を使います。 この SDK は [Apache Calcite の Avatica](https://calcite.apache.org/avatica/) API を基に構築されており、シリアル化形式に Protocol Buffers のみを使います。

詳しくは、[Apache Calcite Avatica の Protocol Buffers リファレンス](https://calcite.apache.org/avatica/docs/protobuf_reference.html)をご覧ください。

## <a name="install-the-sdk"></a>SDK のインストール

Apache Phoenix Query Server 用の Microsoft .NET ドライバーは NuGet パッケージとして提供され、Visual Studio の **NuGet パッケージ マネージャー コンソール**から、次のコマンドを使ってインストールできます。

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>新しい PhoenixClient オブジェクトのインスタンス化

ライブラリの使用を開始するには、クラスターへの `Uri` およびクラスターの Apache Hadoop ユーザー名とパスワードを含む `ClusterCredentials` を渡して、新しい `PhoenixClient` オブジェクトをインスタンス化します。

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

CLUSTERNAME を HDInsight HBase クラスターの名前に、USERNAME と PASSWORD をクラスター作成時に指定した Hadoop 資格情報に、それぞれ置き換えます。 既定の Hadoop ユーザー名は **admin** です。

## <a name="generate-unique-connection-identifier"></a>一意接続識別子の生成

要求を PQS に送信するには、要求と接続を関連付ける一意の接続識別子を含める必要があります。

```csharp
string connId = Guid.NewGuid().ToString();
```

各例では、最初に `OpenConnectionRequestAsync` メソッドを呼び出して、一意の接続識別子を渡します。 次に、`ConnectionProperties` と `RequestOptions` を定義し、これらのオブジェクトと生成された接続識別子を `ConnectionSyncRequestAsync` メソッドに渡します。 PQS の `ConnectionSyncRequest` オブジェクトは、クライアントとサーバーの両方で確実に同じデータベース プロパティが使われるようにするのに役立ちます。

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest とその ConnectionProperties

`ConnectionSyncRequestAsync` を呼び出すには、`ConnectionProperties` オブジェクトで渡します。

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

重要なプロパティを次に示します。

| プロパティ | 説明 |
| -- | -- |
| AutoCommit | Phoenix トランザクションに対して `autoCommit` が有効かどうかを示すブール値です。 |
| ReadOnly | 接続が読み取り専用かどうかを示すブール値です。 |
| TransactionIsolation | JDBC の仕様に従ってトランザクション分離のレベルを示す整数値です。次の表をご覧ください。|
| カタログ | 接続プロパティをフェッチするときに使うカタログの名前です。 |
| スキーマ | 接続プロパティをフェッチするときに使うスキーマの名前です。 |
| IsDirty | プロパティが変更されたかどうかを示すブール値です。 |

`TransactionIsolation` の値を次に示します。

| 分離値 | 説明 |
| -- | -- |
| 0 | トランザクションはサポートされていません。 |
| 1 | ダーティ リード、反復不能読み取り、およびファントム読み取りが発生する可能性があります。 |
| 2 | ダーティ リードはできませんが、反復不能読み取りおよびファントム読み取りは発生する可能性があります。 |
| 4 | ダーティ リードと反復不能読み取りはできませんが、ファントム読み取りは発生する可能性があります。 |
| 8 | ダーティ リード、反復不能読み取り、ファントム読み取りはすべてできません。 |

## <a name="create-a-new-table"></a>新しいテーブルを作成する

他の RDBMS と同じように、HBase はテーブルにデータを格納します。 Phoenix では、標準の SQL クエリを使って新しいテーブルを作成し、プライマリ キーと列の型を定義します。

この例および以降のすべての例では、「[新しい PhoenixClient オブジェクトのインスタンス化](#instantiate-new-phoenixclient-object)」での定義に従ってインスタンス化された `PhoenixClient` オブジェクトを使います。

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

上の例では、`IF NOT EXISTS` オプションを使って `Customers` という名前の新しいテーブルを作成しています。 `CreateStatementRequestAsync` を呼び出して、Avitica (PQS) サーバーで新しいステートメントを作成します。 `finally` ブロックは、返された `CreateStatementResponse` オブジェクトと `OpenConnectionResponse` オブジェクトを閉じます。

## <a name="insert-data-individually"></a>データの個別挿入

この例では、アメリカの州と地区の省略形の `List<string>` コレクションを参照する個別データの挿入を示します。

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

後の選択操作では、テーブルの `StateProvince` 列の値を使います。

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

挿入ステートメントを実行する構造は、新しいテーブルの作成と似ています。 `try` ブロックの最後でトランザクションが明示的にコミットされていることに注意してください。 この例では、挿入トランザクションを 300 回繰り返します。 次の例では、さらに効率的なバッチ挿入処理を示します。

## <a name="batch-insert-data"></a>データのバッチ挿入

次のコードは、データを個別に挿入するコードとほぼ同じです。 この例では、準備されたステートメントで `ExecuteRequestAsync` を繰り返し呼び出すのではなく、`ExecuteBatchRequestAsync` の呼び出しで `UpdateBatch` オブジェクトを使います。

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

あるテスト環境では、300 個の新規レコードの個別挿入に約 2 分かかりました。 これに対し、バッチとしての 300 レコードの挿入に要した時間はわずか 6 秒でした。

## <a name="select-data"></a>データの選択

この例では、1 つの接続を再利用して複数のクエリを実行する方法を示します。

1. すべてのレコードを選択し、既定の最大 100 レコードを取得した後、残りのレコードをフェッチします。
2. 合計行数選択ステートメントを使って、単一のスカラー結果を取得します。
3. 州または地区ごとの顧客の合計数を返す選択ステートメントを実行します。

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

`select` ステートメントの出力結果は次のようになります。

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>次の手順 

* [HDInsight の Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
* [Apache HBase REST SDK の使用](apache-hbase-rest-sdk.md)
