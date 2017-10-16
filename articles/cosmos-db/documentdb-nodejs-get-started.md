---
title: "Azure Cosmos DB 用 DocumentDB API の Node.js チュートリアル | Microsoft Docs"
description: "Node.js で DocumentDB API を使って Cosmos DB を作成するチュートリアルです。"
keywords: "Node.js チュートリアル, ノード データベース"
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: article
ms.date: 08/14/2017
ms.author: anhoh
ms.openlocfilehash: 02e98aadc6a001c7275266d89a196a57bb366b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-tutorial-use-the-documentdb-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Node.js チュートリアル: Azure Cosmos DB の DocumentDB API を使用して Node.js コンソール アプリケーションを作成する
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB 用 Node.js](mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Azure Cosmos DB Node.js SDK の Node.js チュートリアルへようこそ。 このチュートリアルに従うことで、Azure Cosmos DB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

ここで説明する操作は以下のとおりです。

* Azure Cosmos DB アカウントを作成し、アカウントに接続する
* アプリケーションをセットアップする
* ノード データベースの作成
* コレクションを作成する
* JSON ドキュメントを作成する
* コレクションをクエリする
* ドキュメントを置換する
* ドキュメントを削除する
* ノード データベースの削除

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) で完全なソリューションを入手できます。 簡単な手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

Node.js チュートリアルを完了した後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。 マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Node.js チュートリアルの前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 これがない場合は、 [Azure の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) バージョン v0.10.29 以降

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>手順 2: Node.js アプリケーションをセットアップする
1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、2 つの空の JavaScript ファイルを作成します。
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. npm で documentdb モジュールをインストールします。 次のコマンドを使用します。
   * ```npm install documentdb --save```

そこで、 これでセットアップは終了です。 いくつかのコードの記述を開始しましょう。

## <a id="Config"></a>手順 3: アプリの構成を設定する
普段使用しているテキスト エディターで ```config.js``` を開きます。

次に、以下にコード スニペットをコピーして貼り付け、プロパティ ```config.endpoint``` と ```config.primaryKey``` を Azure Cosmos DB エンドポイント URI および主キーに設定します。 これらの構成はどちらも [Azure ポータル](https://portal.azure.com)にあります。

![Node.js チュートリアル - Azure Cosmos DB アカウントを示す Azure ポータルのスクリーンショット。アクティブなハブが強調表示され、[Azure Cosmos DB アカウント] ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キーの値が強調表示されている - ノード データベース][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

```database id```、```collection id```、```JSON documents``` をコピーして ```config``` オブジェクトに貼り付けます。```config.endpoint``` プロパティと ```config.primaryKey``` プロパティの設定に続けて追加してください。 データベースに保存するデータが既にある場合は、ドキュメント定義を追加するのではなく、Azure Cosmos DB の[データ移行ツール](import-data.md)を使用できます。

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


データベース、コレクション、ドキュメント定義は、それぞれ、Azure Cosmos DB の ```database id```、```collection id```、ドキュメントのデータになります。

最後に、```app.js``` ファイル内で参照できるように、```config``` オブジェクトをエクスポートします。

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>手順 4: Azure Cosmos DB アカウントに接続する
テキスト エディターで、空の ```app.js``` ファイルを開きます。 ```documentdb``` モジュールと新たに作成した ```config``` モジュールをインポートするために、以下のコードをコピーして貼り付けます。

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

先ほど保存した ```config.endpoint``` と ```config.primaryKey``` を使用して、新しい DocumentClient を作成するために、次のコードをコピーして貼り付けます。

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Azure Cosmos DB クライアントを初期化するためのコードは以上で完成です。続いて、Azure Cosmos DB リソースの使用方法について説明します。

## <a name="step-5-create-a-node-database"></a>手順 5: ノード データベースを作成する
NOTFOUND の HTTP ステータス、データベースの URL、コレクションの URL を設定するために、以下のコードをコピーして貼り付けます。 Azure Cosmos DB クライアントは、これらの URL を通じて適切なデータベースとコレクションを検出します。

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

[データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。 データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。

```config``` オブジェクトに指定された ```id``` で新しいデータベースを作成するための **getDatabase** 関数をコピーして app.js ファイルに貼り付けます。 この関数は、 ```FamilyRegistry``` の ID が重複する既存のデータベースが存在しないかどうかをチェックします。 存在する場合は、新しいデータベースを作成する代わりに、そのデータベースが返されます。

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

終了メッセージを出力して **getDatabase** 関数を呼び出すヘルパー関数 **exit** を追加するために、**getDatabase** 関数を設定した箇所に以下のコードをコピーして貼り付けます。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB データベースが作成されました。

## <a id="CreateColl"></a>手順 6: コレクションを作成する
> [!WARNING]
> **createCollection** は新しいコレクションを作成します。これは価格に影響します。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。

```config``` オブジェクトに指定された ```id``` で新しいコレクションを作成するために、**getCollection** 関数をコピーして app.js ファイルの **getDatabase** 関数の下に貼り付けます。 ここでも、同じ ID を持つ ```FamilyCollection``` が存在しないことが確認されます。 存在する場合は、新しいコレクションを作成する代わりに、そのコレクションが返されます。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

**getDatabase** の呼び出しの下に、**getCollection** 関数を実行するためのコードをコピーして貼り付けます。

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB コレクションが作成されました。

## <a id="CreateDoc"></a>手順 7: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 これで、Azure Cosmos DB にドキュメントを挿入できます。

```config``` オブジェクトに保存された JSON データを追加してドキュメントを作成するために、**getFamilyDocument** 関数をコピーして **getCollection** 関数のすぐ下に貼り付けます。 ここでも、同じ ID を持つドキュメントが存在しないことが確認されます。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

**getCollection** の呼び出しの下に、**getFamilyDocument** 関数を実行するためのコードをコピーして貼り付けます。

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB ドキュメントが作成されました。

![Node.js チュートリアル - アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図 - ノード データベース](./media/documentdb-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>手順 8: Azure Cosmos DB リソースにクエリを実行する
Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する[豊富なクエリ](documentdb-sql-query.md)がサポートされています。 次のサンプル コードは、コレクションでドキュメントに対して実行できるクエリを示しています。

**queryCollection** 関数をコピーして app.js ファイルの **getFamilyDocument** 関数の下に貼り付けます。 以下のとおり、Azure Cosmos DB は SQL に似たクエリをサポートしています。 複雑なクエリを構築する方法の詳細については、[Query Playground](https://www.documentdb.com/sql/demo) および[クエリに関するドキュメント](documentdb-sql-query.md)を参照してください。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


次の図は、作成したコレクションに対して Azure Cosmos DB SQL クエリ構文がどのように呼び出されるかを示しています。

![Node.js チュートリアル - クエリのスコープおよび意味を示す図 - ノード データベース](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Azure Cosmos DB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md#FromClause) キーワードを省略できます。 したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。 Azure Cosmos DB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

**getFamilyDocument** の呼び出しの下に、**queryCollection** 関数を実行するためのコードをコピーして貼り付けます。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB ドキュメントにクエリが実行されました。

## <a id="ReplaceDocument"></a>手順 9: ドキュメントを置換する
Azure Cosmos DB は、JSON ドキュメントの置換をサポートします。

**replaceFamilyDocument** 関数をコピーして app.js ファイルの **queryCollection** 関数の下に貼り付けます。

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

**queryCollection** の呼び出しの下に、**replaceDocument** 関数を実行するためのコードをコピーして貼り付けます。 さらに、もう一度 **queryCollection** を呼び出してドキュメントが正しく変更されたことを確認するコードを追加します。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB ドキュメントが置換されました。

## <a id="DeleteDocument"></a>手順 10: ドキュメントを削除する
Azure Cosmos DB は、JSON ドキュメントの削除をサポートします。

**deleteFamilyDocument** 関数をコピーして **replaceFamilyDocument** 関数の下に貼り付けます。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

2 つ目の **queryCollection** の呼び出しの下に、**deleteDocument** 関数を実行するためのコードをコピーして貼り付けます。

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

お疲れさまでした。 これで、Azure Cosmos DB ドキュメントが削除されました。

## <a id="DeleteDatabase"></a>手順 11: ノード データベースを削除する
作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。

**cleanup** 関数をコピーして **deleteFamilyDocument** 関数の下に貼り付けます。この関数によって、データベースとすべての子リソースが削除されます。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

**deleteFamilyDocument** の呼び出しの下に、**cleanup** 関数を実行するためのコードをコピーして貼り付けます。

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>手順 12: Node.js アプリケーションの全体的な実行の流れ
以上の説明をまとめると、定義した関数は次の順に呼び出されます。

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

開始したアプリケーションの出力が表示されます。 出力は、次のテキスト例のようなものになるはずです。

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

お疲れさまでした。 Node.js チュートリアルが完了し、初めての Azure Cosmos DB コンソール アプリケーションが完成しました。

## <a id="GetSolution"></a>完全な Node.js チュートリアル ソリューションを入手する
このチュートリアルの手順を実行する時間がない場合や、コードをダウンロードするだけの場合は、[GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) から入手できます。

この記事のすべてのサンプルを含む GetStarted ソリューションを実行するには、以下が必要です。

* [Azure Cosmos DB アカウント][create-account]。
* GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) ソリューション。

npm で **documentdb** モジュールをインストールします。 次のコマンドを使用します。

* ```npm install documentdb --save```

次に、```config.js``` ファイルで、「[手順 3: アプリの構成を設定する](#Config)」の説明に従って、config.endpoint と config.primaryKey の値を更新します。 

次に、ターミナルで ```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

以上です。ビルドすれば完了です。 

## <a name="next-steps"></a>次のステップ
* さらに複雑な Node.js のサンプルが必要ですか。 「[Azure Cosmos DB を使用した Node.js Web アプリケーションの作成](documentdb-nodejs-application.md)」を参照してください。
* [Azure Cosmos DB アカウントを監視する](monitor-accounts.md)方法を学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
* プログラミング モデルの詳細については、[Azure Cosmos DB のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/documentdb/)の開発に関するセクションを参照してください。

[create-account]: create-documentdb-dotnet.md#create-account
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
