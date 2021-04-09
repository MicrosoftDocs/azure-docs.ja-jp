---
title: Azure Cosmos DB と Azure Functions を使用したサーバーレス データベース コンピューティング
description: Azure Cosmos DB と Azure Functions の両方を使用して、イベント ドリブンのサーバーレス コンピューティング アプリケーションを作成する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 73a34cc27eaba33d04f4d31585c7f494f58e7274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334074"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB と Azure Functions を使用したサーバーレス データベース コンピューティング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

サーバーレス コンピューティングとは、繰り返し可能でステートレスな個々のロジックに集中できる機能です。 個々のロジックにインフラストラクチャの管理は必要ありません。秒単位またはミリ秒単位の実行時間のみリソースを使用します。 サーバーレス コンピューティングのムーブメントの中心には、関数があります。関数は、Azure エコシステムの[Azure Functions](https://azure.microsoft.com/services/functions) で使用できます。 Azure での他のサーバーレス実行環境については、「[Azure でのサーバーレス](https://azure.microsoft.com/solutions/serverless/)」ページをご覧ください。 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) と Azure Functions 間のネイティブ統合を使用すると、Azure Cosmos DB アカウントからデータベースのトリガー、入力バインディング、出力バインディングを直接作成できます。 Azure Functions と Azure Cosmos DB を使用すると、グローバル ユーザー ベース向けの、リッチ データに低待機時間でアクセスできる、イベント ドリブンのサーバーレス アプリケーションを作成およびデプロイすることができます。

## <a name="overview"></a>概要

Azure Cosmos DB と Azure Functions を使用して、次の方法でデータベースとサーバーレス アプリケーションを統合できます。

* イベント ドリブンの **Cosmos DB 用 Azure Functions トリガー** を作成します。 このトリガーは、[変更フィード](change-feed.md) ストリームを使用して Azure Cosmos コンテナーの変更を監視します。 コンテナーに変更が加えられると、変更フィード ストリームがトリガーに送信され、それによって Azure Functions が呼び出されます。
* あるいは、**入力バインディング** を使用して、Azure 関数を Azure Cosmos コンテナーにバインドします。 関数が実行されると、入力バインディングはコンテナーのデータを読み取ります。
* **出力バインディング** を使用して、関数を Azure Cosmos コンテナーにバインドします。 関数が完了すると、出力バインディングはコンテナーにデータを書き込みます。

> [!NOTE]
> 現時点では、Cosmos DB 用 Azure Functions トリガー、入力バインディング、および出力バインディングは、SQL API で使用する場合にのみサポートされます。 他のすべての Azure Cosmos DB API については、API 用の静的クライアントを使用して関数からデータベースにアクセスする必要があります。


次の各図は、これら 3 つの統合を示しています。 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Azure Cosmos DB と Azure Functions を統合する方法" border="false":::

Azure Cosmos DB 用 Azure Functions トリガー、入力バインディング、および出力バインディングは、次の組み合わせで使用できます。

* Cosmos DB 用 Azure Functions トリガーは、別の Azure Cosmos コンテナーへの出力バインディングで使用できます。 関数が変更フィードの項目に対してアクションを実行した後に、別のコンテナーに書き込むことができます (同じコンテナーに書き込むと、実質的に再帰的ループが作成されます)。 または、Cosmos DB 用 Azure Functions トリガーと出力バインディングを使用して、実質的に 1 つのコンテナー内の変更されたすべての項目を別のコンテナーに移行できます。
* Azure Cosmos DB の入力バインディングと出力バインディングは、同じ Azure Functions で使用できます。 この方法は、入力バインディングで特定のデータを検索し、Azure Functions で変更し、変更後に同じコンテナーまたは別のコンテナーに保存する場合に適しています。
* Azure Cosmos コンテナーへの入力バインディングは Cosmos DB 用 Azure Functions トリガーと同じ関数で使用でき、また出力バインディングの有無にかかわらず使用できます。 この組み合わせを使用すると、ショッピング カート サービスの新しい注文の変更フィードに最新の為替情報を適用できます (為替コンテナーに対する入力バインディングで取得します)。 最新の為替換算を適用して更新した後のショッピング カート合計は、出力バインディングを使用して 3 つ目のコンテナーに書き込むことができます。

## <a name="use-cases"></a>ユース ケース

次のユース ケースでは、データをイベント ドリブンの Azure Functions に接続して Azure Cosmos DB データを最大限に活用する方法をいくつか紹介します。

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT のユース ケース - Cosmos DB 用 Azure Functions トリガーと出力バインディング

IoT 実装では、接続されている車のエンジンのチェック ランプが点灯したときに、関数を呼び出すことができます。

**実装:** Cosmos DB 用 Azure Functions トリガーと出力バインディングを使用する

1. **Cosmos DB 用 Azure Functions トリガー** を使用して、接続されている車のエンジンのチェック ランプの点灯など、車の警告に関連するイベントをトリガーします。
2. エンジンのチェック ランプが点灯すると、センサー データが Azure Cosmos DB に送信されます。
3. Azure Cosmos DB で新しいセンサー データ ドキュメントが作成または更新された後、それらの変更が Cosmos DB 用 Azure Functions トリガーにストリームされます。
4. トリガーは、センサー データ コレクションに対するデータの変更ごとに呼び出されます。また、変更が変更フィード経由でストリームされたときにも呼び出されます。
5. 関数でしきい値の条件を使用して、センサー データを保証部門に送信します。
6. 温度が特定の値を超えた場合も、警告が所有者に送信されます。
7. 関数への **出力バインディング** によって、別の Azure Cosmos コンテナー内の車の記録が更新され、エンジンのチェック イベントに関する情報が格納されます。

次の図は、このトリガーで Azure Portal で書き込まれるコードを示しています。

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Azure portal で Cosmos DB 用 Azure Functions トリガーを作成する":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>財務ユース ケース - タイマー トリガーと入力バインディング

財務実装では、銀行口座の残高が一定額を下回ったときに関数を呼び出すことができます。

**実装:** タイマー トリガーと Azure Cosmos DB 入力バインディング

1. [タイマー トリガー](../azure-functions/functions-bindings-timer.md)を使用すると、**入力バインディング** を使用して、Azure Cosmos コンテナーに格納されている銀行口座残高の情報を一定の間隔で取得できます。
2. ユーザーが設定した残高の下限しきい値を下回った場合、Azure Functions のアクションが実行されます。
3. 出力バインディングは、サービス アカウントから、低い残高の各口座に指定された電子メール アドレスに対して電子メールが送信される [SendGrid 統合](../azure-functions/functions-bindings-sendgrid.md)です。

次の図は、このシナリオ用の Azure Portal のコードを示しています。

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="財務シナリオのタイマー トリガーの Index.js ファイル":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="財務シナリオのタイマー トリガーの Run.csx ファイル":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>ゲームのユース ケース - Cosmos DB 用 Azure Functions トリガーと出力バインディング 

ゲームでは、新しいユーザーを作成するときに、[Azure Cosmos DB Gremlin API](graph-introduction.md) を使用して、知っている可能性のある他のユーザーを検索することができます。 簡単に取得できるように、結果を Azure Cosmos DB または SQL データベースに書き込むことができます。

**実装:** Cosmos DB 用 Azure Functions トリガーと出力バインディングを使用する

1. Azure Cosmos DB の[グラフ データベース](graph-introduction.md)を使用してすべてのユーザーを格納することで、Cosmos DB 用 Azure Functions トリガーを使用する新しい関数を作成できます。 
2. 新しいユーザーが挿入されるたびに関数が呼び出され、結果は **出力バインディング** を使用して格納されます。
3. この関数は、グラフ データベースに対して、新しいユーザーに直接関連するすべてのユーザーを検索するクエリを実行し、そのデータセットを関数に返します。
4. このデータは、Azure Cosmos DB に格納されます。新規ユーザーに接続されている友人を表示する任意のフロントエンド アプリケーションから、このデータを簡単に取得できます。

### <a name="retail-use-case---multiple-functions"></a>小売のユース ケース - 複数の関数

小売の実装では、ユーザーがアイテムをバスケットに追加したときに、オプションのビジネス パイプライン コンポーネントの関数を柔軟に作成し、呼び出すことができるようになります。

**実装:** 1 つのコンテナーをリッスンする複数の Cosmos DB 用 Azure Functions トリガー

1. それぞれに Cosmos DB 用 Azure Functions トリガーが追加された複数の Azure 関数を作成できます。これらはすべて、ショッピング カート データの同じ変更フィードをリッスンします。 複数の関数が同じ変更フィードをリッスンする際は、各関数に新しいリース コレクションが必要になることに注意してください。 リース コレクションの詳細については、「[Change Feed Processor ライブラリの概要](change-feed-processor.md)」を参照してください。
2. 新しい項目がユーザーのショッピング カートに追加されるたびに、各関数はショッピング カート コンテナーの変更フィードから個別に呼び出されます。
   * 1 つの関数で現在のバスケットの内容を使用して、ユーザーが関心を持つ可能性がある他の項目の表示を変更することができます。
   * 別の関数で在庫の合計を更新できます。
   * また、別の関数は特定の製品に関する顧客情報をマーケティング部門に送信できます。マーケティング部門は宣伝メールを送信します。 

     任意の部門で変更フィードをリッスンして Cosmos DB 用 Azure Functions を作成し、プロセスの重要な注文処理イベントが遅れないようにすることができます。

これらのいずれのユース ケースでも、関数でアプリケーション自体が分離されるので、常に新しいアプリケーション インスタンスを起動する必要はありません。 その代わりに必要に応じて Azure Functions が個々の関数を起動して各プロセスを完了します。

## <a name="tooling"></a>ツール

Azure portal と Visual Studio 2019 では、Azure Cosmos DB と Azure Functions 間のネイティブ統合を使用できます。

* Azure Functions ポータルで、トリガーを作成できます。 クイック スタートの手順については、[Azure portal での Cosmos DB 用 Azure Functions トリガーの作成](../azure-functions/functions-create-cosmos-db-triggered-function.md)に関するページをご覧ください。
* Azure Cosmos DB ポータルで、同じリソース グループ内の既存の Azure Functions アプリに Cosmos DB 用 Azure Functions トリガーを追加できます。
* Visual Studio 2019 で、[Azure Functions Tools](../azure-functions/functions-develop-vs.md) を使用してトリガーを作成できます。

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>サーバーレス コンピューティングに Azure Functions 統合を選択する理由

Azure Functions には、スケーラブルなユニットの作業や、オンデマンドで実行できるロジックの簡潔な部分を作成する機能があります。インフラストラクチャをプロビジョニングまたは管理する必要はありません。 Azure Functions を使用すると、Azure Cosmos データベース内の変更に応答する本格的なアプリを作成する必要はなく、特定のタスクのための小さな再利用可能な関数を作成できます。 また、HTTP 要求または適時のトリガーなどのイベントに応答して、Azure Functions への入力または出力として Azure Cosmos DB データを使用することもできます。

Azure Cosmos DB は、サーバーレス コンピューティング アーキテクチャに推奨されるデータベースです。その理由は次のとおりです。

* **すべてのデータにすぐにアクセス**:Azure Cosmos DB の既定では、すべてのデータの [インデックスが自動的に作成され](index-policy.md)、それらのインデックスをすぐに使用できるため、格納されているすべての値に対するアクセス権を細かくすることができます。 つまり、データベースに対して新しい項目のクエリ、更新、追加をいつでも実行し、Azure Functions 経由ですぐにアクセスできます。

* **スキーマレス**。 Azure Cosmos DB はスキーマレスです。そのため、Azure Functions からすべてのデータ出力を一意に処理できます。 この "すべてを処理する" アプローチによって、すべてを Azure Cosmos DB に出力する多様な関数を簡単に作成できます。

* **スケーラブルなスループット**。 Azure Cosmos DB のスループットのスケール アップとスケール ダウンはすぐに行うことができます。 数百から数千単位の Functions のクエリがあり、同じコンテナーに書き込む場合、負荷を処理する [RU/秒](request-units.md)をスケール アップできます。 すべての関数は、割り当てられた RU/秒を使用して並列処理できます。また、データの[整合性](consistency-levels.md)が保証されます。

* **グローバル レプリケーション** ユーザーのいる場所に最も近いデータの位置を特定することで、[世界中](distribute-data-globally.md)の Azure Cosmos DB データをレプリケートして待機時間を短縮できます。 すべての Azure Cosmos DB クエリと同様に、イベント ドリブン トリガーのデータは、ユーザーに最も近い Azure Cosmos DB から読み取られます。

Azure Functions と統合してデータを格納し、深いインデックス作成が必要ない場合、または添付ファイルとメディア ファイルを格納する必要がある場合、[Azure Blob Storage トリガー](../azure-functions/functions-bindings-storage-blob.md)が適している可能性があります。

Azure Functions の利点: 

* **イベント ドリブン** です。 Azure Functions はイベント ドリブンです。また、Azure Cosmos DB の変更フィードをリッスンできます。 つまり、リッスン ロジックを作成する必要はなく、リッスンしている変更のみに注目するだけで済みます。 

* **無制限**。 関数は並列して実行され、必要な数のサービスを起動できます。 また、必要に応じてパラメーターを設定します。

* **クイック タスクに適しています**。 イベントが発生するたびに、サービスは関数の新しいインスタンスを起動します。関数が完了すると、イベントは直ちに閉じられます。 ユーザーは、関数が実行された時間に対してだけ支払います。

Flow、Logic Apps、Azure Functions、または WebJobs が実装に適しているかどうかがわからない場合は、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

それでは実際に Azure Cosmos DB と Azure Functions を接続してみましょう。 

* [Azure portal で Cosmos DB 用 Azure Functions トリガーを作成する](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Azure Cosmos DB 入力バインディングを使用して Azure Functions HTTP トリガーを作成する](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB のバインディングとトリガー](../azure-functions/functions-bindings-cosmosdb-v2.md)
