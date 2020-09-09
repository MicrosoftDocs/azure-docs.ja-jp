---
title: Azure Stream Analytics からの Azure Synapse Analytics 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Synapse Analytics について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 5d281bf33542354e2904805deed2f1deaf2725a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875500"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics-preview"></a>Azure Stream Analytics からの Azure Synapse Analytics 出力 (プレビュー)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (旧称 SQL Data Warehouse) は、エンタープライズ データ ウェアハウスとビッグ データ分析の機能を同時に備えた無制限の分析サービスです。 

Azure Stream Analytics ジョブで、Azure Synapse Analytics 内の SQL プール テーブルに出力でき、最大 200 MB/秒のスループット レートを処理できます。これにより、レポートやダッシュボードなどのワークロードに対して、最も要求の厳しいリアルタイム分析とホットパス データ処理がサポートされます。  

Stream Analytics ジョブに SQL プール テーブルを出力として追加する前に、それが存在している必要があります。 テーブルのスキーマを、使用するジョブの出力内のフィールドとその型と一致させる必要があります。 

Azure Synapse を出力として使用するには、ストレージ アカウントが構成されていることを確認する必要があります。 [ストレージ アカウントの設定] に移動し、ストレージ アカウントを構成します。 テーブルをサポートする次のストレージ アカウントの種類のみを使用できます: 汎用 V2 と汎用 V1。 Standard レベルのみ選択してください。 Premium レベルはサポートされていません。

## <a name="output-configuration"></a>出力の構成

次の表に、Azure Synapse Analytics 出力を作成するためのプロパティの名前とその説明を示します。

|プロパティ名|説明|
|-|-|
|出力エイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
|データベース |出力を送信する SQL プールの名前。 |
|サーバー名 |Azure Synapse サーバーの名前。  |
|ユーザー名 |データベースに書き込むためのアクセス権が割り当てられているユーザー名です。 Stream Analytics では、SQL 認証のみがサポートされます。 |
|Password |データベースに接続するためのパスワード。 |
|テーブル  | 出力の書き込み先のテーブル名です。 テーブル名は、大文字と小文字が区別されます。 このテーブルのスキーマは、ご自分のジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。|

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-vs-code.md)