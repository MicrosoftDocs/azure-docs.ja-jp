---
title: MongoDB ネイティブ ツールを使用して、MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する
description: MongoDB ネイティブ ツールを使用して、MongoDB インスタンスから Azure Cosmos DB に小規模なデータセットを移行する方法について説明します
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101655753"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>チュートリアル:MongoDB ネイティブ ツールを使用して、MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する

MongoDB ネイティブ ツールを使用して、MongoDB のオンプレミスまたはクラウドのインスタンスから Azure Cosmos DB の MongoDB 用 API に、データベースのオフライン (1 回限り) の移行を実行できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * 自分のユースケースに適した MongoDB ネイティブ ツールを選択する
> * 移行を実行する。
> * 移行を監視する。
> * 移行が正常に終了したことを確認する。

このチュートリアルでは、MongoDB ネイティブ ツールを使用して、Azure 仮想マシンでホストされている MongoDB 内のデータセットを、Azure Cosmos DB の MongoDB 用 API に移行します。 MongoDB ネイティブ ツールは、既存の MongoDB インスタンスに対するデータ操作を容易にするバイナリのセットです。 Azure Cosmos DB では Mongo API が公開されているため、MongoDB ネイティブ ツールを使うと Azure Cosmos DB にデータを挿入できます。 このドキュメントでは、"*mongoexport および mongoimport*" または "*mongodump および mongorestore*" を使用して MongoDB インスタンスからデータを移行する方法に焦点を当てています。 ネイティブ ツールは接続文字列を使用して MongoDB に接続するため、どこからでも実行できます。ただし、ファイアウォールの問題を回避するために、MongoDB インスタンスと同じネットワーク内でこれらのツールを実行することをお勧めします。 

MongoDB ネイティブ ツールでは、ホスト ハードウェアで可能な速度でのみ、データを移動できます。ネイティブ ツールは、総移行時間が問題にならない小規模なデータセットに使用する場合の最も簡単なソリューションです。 スケーラブルな移行パイプラインが必要な場合は、[MongoDB Spark コネクタ](https://docs.mongodb.com/spark-connector/current/)、[Azure Data Migration Service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)、または [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) の方が適している可能性があります。

MongoDB ソースをまだセットアップしていない場合は、記事「[Azure の Windows VM に MongoDB をインストールして構成する](/previous-versions/azure/virtual-machines/windows/install-mongodb)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* スループットの見積もり、パーティション キーの選択、インデックス作成ポリシーなど、[移行前の手順を完了](../cosmos-db/mongodb-pre-migration.md)します。
* [Azure Cosmos DB の MongoDB 用 API アカウントを作成します](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
* MongoDB インスタンスにログインします
    * [このリンクから MongoDB ネイティブ ツールをダウンロードしてインストールします](https://www.mongodb.com/try/download/database-tools)。
        * **MongoDB ネイティブ ツールのバージョンが、既存の MongoDB インスタンスと一致していることを確認します**。
        * MongoDB インスタンスのバージョンが Azure Cosmos DB Mongo API と異なる場合は、**MongoDB ネイティブ ツールの両方のバージョンをインストールし、MongoDB と Azure Cosmos DB Mongo API にそれぞれ適切なツール バージョンを使用します**。
    * `readWrite` アクセス許可を持つユーザーを追加します (まだそのようなユーザーがいない場合)。 このチュートリアルの後の方で、*mongoexport* および *mongodump* ツールに、このユーザー名とパスワードを指定します。

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Azure Cosmos DB のサーバー側の再試行を構成する

MongoDB から Azure Cosmos DB に移行するお客様は、リソース ガバナンス機能を活用できます。これにより、プロビジョニング済み RU/秒のスループットを完全に利用できるようになります。 Azure Cosmos DB では、特定の要求がコンテナーにプロビジョニングされている RU/秒を超え、その要求を再試行する必要がある場合に、移行の過程でその要求を調整することができます。 移行ツールと Azure Cosmos DB の間のネットワーク ホップに関係するラウンドトリップ時間は、その要求の全体的な応答時間に影響します。さらに、MongoDB ネイティブ ツールによって再試行が処理されない場合があります。 Azure Cosmos DB の "*サーバー側の再試行*" 機能を使用すると、サービスで調整エラー コードをインターセプトし、はるかに短いラウンドトリップ時間で再試行することができるため、要求の応答時間が大幅に短縮されます。 MongoDB ネイティブ ツールの観点からは、再試行を処理する必要性が最小限に抑えられます。これは、移行中のエクスペリエンスに良い影響を与えます。

サーバー側の再試行機能は、Azure Cosmos DB ポータルの *[機能]* ブレードで確認できます

![MongoDB SSR 機能のスクリーンショット。](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

*[無効]* になっている場合は、次のように有効にすることをお勧めします

![MongoDB SSR の有効化のスクリーンショット。](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>適切な MongoDB ネイティブ ツールを選択する

![最適な MongoDB ネイティブ ツールの選択の図。](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* "*mongoexport と mongoimport*" は、MongoDB データベースのサブセットを移行するための最適な移行ツールのペアです。
    * *mongoexport* は、既存のデータを人間が判読できる JSON ファイルまたは CSV ファイルにエクスポートします。 *mongoexport* は、エクスポートする既存のデータのサブセットを指定する引数を受け取ります。 
    * *mongoimport* は、JSON ファイルまたは CSV ファイルを開き、その内容を対象のデータベース インスタンス (ここでは Azure Cosmos DB) に挿入します。 
    * JSON と CSV はコンパクトな形式ではないことに注意してください。*mongoimport* によって Azure Cosmos DB にデータが送信される際に、追加のネットワーク料金が発生することがあります。
* "*mongodump と mongorestore*" は、MongoDB データベース全体を移行するための最適な移行ツールのペアです。 コンパクトな BSON 形式なら、データが Azure Cosmos DB に挿入される際に、ネットワーク リソースがより効率的に使用されます。
    * *mongodump* は、既存のデータを BSON ファイルとしてエクスポートします。
    * *mongorestore* は、BSON ファイル ダンプを Azure Cosmos DB にインポートします。
* なお、Azure Cosmos DB Mongo API にインポートするのが小さな JSON ファイルだけである場合、データを取り込むための簡易なソリューションは *mongoimport* ツールです。

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Azure Cosmos DB Mongo API の資格情報を収集する

Azure Cosmos DB Mongo API は、MongoDB ネイティブ ツールで利用できる、互換性のあるアクセス資格情報を提供します。 Azure Cosmos DB Mongo API にデータを移行するには、これらのアクセス資格情報を手元に用意しておく必要があります。 これらの資格情報を見つけるには、次のように操作します。

1. Azure ポータルを開きます
1. 自分の Azure Cosmos DB Mongo API アカウントに移動します
1. 左側のナビゲーションで *[接続文字列]* ブレードを選択します。すると、次のような表示になるはずです。

    ![Azure Cosmos DB の資格情報のスクリーンショット。](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *[ホスト]* - Azure Cosmos DB エンドポイントが MongoDB ホスト名として機能します
    * *[ポート]* - MongoDB ネイティブ ツールが Azure Cosmos DB に接続するときに、このポートを明示的に指定する必要があります
    * *[ユーザー名]* - Azure Cosmos DB エンドポイントのドメイン名のプレフィックスが MongoDB のユーザー名として機能します
    * *[パスワード]* - Azure Cosmos DB マスター キーが MongoDB パスワードとして機能します
    * また、 *[SSL]* フィールド (値は `true`) をメモしておきます。MongoDB ネイティブ ツールは、Azure Cosmos DB にデータを書き込むときに、SSL を有効にする **必要があります**

## <a name="perform-the-migration"></a>移行する

1. 移行するデータベースとコレクションを選択します。 この例では、*edx* データベースの *query* コレクションを MongoDB から Azure Cosmos DB に移行しています。

このセクションの残りの部分では、前のセクションで選択したツールのペアを使用する方法について説明します。

### <a name="mongoexportmongoimport"></a>*mongoexport と mongoimport*

1. 移行元 MongoDB インスタンスからデータをエクスポートするには、MongoDB インスタンス マシンでターミナルを開きます。 Linux マシンの場合は、次のように入力します

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Windows では、実行可能ファイルは `mongoexport.exe` です。 既存の MongoDB データベース インスタンスのプロパティに基づいて、*HOST*、*PORT*、*USERNAME*、および *PASSWORD* を指定する必要があります。 
    
    また、MongoDB データセットのサブセットのみをエクスポートするように選択することもできます。 そのための 1 つの方法は、フィルター引数を追加することです。
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    フィルター `{"field1":"value1"}` に一致するドキュメントのみがエクスポートされます。

    呼び出しを実行すると、`edx.json` ファイルが生成されるはずです。

    ![mongoexport の呼び出しのスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. 同じターミナルを使用して、`edx.json` を Azure Cosmos DB にインポートできます。 Linux マシンで `mongoimport` を実行している場合は、次のように入力します

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Windows では、実行可能ファイルは `mongoimport.exe` です。 前に収集した Azure Cosmos DB 資格情報に基づいて、*HOST*、*PORT*、*USERNAME*、および *PASSWORD* を指定する必要があります。 
1. *mongoimport* からのターミナル出力を **監視** します。 次のような、移行状態の更新を含むテキスト行がターミナルに出力されるはずです。

    ![mongoimport の呼び出しのスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. 最後に、Azure Cosmos DB を調べて、移行が成功したことを **検証** します。 Azure Cosmos DB ポータルを開き、Data Explorer に移動します。 (1) *edx* データベースと *importedQuery* コレクションが作成され、(2) データのサブセットのみをエクスポートした場合は、データの目的のサブセットに一致するドキュメント "*だけ*" が *importedQuery* に含まれているはずです。 次の例では、1 つのドキュメントだけが、フィルター `{"field1":"value1"}` に一致しました。

    ![Cosmos DB のデータ検証のスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump と mongorestore*

1. MongoDB インスタンスの BSON データ ダンプを作成するには、MongoDB インスタンス マシンでターミナルを開きます。 Linux マシンの場合は、次のように入力します

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    既存の MongoDB データベース インスタンスのプロパティに基づいて、*HOST*、*PORT*、*USERNAME*、および *PASSWORD* を指定する必要があります。 `edx-dump` ディレクトリが生成され、`edx-dump` のディレクトリ構造が移行元 MongoDB インスタンスのリソース階層 (データベースとコレクションの構造) を再現しているはずです。 各コレクションは、BSON ファイルによって表されます。

    ![mongodump の呼び出しのスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. 同じターミナルを使用して、`edx-dump` の内容を Azure Cosmos DB に復元できます。 Linux マシンで `mongorestore` を実行している場合は、次のように入力します

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Windows では、実行可能ファイルは `mongorestore.exe` です。 前に収集した Azure Cosmos DB 資格情報に基づいて、*HOST*、*PORT*、*USERNAME*、および *PASSWORD* を指定する必要があります。 
1. *mongorestore* からのターミナル出力を **監視** します。 次のように、移行状態の更新を示す行がターミナルに出力されるはずです。

    ![mongorestore の呼び出しのスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. 最後に、Azure Cosmos DB を調べて、移行が成功したことを **検証** します。 Azure Cosmos DB ポータルを開き、Data Explorer に移動します。 (1) *edx* データベースと *importedQuery* コレクションが作成され、(2) 移行元コレクションのデータセット "*全体*" が *importedQuery* に含まれているはずです。

    ![Cosmos DB mongorestore データの検証のスクリーンショット。](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>移行後の最適化

MongoDB データベースに格納されているデータを Azure Cosmos DB の MongoDB 用 API に移行した後、Azure Cosmos DB に接続してデータを管理できます。 また、インデックス作成ポリシーの最適化、既定の整合性レベルの更新、Azure Cosmos DB アカウントのグローバル分散の構成など、移行後の他の最適化手順を実行することもできます。 詳細については、「[移行後の最適化](../cosmos-db/mongodb-post-migration.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [Cosmos DB サービスの情報](https://azure.microsoft.com/services/cosmos-db/)
* [MongoDB データベース ツールのドキュメント](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>次のステップ

* Microsoft の「[Database Migration Guide](https://datamigration.microsoft.com/)」(データベース移行ガイド) で、他のシナリオの移行ガイダンスを確認する。