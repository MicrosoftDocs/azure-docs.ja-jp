---
title: Cassandra 用 Azure Cosmos DB API でフィードを変更する
description: Cassandra 用 Azure Cosmos DB API で変更フィードを使用して、データに加えられた変更を取得する方法について説明します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605095"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Cassandra 用 Azure Cosmos DB API でフィードを変更する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra 用 Azure Cosmos DB API の[変更フィード](change-feed.md) サポートは、Cassandra Query Language (CQL) のクエリ述語を通じて利用できます。 これらの述語条件を使用して、フィード変更 API に対してクエリを実行できます。 アプリケーションでは、CQL で必要とされる主キー (パーティション キーとも呼ばれます) を使用して、テーブルへの変更を取得できます。 その後、結果に基づいてさらにアクションを実行できます。 テーブルの行に加えた変更は、変更時刻順かつ、パーティション キー別の並べ替え順序でキャプチャされます。

次の例では、.NET を使用して Cassandra API キー スペース テーブル内のすべての行で変更フィードを取得する方法を示します。 述語 COSMOS_CHANGEFEED_START_TIME () は CQL 内で直接使用され、指定された開始時刻 (この場合は現在の日付/時刻) か変更フィードの項目をクエリします。 C# は[こちら](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)、および Java は[こちら](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)から、完全なサンプルをダウンロードすることができます。

各反復処理では、ページング状態を使用して、最後に変更が読み取られた時点でクエリが再開されます。 キー スペースのテーブルに新しい変更のストリームが連続して表示されます。 挿入または更新された行の変更が表示されます。 Cassandra API の変更フィードを使用した削除操作の監視は、現在はサポートされていません。

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

主キーで 1 つの行に対する変更を取得するには、クエリに主キーを追加します。 次の例は、"user_id = 1" という行の変更履歴を残す方法を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>現在の制限

Cassandra API で変更フィードを使用する場合、次の制限事項が適用されます。

* 挿入と更新は現在サポートされています。 削除操作はまだサポートされていません。 回避策として、削除する行にソフト マーカーを追加できます。 たとえば、"削除済み" という行にフィールドを追加し、"true" に設定します。
* 最新の更新プログラムはコア SQL API のように永続化され、エンティティに対する中間更新プログラムは使用できません。


## <a name="error-handling"></a>エラー処理

Cassandra API で変更フィードを使用するときは、次のエラー コードとメッセージがサポートされます。

* **HTTP エラー コード 429** - 変更フィードのレートが制限されている場合、空のページが返されます。

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートを使用して Azure Cosmos DB Cassandra API リソースを管理する](./templates-samples-cassandra.md)