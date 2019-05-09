---
title: Azure Cosmos DB の変更フィードを Azure Functions と共に使用する方法
description: Azure Cosmos DB の変更フィードを Azure Functions と共に使用します
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 93cd93b40c142d504c52f08f9005d082fb5a2a20
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469483"
---
# <a name="trigger-azure-functions-from-azure-cosmos-db"></a>Azure Cosmos DB から Azure Functions をトリガーする

Azure Functions を使用している場合、変更フィードに接続する最も簡単な方法は、[Azure Cosmos DB トリガー](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)を Azure Functions のアプリケーションに追加することです。 Azure Functions のアプリケーション内で Cosmos DB トリガーを作成するときに、接続先の Cosmos コンテナーを選択します。これで、コンテナーに変更が加えられるたびに関数がトリガーされます。

トリガーを作成するには、Azure Functions ポータル、Azure Cosmos DB ポータル、またはプログラムを使用します。 詳しくは、「[Azure Cosmos DB と Azure Functions を使用したサーバーレス データベース コンピューティング](serverless-computing-database.md)」をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>特定リージョンから読み取るように Azure 関数を構成するにはどうすればよいですか?

Azure Cosmos DB トリガーを使用してリージョンの一覧を指定する際に、[PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) を定義できます。 これは、トリガーによる読み取りの元が優先リージョンとなるように ConnectionPolicy をカスタマイズするのと同じことです。 Azure 関数がデプロイされている場所に最も近いリージョンから読み取るのが理想です。

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Azure 関数のバッチの既定サイズは何ですか?

既定のサイズは、Azure 関数の呼び出しごとに 100 項目です。 ただし、この値は function.json ファイルで構成できます。 こちらが[全構成オプションの一覧](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)です。 開発をローカルで行っている場合は、local.settings.json ファイル内のアプリケーション設定を更新してください。

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>コンテナーを監視し、その変更フィードを読み取っていますが、挿入されたすべてのドキュメントを取得できずに一部のドキュメントが欠落しています。

同一リース コンテナーの同一コンテナーが別の Azure 関数によって読み取り中でないことをご確認ください。 不足しているドキュメントは、同じリースを使用している他の Azure 関数によって処理されます。

したがって、同一の変更フィードを読み取る複数の Azure 関数を作成する場合は、それぞれに異なるリース コンテナーを使用させるか、"leasePrefix" 構成を使って同一コンテナーを共有させる必要があります。 ただし、変更フィード プロセッサ ライブラリを使用する場合は、Azure 関数の複数インスタンスを開始すれば、ドキュメントは SDK によって自動的にインスタンス間に分割されます。

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos 項目の更新間隔は 1 秒ですが、変更フィードをリッスンしている Azure 関数内ですべての変更を取得できていません。

Azure Functions では、既定の最大遅延時間を 5 秒間として、変更フィードに変更を継続的にポーリングします。 読み取り対象の保留の変更がない場合、またはトリガーの適用後に保留中の変更がある場合は、関数によってそれらがすぐに読み取られます。 一方、保留中の変更がない場合、関数は 5 秒間待機してから変更をさらにポーリングします。

ドキュメントで、新しい変更をポーリングするトリガーを取得したのと同じ間隔で複数の変更を受信する場合は、中間バージョンではなく最新バージョンのドキュメントを取得できます。

5 秒未満、たとえば毎秒の間隔で変更フィードをポーリングする場合は、ポーリング時間 "feedPollDelay" を構成できます。[構成全体](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations)をご覧ください。 単位はミリ秒で、既定値は 5000 です。 1 秒未満の間隔でポーリングすることもできますが、CPU メモリの使用量が増え始めるのでお勧めできません。

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>複数の Azure 関数が 1 つのコンテナーの変更フィードを読み取ることはできますか?

はい。 複数の Azure 関数が同一コンテナーの変更フィードを読み取ることができます。 ただし、Azure 関数には "leaseCollectionPrefix" を個別に定義する必要があります。

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>変更フィードの処理に Azure 関数を使用している場合、10 ドキュメントのバッチの 7 番目のドキュメントでエラーが発生します。 この場合、最後の 3 ドキュメントは処理されません。次回のフィードで失敗したドキュメント (7 番目のドキュメント) から処理を開始するにはどうすればよいですか?

このエラーへの対応でお勧めするパターンは、try-catch ブロックでコードをラップすることです。ドキュメントの一覧を繰り返し処理する場合は、独自の try-catch ブロックで各反復処理をラップします。 エラーをキャッチしてドキュメントをキュー (配達不能) に配置し、エラーが生じたドキュメントを処理するロジックを定義します。 この方法であれば、200 ドキュメントのバッチで 1 つのドキュメントのみが失敗する場合でも、バッチ全体を無駄にする必要はありません。

エラーが生じても、チェックポイントを先頭まで巻き戻さないでください。これらのドキュメントは、変更フィードから取得し続けることができます。 変更フィードは、ドキュメントの最終スナップショットを維持します。そのために、ドキュメントの以前のスナップショットを失っても問題はありません。 変更フィードは、ドキュメント最終バージョンのみを維持し、間に別の処理が行われると、ドキュメントは変更されます。

コードの修正を続けていると、配達不能キュー内のドキュメントがすぐになくなることに気が付くはずです。 Azure 関数は変更フィード システムによって自動的に呼び出され、チェックポイントは Azure 関数によって内部的に維持されます。 チェックポイントをロールバックしてすべての側面をコントロールするのであれば、変更フィード プロセッサ SDK の使用を検討してください。

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Azure Cosmos DB トリガーを使用するために追加コストはかかりますか?

Azure Cosmos DB トリガーでは、Change Feed Processor ライブラリを内部的に活用します。 そのため、状態と部分的なチェックポイントを管理するために、リース コレクションと呼ばれる追加のコレクションが必要です。 この状態管理は、動的にスケーリングできる必要があり、Azure 関数を停止して後で処理を続行したい場合に続行できる必要があります。 詳しくは、[変更フィード プロセッサ ライブラリの操作方法](change-feed-processor.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

以下の記事で、変更フィードに関してさらに詳しく知ることができます。

* [変更フィードの概要](change-feed.md)
* [変更フィードを読み取る方法](read-change-feed.md)
* [Change Feed Processor ライブラリの使用](change-feed-processor.md)
* [変更フィード プロセッサ ライブラリの操作方法](change-feed-processor.md)
* [Azure Cosmos DB と Azure Functions を使用したサーバーレス データベース コンピューティング](serverless-computing-database.md)
