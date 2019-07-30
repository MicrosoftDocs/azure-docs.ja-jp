---
title: Qlik Sense を Azure Cosmos DB に接続してデータを可視化する
description: この記事では、Azure Cosmos DB を Qlik Sense に接続してデータを可視化するのに必要な手順を説明します。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 3a955060eb5f19544860c1c97abe1577084bef24
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985547"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Qlik Sense を Azure Cosmos DB に接続してデータを可視化する

Qlik Sense は、さまざまなソースからのデータを 1 つのビューに結合するデータ視覚化ツールです。 Qlik Sense はデータ内の可能なすべての関係をインデックス化するので、データを直ちに分析して理解することができます。 Qlik Sense を使用して Azure Cosmos DB データを視覚化できます。 この記事では、Azure Cosmos DB を Qlik Sense に接続してデータを可視化するのに必要な手順を説明します。 

> [!NOTE]
> Qlik Sense と Azure Cosmos DB との接続は現在、SQL API および Azure Cosmos DB の MongoDB 用 API のアカウントでのみサポートされています。

次の機能で、Qlik Sense と Azure Cosmos DB を接続することができます。

* Cosmos DB SQL API (ODBC コネクタを使用)。

* Azure Cosmos DB の MongoDB 用 API (Qlik Sense MongoDB コネクタを使用。現在プレビュー段階)。

* Azure Cosmos DB の MongoDB 用 API および SQL API (Qlik Sense で REST API コネクタを使用)。

* Cosmos DB Mongo DB API (Qlik Core 用 gRPC コネクタを使用)。
この記事では、ODBC コネクタを使用して Cosmos DB SQL API に接続する方法を詳しく説明します。

この記事では、ODBC コネクタを使用して Cosmos DB SQL API に接続する方法を詳しく説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行する前に、次のリソースを準備しておいてください。

* [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) をダウンロードするか、[Qlik Sense マーケットプレース アイテムをインストールする](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)ことにより Azure で Qlik Sense をセットアップします。

* [ビデオ ゲーム データ](https://www.kaggle.com/gregorut/videogamesales)をダウンロードします。このサンプル データは CSV 形式です。 あとでこのデータを Cosmos DB アカウントに保存して Qlik Sense で視覚化します。

* クイック スタート記事の「[アカウントの作成](create-sql-api-dotnet.md#create-account)」セクションで説明されている手順に従って、Azure Cosmos DB SQL API アカウントを作成します。

* [データベースとコレクションを作成](create-sql-api-java.md#add-a-container)します。コレクションのスループット値を 1000 RU/秒に設定できます。 

* サンプル ビデオ ゲームの売上データを Cosmos DB アカウントに読み込みます。 データをインポートするには Azure Cosmos DB データ移行ツールを使用できます。[順次](import-data.md#SQLSeqTarget)または[一括](import-data.md#SQLBulkTarget)のデータ インポートが可能です。 データが Cosmos DB アカウントにインポートされるまでに 3 分から 5 分かかります。

* 「[ODBC ドライバーを使用して Cosmos DB に接続する](odbc-driver.md)」の記事の手順に従い、ODBC ドライバーをダウンロード、インストール、および構成します。 ビデオ ゲーム データは単純なデータ セットですから、スキーマを編集する必要はありません。既定のコレクション マッピング スキーマを使用してください。

## <a name="connect-qlik-sense-to-cosmos-db"></a>Qlik Sense を Cosmos DB に接続する

1. Qlik Sense を開き、 **[新しいアプリの作成]** を選択します。 アプリに名前を付けて、 **[追加]** を選択します。

   ![新規 Qlik Sense アプリの作成](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. 新しいアプリが正常に作成された後、 **[アプリを開く]** を選択し、 **[ファイルやその他のソースからデータを追加する]** を選択します。 

3. データ ソースから **[ODBC]** を選択すると、新しい接続のセットアップ ウィンドウが開きます。 

4. **[ユーザー DSN]** に切り替え、既に作成した ODBC 接続を選択します。 接続の名前を付けて、 **[作成]** を選択します。 

   ![新しい接続を作成する](./media/visualize-qlik-sense/create-new-connection.png)

5. 接続を作成した後、ビデオ ゲーム データが配置されているデータベースとコレクションを選択し、プレビューすることができます。

   ![データベースとコレクションの選択](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. 次に **[データの追加]** を選択して Qlik Sense にデータを読み込みます。 Qlik Sense にデータを読み込んだ後、分析情報を生成してデータを分析することができます。 その分析情報を使用することも、ビデオ ゲーム売上を調べる独自のアプリを作成することもできます。 次の図に示します 

   ![データの視覚化](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>ODBC による接続時の制限事項 

Cosmos DB は、開発者のニーズに基づいてモデル化されたドライバーを含むスキーマのない分散型データベースです。 ODBC ドライバーでは、列、列のデータ型、その他のプロパティを推測するためにスキーマ付きのデータベースが必要です。 リレーショナル機能を持つ通常の SQL クエリや DML 構文は Cosmos DB SQL API に当てはまりません。これは、SQL API が ANSI SQL でないためです。 この理由で、ODBC ドライバーを介して発行される SQL ステートメントは Cosmos DB 固有の SQL 構文に変換され、その構文にはすべてのコンストラクトの同等機能があるわけではありません。 このような変換の問題を防ぐには、ODBC 接続のセットアップ時にスキーマを適用する必要があります。 「[ODBC ドライバーを使用した接続](odbc-driver.md)」の記事では、スキーマの構成に役立つ提案や手法が示されています。 Cosmos DB アカウント内のそれぞれのデータベース/コレクションごとに、このマッピングを必ず作成してください。

## <a name="next-steps"></a>次の手順

Power BI などの別の視覚化ツールを使用している場合は、次のドキュメントの手順に従ってそれに接続できます。

* [Power BI コネクタを使用して Cosmos DB データを視覚化する](powerbi-visualize.md)
