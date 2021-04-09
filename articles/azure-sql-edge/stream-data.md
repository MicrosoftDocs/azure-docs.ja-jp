---
title: Azure SQL Edge でのデータ ストリーミング
description: Azure SQL Edge でのデータ ストリーミングの詳細について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392114"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Azure SQL Edge でのデータ ストリーミング

Azure SQL Edge には、T-SQL ストリーミングと呼ばれるデータストリーミング機能のネイティブ実装が用意されています。 これにはリアルタイムのデータ ストリーミング、分析、イベント処理機能があり、複数のソースからの大量の高速ストリーミング データを同時に分析および処理することができます。 T-SQL ストリーミングは、Microsoft Azure の [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) を強化しているものと同じハイパフォーマンス ストリーミング エンジンを使用して構築されています。 この機能は、エッジ上で実行されている Azure Stream Analytics によって提供されるものと同様の機能セットをサポートしています。

T-SQL ストリーミングを使用すると、Stream Analytics と同様に、デバイス、センサー、アプリケーションなどのさまざまな IoT 入力ソースから抽出された情報に含まれるパターンやリレーションシップを認識できます。 これらのパターンを使用してアクションをトリガーし、ワークフローを開始できます。 たとえば、アラートの作成、レポートまたは視覚化ソリューションへの情報のフィード、後で使用するためのデータの保存を行うことができます。 

T-SQL ストリーミングは次の場合に役立ちます。

* IoT デバイスからのリアルタイムのテレメトリ ストリームの分析。
* 無人の自動運転車両から生成されたデータのリアルタイム分析の使用。
* 高価な産業資産や製造資産のリモート監視と予測メンテナンスの使用。
* 農園や発電所での IoT センサーの読み取り値の異常検出やパターン認識の使用。

## <a name="how-does-t-sql-streaming-work"></a>T-SQL ストリーミングのしくみ

T-SQL ストリーミングは、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work) とまったく同じ方法で動作します。 たとえば、リアルタイム データ ストリーミングを処理するために、"*ストリーミング ジョブ*" の概念が使用されます。 

ストリーム分析ジョブは次のもので構成されます。

- **ストリーム入力**:データ ストリームを読み取るデータ ソースへの接続が定義されます。 現在、Azure SQL Edge では次の種類のストリーム入力がサポートされています。
    - Edge ハブ
    - Kafka (現在、Kafka 入力のサポートは Intel/AMD64 バージョンの Azure SQL Edge でのみ利用可能です)。

- **ストリーム出力**:データ ストリームを書き込むデータ ソースへの接続が定義されます。 現在、Azure SQL Edge では次の種類のストリーム出力がサポートされています。
    - Edge ハブ
    - SQL (SQL 出力には、Azure SQL Edge のインスタンス内のローカル データベースか、リモートの SQL サーバーまたは Azure SQL Database を指定できます)。 

- **ストリーム クエリ**:入力ストリームがストリーム出力に書き込まれる前に適用される変換、集計、フィルター、並べ替え、結合が定義されます。 ストリーム クエリは、Stream Analytics で使用されているものと同じクエリ言語に基づいています。 詳細については、[Stream Analytics クエリ言語](/stream-analytics-query/stream-analytics-query-language-reference)に関するページを参照してください。

> [!IMPORTANT]
> Stream Analytics とは異なり、現在、T-SQL ストリーミングでは[ルックアップでの参照データの使用](../stream-analytics/stream-analytics-use-reference-data.md)や[ストリーム ジョブでの UDF および UDA の使用](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c)はサポートされていません。

> [!NOTE]
> T-SQL ストリーミングでは、Stream Analytics でサポートされている言語サーフェス領域のサブセットのみがサポートされます。 詳細については、[Stream Analytics クエリ言語](/stream-analytics-query/stream-analytics-query-language-reference)に関するページを参照してください。

## <a name="limitations-and-restrictions"></a>制限事項と制約事項

T-SQL ストリーミングには、次の制限事項と制約事項が適用されます。 

- どの時点でも、アクティブにできるストリーミング ジョブは 1 つだけです。 既に実行中のジョブは、別のジョブを開始する前に停止する必要があります。
- 各ストリーミング ジョブの実行はシングルスレッドです。 ストリーミング ジョブに複数のクエリが含まれている場合、各クエリは直列の順序で評価されます。
- Azure SQL Edge でストリーミング ジョブを停止した場合、次のストリーミング ジョブを開始できるようになるまでに、遅延が発生する場合があります。 この遅延が導入されている理由は、ジョブの停止要求に応答して基になるストリーミング プロセスを停止し、ジョブの開始要求に応答して再開する必要があるためです。 
- Kafka ストリームの最大 32 パーティションの T-SQL ストリーミング。 パーティション数を増やして構成しようとすると、エラーが発生します。 

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge での Stream Analytics ジョブの作成](create-stream-analytics-job.md)
- [Azure SQL Edge でのストリーム ジョブに関連付けられたメタデータの表示](streaming-catalog-views.md)
- [外部ストリームの作成](create-external-stream-transact-sql.md)