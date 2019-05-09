---
title: Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述する方法
description: Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を定義する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: c94509fb39d1c5ebb9aec1acfe1cbacc9cd6fd4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268416"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述する方法

Azure Cosmos DB では、統合された JavaScript 言語によるトランザクション実行が可能なため、開発者は、**ストアド プロシージャ**、**トリガー**、**ユーザー定義関数 (UDF)** を記述できます。 Azure Cosmos DB で SQL API を使用するとき、ストアド プロシージャ、トリガー、および UDF を JavaScript 言語で定義できます。 JavaScript でロジックを記述し、データベース エンジン内でロジックを実行することができます。 トリガー、ストアド プロシージャ、および UDF は、[Azure portal](https://portal.azure.com/)、[Azure Cosmos DB のJavaScript 言語統合クエリ API](javascript-query-api.md)、[Cosmos DB SQL API クライアント SDK](sql-api-dotnet-samples.md) を使用して作成および実行できます。 

ストアド プロシージャ、トリガー、およびユーザー定義関数を呼び出すには、これを登録する必要があります。 詳細については、[Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する方法](how-to-use-stored-procedures-triggers-udfs.md)に関する記事を参照してください。

> [!NOTE]
> パーティション分割されたコンテナーの場合、ストアド プロシージャを実行するとき、要求オプションにパーティション キー値を指定する必要があります。 ストアド プロシージャは常に 1 つのパーティション キーに範囲設定されます。 別のパーティション キー値を持つ項目は、ストアド プロシージャから認識できません。 このことはトリガーにも該当します。

## <a id="stored-procedures"></a>ストアド プロシージャを記述する方法

ストアド プロシージャは JavaScript を使用して記述され、Azure Cosmos コンテナー内の項目を作成、更新、読み取り、クエリの実行、および削除できます。 ストアド プロシージャは、コレクションごとに登録され、そのコレクションに存在するあらゆるドキュメントまたは添付ファイルに作用します。

**例**

これは "Hello World" 応答を返す単純なストアド プロシージャです。

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

コンテキスト オブジェクトは、要求オブジェクトと応答オブジェクトへのアクセスに加えて、Azure Cosmos DB に対して実行できるすべての操作へのアクセスを提供します。 ここでは、応答オブジェクトを使用して、クライアントに送り返される応答の本文を設定します。

ストアド プロシージャを記述したら、コレクションに登録する必要があります。 詳細については、[Azure Cosmos DB でストアド プロシージャを使用する方法](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)に関する記事を参照してください。

### <a id="create-an-item"></a>ストアド プロシージャを使用して項目を作成する

ストアド プロシージャを使用して項目を作成すると、項目は Azure Cosmos DB コンテナーに挿入され、新しく作成された項目の ID が返されます。 項目の作成は非同期操作で、JavaScript コールバック関数に依存します。 コールバック関数には、操作が失敗した場合のエラー オブジェクト用と戻り値用 (この例では作成されたオブジェクト用) の 2 つのパラメーターがあります。 コールバック内では、例外を処理することも、エラーをスローすることもできます。 コールバックが提供されていない場合にエラーが発生すると、Azure Cosmos DB ランタイムはエラーをスローします。 

ストアド プロシージャには、説明を設定するパラメーターも含まれており、このパラメーターはブール値です。 パラメーターが true に設定されているときに説明が存在しないと、ストアド プロシージャは例外をスローします。 そうでない場合、ストアド プロシージャの残りの部分が引き続き実行されます。

次のサンプル ストアド プロシージャは、新しい Azure Cosmos DB 項目を入力として受け取り、Azure Cosmos DB コンテナーに挿入し、新しく作成された項目の ID を返します。 この例では、[クイック スタート .NET SQL API](create-sql-api-dotnet.md) のToDoList サンプルを使用します。

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>ストアド プロシージャの入力パラメーターとしての配列 

Azure portal でストアド プロシージャを定義するときには、入力パラメーターは常に文字列としてストアド プロシージャに送信されます。 入力として文字列の配列を渡す場合でも、配列は文字列に変換されてストアド プロシージャに送信されます。 これを解決するため、ストアド プロシージャ内に関数を定義し、文字列を配列として解析できます。 次のコードでは、文字列入力パラメーターを配列として解析する方法を示します。

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>ストアド プロシージャ内でのトランザクション

ストアド プロシージャを使用して、コンテナー内の項目にトランザクションを実装できます。 次の例では、架空のフットボール ゲーム アプリ内で、 2 つのチームのプレーヤーを 1 回の操作でトレードするトランザクションを使用します。 このストアド プロシージャは、引数として渡されたプレーヤー ID にそれぞれ対応する 2 つの Azure Cosmos DB 項目の読み取りを試行します。 両方のプレーヤーが見つかると、ストアド プロシージャはプレーヤーのチームを交換して項目を更新します。 処理の途中でエラーが発生した場合は、ストアド プロシージャは JavaScript 例外をスローし、トランザクションが暗黙的に中止されます。

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>ストアド プロシージャ内で制限された実行

次のストアド プロシージャの例は、Azure Cosmos コンテナーに項目を一括インポートします。 このストアド プロシージャでは、`createDocument` からのブール型の戻り値を調べて制限された実行を処理し、ストアド プロシージャの各呼び出しで挿入された項目の数を使用してバッチの進行状況を追跡および再開しています。

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>トリガーを書き込む方法

Azure Cosmos DB は、プリトリガーとポストトリガーをサポートします。 プリトリガーはデータベース項目の変更前に実行され、ポストトリガーはデータベース項目の変更後に実行されます。

### <a id="pre-triggers"></a>プリトリガー

次の例に、プリトリガーを使用して、作成する Azure Cosmos DB 項目のプロパティを検証する方法を示します。 この例では、[クイック スタート .NET SQL API](create-sql-api-dotnet.md) のToDoList サンプルを使用して、新しく追加された項目にタイムスタンプ プロパティが含まれていない場合にタイムスタンプ プロパティを追加します。

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

プリトリガーは入力パラメーターを持つことができません。 トリガー内の要求オブジェクトを使用して、操作に関連付けられた要求メッセージを操作します。 前の例では、Azure Cosmos DB 項目を作成するときにプリトリガーが実行され、要求メッセージの本文に、作成する項目が JSON 形式で含まれています。

トリガーが登録されたら、ユーザーは実行できる操作を指定できます。 このトリガーは `TriggerOperation` 値 `TriggerOperation.Create` によって作成される必要があります。つまり、次のコードで示す置換操作でのこのトリガーの使用は許可されません。

プリトリガーを登録して呼び出す方法の例については、[プリトリガー](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)と[ポストトリガー](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)に関する記事を参照してください。 

### <a id="post-triggers"></a>ポストトリガー

次にポストトリガーの例を示します。 このトリガーは、メタデータ項目を照会し、新しく作成された項目に関する詳細情報に基づいてこれを更新します。


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

ここで重要なのは、Azure Cosmos DB でのトリガーのトランザクション実行です。 ポストトリガーは、基になる項目自体と同じトランザクションの一部として実行されます。 ポストトリガーの実行中に例外が発生すると、トランザクション全体が失敗します。 コミットされたものすべてがロールバックされ、例外が返されます。

プリトリガーを登録して呼び出す方法の例については、[プリトリガー](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)と[ポストトリガー](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)に関する記事を参照してください。 

## <a id="udfs"></a>ユーザー定義関数を記述する方法

次の例では、さまざまな所得階層についての所得税を計算する UDF を作成します。 このユーザー定義関数は、クエリ内で使用できます。 この例では、次のようなプロパティを持つ「Incomes」(所得) という名前のコンテナーがあると仮定します。

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

さまざまな所得階層についての所得税を計算するための関数定義を以下に示します。

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

ユーザー定義関数を登録して使用する方法の例については、 [Azure Cosmos DB でユーザー定義関数を使用する方法](how-to-use-stored-procedures-triggers-udfs.md#udfs)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述または作成する方法および概念について説明します。

* [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を登録および使用する方法](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する](stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB のJavaScript 言語統合クエリ API の操作](javascript-query-api.md)
