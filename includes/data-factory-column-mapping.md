## <a name="column-mapping-with-translator-rules"></a>翻訳規則による列マッピング
列マッピングは、ソース テーブル マップの “structure” 列を、シンク テーブルの “structure” 列に対し指定するために使用できます。 **columnMapping** プロパティは、コピー アクティビティの **typeProperties** セクションにあります。

列マッピングは、次のシナリオをサポートしています。

* ソース テーブルの "structure" のすべての列が、シンク テーブルの "structure" のすべての列にマップされる。
* ソース テーブルの "structure" の列のサブセットが、シンク テーブルの "structure" のすべての列にマップされる。

次のエラー状態では例外が発生します。

* シンク テーブルの “structure” の列数が、マッピングの指定数より多いか少ない。
* 重複したマッピング。
* SQL クエリの結果に、マッピングで指定されている列名がない。

## <a name="column-mapping-samples"></a>列マッピングの例
> [!NOTE]
> 以下の例は Azure SQL と Azure BLOB 向けですが、四角形のデータセットをサポートする任意のデータ ストアにも適用できます。 以下の例では、適切なデータ ソース内のデータを示すため、データセットとリンクされているサービスの定義を調整する必要があります。 
> 
> 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>例 1 – Azure SQL から Azure BLOB への列マッピング
この例の入力テーブルには構造体が 1 つあり、Azure SQL データベース内の SQL テーブルをポイントしています。

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

この例の出力テーブルには構造体が 1 つあり、Azure BLOB ストレージ内の BLOB をポイントしています。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
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
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

アクティビティの JSON は以下の通りです。 ソースの列は、**Translator** プロパティを使用して、シンク (**columnMappings**) の列にマップされます。

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**列マッピングのフロー:**

![列マッピングのフロー](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>例 2 – SQL クエリを使用した Azure SQL から Azure BLOB への列マッピング
この例では、“structure” セクションでテーブル名と列名を単純に指定する代わりに、SQL クエリを Azure SQL からデータを抽出するために使用します。 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
この場合、クエリの結果は、ソースの “structure” で指定された列に最初にマップされます。 次に、“structure” ソースからの列が、columnMappings で指定した規則によって、シンク"structure"内の列にマップされます。  クエリが 5 つの列を返した場合、さらに 2 つの列と、ソースの “structure” で指定される列が返されます。

**列マッピングのフロー**

![列マッピングのフロー - 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)



<!--HONumber=Nov16_HO3-->


