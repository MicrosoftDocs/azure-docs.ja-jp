---
title: Azure Stream Analytics イベント処理を使用したリアルタイム イベント処理
description: この記事では、Azure Stream Analytics を使用したリアルタイム イベント処理と分析を行うための参照アーキテクチャについて説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: ab1ca89427f332151120420c3c087902584706f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621780"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>参照アーキテクチャ:Microsoft Azure Stream Analytics によるリアルタイム イベント処理
Azure Stream Analytics を使用したリアルタイム イベント処理のための参照アーキテクチャの目的は、リアルタイムのサービスとしてのプラットフォーム (PaaS) のストリーム処理ソリューションを、 Microsoft Azure でデプロイするための一般的な青写真を提供することです。

## <a name="summary"></a>まとめ
これまで分析ソリューションは、ETL (抽出、変換、読み込み) やデータ ウェアハウジングなどの機能を基盤としており、分析前のデータが格納されていました。 データ受信速度の向上を含む要件の変化によって、この既存モデルが限界に達しました。 データを、格納される前のストリームで流れている間に分析する機能は 1 つのソリューションとなります。これは新しい機能でないですが、このようなアプローチはすべての業種で広く採用されることはありませんでした。 

Microsoft Azure は、さまざまなソリューションのシナリオと要件をサポートできる分析テクノロジの幅広い選択肢を提供します。 エンド ツー エンド ソリューション導入のためにデプロイする Azure サービスを選択するのは、サービスの幅広さを考えると困難な作業です。 このドキュメントの目的は、イベント ストリーミング ソリューションをサポートするさまざまな Azure サービスの機能および相互運用性について説明することです。 また、このようなアプローチによってユーザーがメリットを得られるシナリオもいくつか説明します。

## <a name="contents"></a>目次
* 概要
* リアルタイム分析の概要
* Azure でのリアルタイム データの価値提案
* リアルタイム分析の一般的なシナリオ
* アーキテクチャとコンポーネント
  * データ ソース
  * データ統合層
  * リアルタイム分析層
  * データ ストレージ 層
  * プレゼンテーション層 / 消費層
* まとめ

**執筆者:** Charles Feddersen、Microsoft Corporation の Data Insights Center of Excellence チームに所属するソリューション設計者

**公開:** 2015 年 1 月

**リビジョン:** 1.0

**ダウンロード:** [Microsoft Azure Stream Analytics によるリアルタイム イベント処理](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

