---
title: Azure Stream Analytics からの Table Storage 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Table Storage について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013891"
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
| バッチ サイズ |バッチ操作のレコードの数です。 ほとんどのジョブは既定値 (100) で十分です。 この設定の変更について詳しくは、[テーブル バッチ操作の仕様](/java/api/com.microsoft.azure.storage.table.tablebatchoperation)に関するページを参照してください。 |

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
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)