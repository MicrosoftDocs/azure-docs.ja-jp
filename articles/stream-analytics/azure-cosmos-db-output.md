---
title: Azure Stream Analytics からの Azure Cosmos DB 出力
description: この記事では、Azure Stream Analytics から Azure Cosmos DB にデータを出力する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016579"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure Cosmos DB 出力

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) はグローバル分散型データベース サービスです。世界各地に対応する、制限のないエラスティックなスケーリング、スキーマに依存しないデータ モデルでの豊富なクエリと自動インデックス作成機能を提供します。 Stream Analytics の Azure Cosmos DB コンテナー オプションについては、[Azure Cosmos DB を出力として使用する Stream Analytics](stream-analytics-documentdb-output.md) に関する記事を参照してください。

Stream Analytics からの Azure Cosmos DB 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

> [!Note]
> Azure Stream Analytics は、SQL API を使用した Azure Cosmos DB への接続のみをサポートしています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Azure Stream Analytics を、その他のAPI で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。

次の表では、Azure Cosmos DB の出力を作成するプロパティについて説明します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | Stream Analytics クエリ内でこの出力を意味するエイリアス。 |
| シンク | Azure Cosmos DB。 |
| インポート オプション | **[サブスクリプションから Cosmos DB を選択する]** または **[Cosmos DB 設定を手動で指定します]** を選択します。
| Account ID | Azure Cosmos DB アカウントの名前またはエンドポイント URI。 |
| アカウント キー | Azure Cosmos DB アカウントの共有アクセス キー。 |
| データベース | Azure Cosmos DB データベース名。 |
| コンテナー名 | 使用するコンテナーの名前。Cosmos DB に存在する必要があります。 例:  <br /><ul><li> _MyContainer_: "MyContainer" という名前のコンテナーが存在する必要があります。</li>|
| ドキュメント ID |省略可能。 挿入操作または更新操作の基盤となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前です。

## <a name="partitioning"></a>パーティション分割

パーティション キーは、クエリの PARTITION BY 句に基づきます。 出力ライターの数は、[完全並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 Stream Analytics によって、Cosmos DB 出力パーティション キーが文字列に変換されます。 たとえば、bigint 型の値が 1 のパーティション キーがある場合、string 型の "1" に変換されます。 この変換は、パーティションのプロパティが Cosmos DB に書き込まれるかどうかにかかわらず、常に発生します。

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズについては、「[Azure Cosmos DB の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)」を参照してください。 バッチ サイズと書き込みの頻度は、Azure Cosmos DB の応答に基づいて動的に調整されます。 Stream Analytics からの事前に定義された制限はありません。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
