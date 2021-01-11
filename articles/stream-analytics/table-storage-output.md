---
title: Azure Stream Analytics からの Table Storage 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Table Storage について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0f8da31dbe59412ebd57c1f3523089062e5089a6
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875499"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Table Storage 出力

[Azure Table Storage](../storage/common/storage-introduction.md) は高度な可用性を備えた非常にスケーラブルなストレージであるため、アプリケーションを需要に応じて自動的に拡張できます。 Table Storage は Microsoft の NoSQL キー/属性ストアであり、スキーマに対する制約を抑えながら、構造化されたデータに使用できます。 Azure Table Storage を使用すると、永続化と効率的な取得のためにデータを保持できます。

次の表に、テーブルの出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこのテーブル ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント |ご自分の出力を送信するストレージ アカウントの名前です。 |
| ストレージ アカウント キー |ストレージ アカウントに関連付けられているアクセス キー。 |
| テーブル名 |テーブルの名前。 テーブルが存在しない場合は、テーブルが作成されます。 |
| パーティション キー |パーティション キーが含まれる出力列の名前です。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、テーブル内のパーティションの一意識別子です。 最大サイズが 1 KB の文字列値です。 |
| 行キー |行キーが含まれる出力列の名前です。 行キーは、パーティション内のエンティティの一意識別子です。 これにより、エンティティのプライマリ キーの 2 番目の部分が形成されます。 行キーは、最大サイズが 1 KB の文字列値です。 |
| バッチ サイズ |バッチ操作のレコードの数です。 ほとんどのジョブは既定値 (100) で十分です。 この設定の変更について詳しくは、[テーブル バッチ操作の仕様](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)に関するページを参照してください。 |

## <a name="partitioning"></a>パーティション分割

パーティション キーは任意の出力列です。 出力ライターの数は、[完全並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズについては、[Azure Storage の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)に関する記事を参照してください。 既定値は、1 つのトランザクションあたり 100 エンティティですが、必要に応じて、より小さい値に構成することができます。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-vs-code.md)
