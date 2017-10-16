---
title: "MongoDB 用 Azure Cosmos DB API で mongoimport と mongorestore を使用する | Microsoft Docs"
description: "mongoimport と mongorestore を使用して、MongoDB 用 API アカウントにデータをインポートする方法について説明します"
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: MongoDB データをインポートする 

MongoDB 用 API で使用するために MongoDB から Azure Cosmos DB アカウントにデータを移行するには、次の操作を行う必要があります。

* *mongoimport.exe* または *mongorestore.exe* のいずれかを [MongoDB Download Center](https://www.mongodb.com/download-center) からダウンロードします。
* [MongoDB 用 API 接続文字列](connect-mongodb-account.md)を取得します。

使用する必要があります MongoDB からデータをインポートして、Azure Cosmos DB で使用する予定の場合、[データ移行ツール](import-data.md)データをインポートします。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 接続文字列の取得
> * mongoimport を使用した MongoDB データのインポート
> * mongorestore を使用した MongoDB データのインポート

## <a name="prerequisites"></a>前提条件

* スループットを上げる: データの移行にかかる時間は、コレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、[Azure Cosmos DB のパフォーマンス レベルと価格レベル](performance-levels.md)に関するページを参照してください。

* SSL を有効にする: Azure Cosmos DB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事で説明する手順の中に、mongoimport と mongorestore で SSL を有効にする方法が含まれています。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>接続文字列情報 (ホスト、ポート、ユーザー名、およびパスワード) を見つける

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Cosmos DB]** エントリをクリックします。
2. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。
3. **[接続文字列]** ブレードで、**[接続文字列]** をクリックします。  
右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

    ![[接続文字列] ブレード](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>mongoimport を使用して MongoDB 用 API にデータをインポートする

Azure Cosmos DB アカウントにデータをインポートするには、次のテンプレートを使用します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。  

テンプレート:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

例:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>mongorestore を使用して MongoDB 用 API にデータをインポートする

MongoDB 用 API アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。

テンプレート:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

例:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>移行を成功させるためのガイド

1. コレクションを事前に作成し、拡大縮小します。
        
    * Azure Cosmos DB は、既定で、1,000 要求ユニット (RU) を含む新しい MongoDB コレクションをプロビジョニングします。 mongoimport、mongorestore、または mongomirror を使用して移行を開始する前に、[Azure Portal](https://portal.azure.com) または MongoDB ドライバーとツールからすべてのコレクションを事前に作成してください。 コレクションが 10 GB を超える場合は、適切なシャード キーを使用して[シャード/パーティション コレクション](partition-data.md)を作成してください。

    * [Azure Portal](https://portal.azure.com) では、コレクションのスループットを 1,000 RU (単一パーティションのコレクションの場合) または 2,500 RU (移行のためだけにシャード化されたコレクションの場合) から引き上げます。 スループットが高くなるほど、スロットルを回避し、移行に要する時間を短縮できます。 時間単位で課金される Azure Cosmos DB では、移行直後にスループットを低くすることでコストを削減できます。

2. 1 回のドキュメントの書き込みに対するおおよその RU 請求金額を計算します。

    a. MongoDB シェルを使用して Azure Cosmos DB MongoDB データベースに接続します。 手順については、「[Connect a MongoDB application to Azure Cosmos DB (Azure Cosmos DB への MongoDB アプリケーションの接続)](connect-mongodb-account.md)」を参照してください。
    
    b. MongoDB シェルからサンプル ドキュメントの 1 つを使用して、サンプルの挿入コマンドを実行します。
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. ```db.runCommand({getLastRequestStatistics: 1})``` を実行すると、次のような応答が表示されます。
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. 要求の使用量を書き留めます。
    
3. マシンから Azure Cosmos DB クラウド サービスまでの待ち時間を決定します。
    
    a. MongoDB シェルで ```setVerboseShell(true)``` コマンドを使用して、詳細ログ記録を有効にします。
    
    b. ```db.coll.find().limit(1)``` を使用して、データベースに対して単純なクエリを実行します。 次のような応答が返されます。

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. 移行前に挿入したドキュメントを削除して、重複するドキュメントがないことを確認します。 ドキュメントを削除するには、```db.coll.remove({})``` コマンドを使用します。

5. *batchSize* と *numInsertionWorkers* の概算値を計算します。

    * *batchSize* については、プロビジョニングされた RU の総数を、手順 3. の 1 回のドキュメント書き込みで消費された RU 数で除算します。
    
    * 計算された *batchSize* 値が 24 以下の場合は、その数値を *batchSize* 値として使用します。
    
    * 計算された *batchSize* 値が 24 を超える場合は、*batchSize* 値を 24 に設定します。
    
    * *NumInsertionWorkers*、この式を使用: *numInsertionWorkers = (プロビジョニング スループット * 待機時間 (秒))/(バッチ サイズ * RUs を 1 回の書き込み消費)*です。
        
    |プロパティ|値|
    |--------|-----|
    |batchSize| 24 |
    |プロビジョニングされた RU 数 | 10000 |
    |待機時間 | 0.100 秒 |
    |1 回のドキュメント書き込みで消費された RU | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10,000 RU x 0.1) / (24 x 10 RU) = 4.1666*

6. 最後の移行コマンドを実行します。

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進み、Azure Cosmos DB を使用して MongoDB データにクエリを実行する方法を学習することができます。 

> [!div class="nextstepaction"]
>[MongoDB データにクエリを実行する方法](../cosmos-db/tutorial-query-mongodb.md)
