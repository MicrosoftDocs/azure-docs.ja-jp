### <a name="type-conversion-sample"></a>タイプ変換の例
次の例では、タイプ変換により、データを BLOB から Azure SQL へコピーします。

BLOB のデータセットは CSV 形式で、3 つの列を含んでいるとします。 列のうち 1 つは、カスタム日付/時刻形式の datetime 型の列です。名前は週の曜日を表すフランス語の省略形を使用しています。

次のように、列の種類と、コピー元のBLOB データセットを定義します。

```json
{
    "name": "AzureBlobTypeSystemInput",
    "properties":
    {
         "structure": 
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
上部で SQL タイプが .NET 型マッピング テーブルに指定されるので、次のスキーマで Azure SQL テーブルを定義します。

| 列名 | SQL 型 |
| --- | --- |
| userid |bigint |
| name |テキスト |
| lastlogindate |datetime |

続いて、次のように Azure SQL のデータセットを定義します。 

> [!NOTE]
> 基になるデータ ストアで既に指定されているため、**structure** セクションのタイプ情報を指定する必要はありません。

```json
{
    "name": "AzureSQLOutput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

ここでは、データを BLOB から Azure SQL に移動するときに、データ ファクトリはカスタム日付/時刻を持つ Datetime フィールドを含むタイプ変換を、fr-fr カルチャを使用して自動的に実行します。



<!--HONumber=Dec16_HO3-->


