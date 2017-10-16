---
title: "サーバーなしのデータベースのコンピューティング - Azure Functions と Azure Cosmos DB | Microsoft Docs"
description: "Azure Cosmos DB と Azure Functions の両方を使用して、イベント ドリブンのサーバーなしのコンピューティング アプリケーションを作成する方法について説明します。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 084dacc6a07b64500652481fbcabf1b0fc44306c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング

サーバーなしのコンピューティングとは、繰り返し可能でステートレスな個々のロジックに集中できる機能です。 個々のロジックにインフラストラクチャの管理は必要ありません。秒単位またはミリ秒単位の実行時間のみリソースを使用します。 サーバーなしのコンピューティング移動の中心には、関数があります。関数は、Azure エコシステムの [Azure Functions](https://azure.microsoft.com/services/functions) で使用できます。

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) と Azure Functions 間のネイティブ統合を使用すると、Azure Cosmos DB アカウントからデータベースのトリガー、入力バインディング、出力バインディングを直接作成できます。 Azure Functions と Azure Cosmos DB を使用すると、グローバル ユーザー ベースのリッチ データに低待機時間でアクセスできる、イベント ドリブンのサーバーなしのアプリケーションを作成およびデプロイすることができます。

## <a name="overview"></a>概要

Azure Cosmos DB と Azure Functions を使用して、次の方法でデータベースとサーバーなしのアプリケーションを統合できます。

* Azure Functions でイベント ドリブンの **Azure Cosmos DB トリガー**を作成します。 このトリガーは、[変更フィード](change-feed.md) ストリームを利用して、Azure Cosmos DB コンテナーの変更を監視しています。 コンテナーに変更が加えられると、変更フィード ストリームがトリガーに送信され、それによって Azure Functions が呼び出されます。
* または、**入力バインディング**を使用して、Azure Functions を Azure Cosmos DB コレクションにバインドします。 関数が実行されると、入力バインディングはコンテナーのデータを読み取ります。
* **出力バインディング**を使用して、関数を Azure Cosmos DB コレクションにバインドします。 関数が完了すると、出力バインディングはコンテナーにデータを書き込みます。

次の各図は、これら 3 つの統合を示しています。 

![Azure Cosmos DB と Azure Functions を統合する方法](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB トリガー、入力バインディング、出力バインディングは、次の組み合わせで使用できます。
* Azure Cosmos DB トリガーと出力バインディングを組み合わせて別の Azure Cosmos DB コンテナーに使用することができます。 関数が変更フィードの項目に対してアクションを実行した後に、別のコンテナーに書き込むことができます (同じコンテナーに書き込むと、実質的に再帰的ループが作成されます)。 または、Azure Cosmos DB トリガーと出力バインディングを使用して、実質的に 1 つのコンテナー内の変更されたすべての項目を別のコンテナーに移行できます。
* Azure Cosmos DB の入力バインディングと出力バインディングは、同じ Azure Functions で使用できます。 この方法は、入力バインディングで特定のデータを検索し、Azure Functions で変更し、変更後に同じコンテナーまたは別のコンテナーに保存する場合に適しています。
* Azure Cosmos DB コンテナーへの入力バインディングは、Azure Cosmos DB トリガーと同じ関数で使用できます。また、出力バインディングの有無にかかわらず使用できます。 この組み合わせを使用すると、ショッピング カート サービスの新しい注文の変更フィードに最新の為替情報を適用できます (為替コンテナーに対する入力バインディングで取得します)。 最新の為替換算を適用して更新した後のショッピング カート合計は、出力バインディングを使用して 3 つ目のコンテナーに書き込むことができます。

> [!NOTE]
> この時点で、Azure Cosmos DB トリガー、入力バインディング、出力バインディングは、DocumentDB アカウント、Table アカウント、Graph API アカウントにのみ使用できます。

## <a name="use-cases"></a>ユース ケース

次のユース ケースでは、データをイベント ドリブンの Azure Functions に接続して Azure Cosmos DB データを最大限に活用する方法をいくつか紹介します。

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT のユース ケース - Azure Cosmos DB トリガーと出力バインディング

IoT 実装では、接続されている車のエンジンのチェック ランプが点灯したときに、関数を呼び出すことができます。

**実装:** Azure Cosmos DB トリガーと出力バインディングを使用する

1. **Azure Cosmos DB トリガー**を使用して、接続されている車のエンジンのチェック ランプの点灯など、車の警告に関連するイベントをトリガーします。
2. エンジンのチェック ランプが点灯すると、センサー データが Azure Cosmos DB に送信されます。
3. Azure Cosmos DB は新しいセンサー データ ドキュメントを作成または更新し、その変更を Azure Cosmos DB トリガーにストリームします。
4. トリガーは、センサー データ コレクションに対するデータの変更ごとに呼び出されます。また、変更が変更フィード経由でストリームされたときにも呼び出されます。
5. 関数でしきい値の条件を使用して、センサー データを保証部門に送信します。
6. 温度が特定の値を超えた場合も、警告が所有者に送信されます。
7. 関数に対する**出力バインディング**によって、別の Azure Cosmos DB コレクションの車の記録が更新され、エンジンのチェック イベントに関する情報が保存されます。

次の図は、このトリガーで Azure Portal で書き込まれるコードを示しています。

![Azure Portal で Azure Cosmos DB トリガーを作成する](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>財務ユース ケース - タイマー トリガーと入力バインディング

財務実装では、銀行口座の残高が一定額を下回ったときに関数を呼び出すことができます。

**実装:** タイマー トリガーと Azure Cosmos DB 入力バインディング

1. [タイマー トリガー](../azure-functions/functions-bindings-timer.md)と**入力バインディング**を使用して、Azure Cosmos DB コンテナーに格納されている銀行口座残高の情報を一定の間隔で取得できます。
2. ユーザーが設定した残高の下限しきい値を下回った場合、Azure Functions のアクションが実行されます。
3. 出力バインディングは、サービス アカウントから、低い残高の各口座に指定された電子メール アドレスに対して電子メールが送信される [SendGrid 統合](../azure-functions/functions-bindings-sendgrid.md)です。

次の図は、このシナリオ用の Azure Portal のコードを示しています。

![財務シナリオのタイマー トリガーの Index.js ファイル](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![財務シナリオのタイマー トリガーの Run.csx ファイル](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>ゲームのユース ケース - Azure Cosmos DB トリガーと出力バインディング

ゲームでは、新しいユーザーを作成するときに、[Azure Cosmos DB Graph API](graph-introduction.md) を使用して、知っている可能性のある他のユーザーを検索することができます。 簡単に取得できるように、結果を [Azure Cosmos DB Table データベース](table-introduction.md)に書き込むことができます。

**実装:** Azure Cosmos DB トリガーと出力バインディングを使用する

1. Azure Cosmos DB [グラフ データベース](graph-introduction.md)を使用してすべてのユーザーを格納することで、Azure Cosmos DB トリガーを使用して新しい関数を作成できます。 
2. 新しいユーザーが挿入されるたびに関数が呼び出され、結果は**出力バインディング**を使用して格納されます。
3. この関数は、グラフ データベースに対して、新しいユーザーに直接関連するすべてのユーザーを検索するクエリを実行し、そのデータセットを関数に返します。
4. このデータは、Azure Cosmos DB [テーブル データベース](table-introduction.md)にキーと値のペア セットとして格納されます。新規ユーザーに接続されている友人を表示する任意のフロントエンド アプリケーションから、このデータを簡単に取得できます。

### <a name="retail-use-case---multiple-functions"></a>小売のユース ケース - 複数の関数

小売の実装では、ユーザーが項目をバスケットに追加したときに、オプションのビジネス パイプライン コンポーネントの関数を柔軟に作成し、呼び出すことができるようになります。

**実装:** 1 つの接続をリッスンする複数の Azure Cosmos DB トリガー

1. Azure Cosmos DB トリガーを各 Azure Functions に追加して、複数の Azure Functions を作成できます。そのすべてがショッピング カート データの同じ変更フィードをリッスンします。 
2. 新しい項目がユーザーのショッピング カートに追加されるたびに、各関数はショッピング カート コンテナーの変更フィードから個別に呼び出されます。
    * 1 つの関数で現在のバスケットの内容を使用して、ユーザーが関心を持つ可能性がある他の項目の表示を変更することができます。
    * 別の関数で在庫の合計を更新できます。
    * また、別の関数は特定の製品に関する顧客情報をマーケティング部門に送信できます。マーケティング部門は宣伝メールを送信します。 

    任意の部門が変更フィードをリッスンして Azure Cosmos DB トリガーを作成し、プロセスの重要な注文処理イベントが遅れないようにすることができます。

これらのいずれのユース ケースでも、関数でアプリケーション自体が分離されるので、常に新しいアプリケーション インスタンスを起動する必要はありません。 その代わりに必要に応じて Azure Functions が個々の関数を起動して各プロセスを完了します。

## <a name="tooling"></a>ツール

Azure Portal では、Azure Cosmos DB と Azure Functions 間のネイティブ統合を使用できます。
* Azure Functions ポータルでは、Azure Cosmos DB トリガーを作成できます。 クイックスタートの手順については、[Azure Portal での Azure Cosmos DB トリガーの作成](https://aka.ms/cosmosdbtriggerportalfunc)に関するページを参照してください。![Azure Functions ポータルで Azure Cosmos DB トリガーを作成する](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* Azure Functions ポータルでは、Azure Cosmos DB 入力バインディングと出力バインディングを他の種類のトリガーに追加することもできます。 クイックスタートの手順については、「[Azure Functions と Cosmos DB を使用して非構造化データを格納する](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)」を参照してください。
    ![Azure Functions ポータルで Azure Cosmos DB トリガーを作成する](./media/serverless-computing-database/function-portal-input-binding.png)
*   Azure Cosmos DB ポータルで、Azure Cosmos DB トリガーを同じリソース グループ内の既存の Azure Functions アプリに追加できます。
    ![Azure Functions ポータルで Azure Cosmos DB トリガーを作成する](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>サーバーなしのコンピューティングに Azure Functions 統合を選択する理由

Azure Functions には、スケーラブルなユニットの作業や、オンデマンドで実行できるロジックの簡潔な部分を作成する機能があります。インフラストラクチャをプロビジョニングまたは管理する必要はありません。 Azure Functions を使用すると、Azure Cosmos DB データベースの変更に反応する本格的なアプリケーションを作成する必要はありません。特定のタスクのために小さな再利用可能な関数を作成できます。 また、HTTP 要求または適時のトリガーなどのイベントに応答して、Azure Functions への入力または出力として Azure Cosmos DB データを使用することもできます。

Azure Cosmos DB は、サーバーなしのコンピューティング アーキテクチャに推奨されるデータベースです。その理由は次のとおりです。

* **すべてのデータにすぐにアクセス**: Azure Cosmos DB の既定では、すべてのデータの[インデックスが自動的に作成され](indexing-policies.md)、それらのインデックスをすぐに使用できるため、格納されているすべての値に対するアクセス権を細かくすることができます。 つまり、データベースに対して新しい項目のクエリ、更新、追加をいつでも実行し、Azure Functions 経由ですぐにアクセスできます。

* **スキーマレス**。 Azure Cosmos DB はスキーマレスです。そのため、Azure Functions からすべてのデータ出力を一意に処理できます。 この "すべてを処理する" アプローチによって、すべてを Azure Cosmos DB に出力する多様な関数を簡単に作成できます。

* **スケーラブルなスループット**。 Azure Cosmos DB のスループットのスケール アップとスケール ダウンはすぐに行うことができます。 数百から数千単位の Functions のクエリがあり、同じコレクションに書き込む場合、負荷を処理する [RU/秒](request-units.md)をスケール アップできます。 すべての関数は、割り当てられた RU/秒を使用して並列処理できます。また、データの[整合性](consistency-levels.md)が保証されます。

* **グローバル レプリケーション** ユーザーのいる場所に最も近いデータの位置を特定することで、[世界中](distribute-data-globally.md)の Azure Cosmos DB データをレプリケートして待機時間を短縮できます。 すべての Azure Cosmos DB クエリと同様に、イベント ドリブン トリガーのデータは、ユーザーに最も近い Azure Cosmos DB から読み取られます。

Azure Functions と統合してデータを格納し、深いインデックス作成が必要ない場合、または添付ファイルとメディア ファイルを格納する必要がある場合、[Azure Blog Storage トリガー](../azure-functions/functions-bindings-storage-blob.md)が適している可能性があります。

Azure Functions の利点: 

* **イベント ドリブン**です。 Azure Functions はイベント ドリブンです。また、Azure Cosmos DB の変更フィードをリッスンできます。 つまり、リッスン ロジックを作成する必要はなく、リッスンしている変更のみに注目するだけで済みます。 

* **無制限**。 関数は並列して実行され、必要な数のサービスを起動できます。 また、必要に応じてパラメーターを設定します。

* **クイック タスクに適しています**。 イベントが発生するたびに、サービスは関数の新しいインスタンスを起動します。関数が完了すると、イベントは直ちに閉じられます。 ユーザーは、関数が実行された時間に対してだけ支払います。

Flow、Logic Apps、Azure Functions、または WebJobs が実装に適しているかどうかがわからない場合は、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

それでは実際に Azure Cosmos DB と Azure Functions を接続してみましょう。 

* [Azure Portal で Azure Cosmos DB トリガーを作成する](https://aka.ms/cosmosdbtriggerportalfunc)
* [Azure Cosmos DB 入力バインディングを使用して Azure Functions HTTP トリガーを作成する](https://aka.ms/cosmosdbinputbind)
* [Azure Functions と Cosmos DB を使用して非構造化データを格納する](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Azure Cosmos DB のバインディングとトリガー](../azure-functions/functions-bindings-documentdb.md)


 




