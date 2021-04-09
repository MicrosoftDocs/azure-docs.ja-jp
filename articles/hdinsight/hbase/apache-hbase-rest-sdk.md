---
title: HBase .NET SDK の使用 - Azure HDInsight
description: HBase .NET SDK を使用して、テーブルの作成と削除、およびデータの読み取りと書き込みを行います。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 70c1dcc133f01cf72392f444192d977440c55cf4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866626"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Apache HBase 用 .NET SDK の使用

[Apache HBase](apache-hbase-overview.md) では主に 2 つの方法でデータを操作できます。[1 つは Apache Hive クエリ、もう 1 つは HBase の RESTful API への呼び出し](apache-hbase-tutorial-get-started-linux.md)です。 `curl` コマンドまたは同様のユーティリティを使用すると、REST API を直接操作できます。

C# および .NET アプリケーションの場合、[.NET 用 Microsoft HBase REST クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.HBase.Client/)は、HBase REST API 上にクライアント ライブラリを提供します。

## <a name="install-the-sdk"></a>SDK のインストール

HBase .NET SDK は NuGet パッケージとして提供され、Visual Studio の **NuGet パッケージ マネージャー コンソール** から、次のコマンドを使用してインストールできます。

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>新しい HBaseClient オブジェクトのインスタンス化

SDK を使用するには、`Uri` で構成される `ClusterCredentials` をクラスターに渡す新しい `HBaseClient` オブジェクト、Hadoop ユーザー名、およびパスワードをインスタンス化します。

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

CLUSTERNAME を HDInsight HBase クラスターの名前に、USERNAME と PASSWORD をクラスター作成時に指定した Apache Hadoop 資格情報に、それぞれ置き換えます。 既定の Hadoop ユーザー名は **admin** です。

## <a name="create-a-new-table"></a>新しいテーブルを作成する

HBase ではテーブルにデータが格納されます。 テーブルは *Rowkey*、主キー、"*列ファミリ*" と呼ばれる 1 つ以上の列グループで構成されます。 各テーブルのデータは、Rowkey 範囲によって水平方向に分散され、"*領域*" に配置されます。 各領域には、開始および終了キーがあります。 テーブル には、1 つ以上の領域を含めることができます。 テーブルのデータが増大すると、HBase によって、大きな領域が小さな領域に分割されます。 領域が格納されるのは "*領域サーバー*" で、1 つの領域サーバーに複数の領域を格納できます。

データは *HFiles* に物理的に格納されます。 1 つの HFile には、1 つのテーブル、1 つの領域、および 1 つの列ファミリのデータが含まれています。 HFile の行は Rowkey 順に格納されます。 HFile ごとに、行を迅速に取得するための "*B+ ツリー*" インデックスがあります。

新しいテーブルを作成するには、`TableSchema` と列を指定します。 次のコードでは、"RestSDKTable" テーブルが既に存在するかどうかを確認し、存在しない場合は、そのテーブルを作成します。

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

この新しいテーブルには、2 列のファミリ (t1 と t2) が含まれます。 列ファミリは異なる HFiles に個別に保存されているため、頻繁にクエリが実行されるデータの列ファミリは別にしておくと効果的です。 次の「[データを挿入する](#insert-data)」の例では、t1 列のファミリに列が追加されます。

## <a name="delete-a-table"></a>テーブルを削除する

テーブルを削除するには:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>データの挿入

データを挿入するには、一意の行キーを行識別子として指定します。 すべてのデータが `byte[]` 配列に格納されます。 `title`、`director`、および `release_date` 列は最も頻繁にアクセスされるため、次のコードで、これらの列を定義し、t1 列ファミリに追加します。 `description` と `tagline` 列は、t2 列ファミリに追加されます。 データは、必要に応じて、列ファミリにパーティション分割できます。

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase では [Cloud BigTable](https://cloud.google.com/bigtable/) が実装されるため、データ形式は次のようなイメージになります。

:::image type="content" source="./media/apache-hbase-rest-sdk/hdinsight-table-roles.png" alt-text="Apache HBase のサンプル データ出力" border="true":::

## <a name="select-data"></a>データの選択

HBase テーブルからデータを読み取るには、テーブル名と行キーを `GetCellsAsync` メソッドに渡して、`CellSet` を返します。

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

ここでは、一意キーに対して 1 つの行しか存在しないため、コードは最初の一致行のみを返します。 戻り値は、`byte[]` 配列から `string` 形式に変更されます。 映画のリリース日を示す整数型など、他の型に値を変換することもできます。

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>行のスキャン

HBase では `scan` を使用して、1 つ以上の行を取得します。 この例では 10 行単位で複数の行を要求し、キー値が 25 ～ 35 のデータを取得します。 すべての行を取得したら、スキャナーを削除して、リソースをクリーンアップします。

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>次のステップ

* [HDInsight で Apache HBase の例を使用する](apache-hbase-tutorial-get-started-linux.md)
* [Apache HBase での Twitter センチメントのリアルタイム分析](./apache-hbase-tutorial-get-started-linux.md)によりエンド ツー エンド アプリケーションを構築する