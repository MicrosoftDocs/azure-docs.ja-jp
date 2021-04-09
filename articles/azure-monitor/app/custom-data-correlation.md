---
title: Azure Application Insights | Microsoft Docs
description: Application Insights のデータを、データ エンリッチメントまたは参照テーブル、Application Insights 以外のデータ ソース、カスタム データなどの他のデータセットに関連付けます。
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 933280b5d3b81098f18f22a72bd2c7f942869e6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578321"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Application Insights のデータをカスタム データ ソースと関連付ける

Application Insights は、例外、トレース、ページ ビューなど、さまざまな種類のデータを収集します。 多くの場合、それだけでもアプリケーションのパフォーマンス、信頼性、使用状況を十分に調査できますが、その他のまったく独自のデータセットに対して、Application Insights に格納されたデータを関連付けることが必要になるケースはあります。

以下、カスタム データが必要になる状況の例をいくつか示します。

- データ エンリッチメントやルックアップ テーブル: たとえば、サーバー名を補う情報として、サーバーの所有者やそのサーバーがあるラボの場所を追加します。 
- Application Insights 以外のデータ ソースとの関連付け: たとえば Web ストアでの購入に関するデータと、購入フルフィルメント サービスからの情報とを関連付けることで、出荷予定時刻がどの程度正確であったかを調査します。 
- まったく独自のデータ: Application Insights を支えている Azure Monitor ログ プラットフォームのクエリ言語とパフォーマンスは、Microsoft の多くのお客様に支持されており、そうしたお客様が Application Insights とはまったく関係のないデータも照会したいと考えています。 たとえば、[こちら](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)に記載されているようなスマート ホーム導入の一環として、ソーラー パネルのパフォーマンスを追跡する用途が考えられます。

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>カスタム データを Application Insights のデータと関連付ける方法 

Application Insights は Azure Monitor の強力なログ プラットフォームによって支えられているため、データの取り込みには、Azure Monitor の機能をフル活用することができます。 さらに、このカスタム データを Azure Monitor ログからアクセスできるデータと関連付ける "join" 演算子を使ってクエリを記述することになります。 

## <a name="ingesting-data"></a>データの取り込み

このセクションでは、Azure Monitor ログにデータを取り込む方法について確認します。

Log Analytics ワークスペースがまだない場合は、[こちらの指示](../vm/quick-collect-azurevm.md) (「ワークスペースの作成」の手順を含む) に従って、新たにプロビジョニングします。

Azure Monitor へのログ データの送信を開始するには、 いくつかのオプションがあります。

- 同期メカニズムを使用する場合は、[データ コレクター API](../logs/data-collector-api.md) を直接呼び出すか、または Microsoft のロジック アプリ コネクタを使用できます。[Azure Log Analytics] から [データを送信する] オプションを選択してください。

  ![スクリーンショット (アクションを選択してください)](./media/custom-data-correlation/01-logic-app-connector.png)  

- 非同期オプションを使用する場合は、データ コレクター API を使って処理パイプラインを作成します。 詳細については、[こちらの記事](../logs/create-pipeline-datacollector-api.md)をご覧ください。

## <a name="correlating-data"></a>データの関連付け

Application Insights は、Azure Monitor のログ プラットフォームを基盤としています。 そのため、[リソースをまたいだ結合操作](../logs/cross-workspace-query.md)を使用して、Azure Monitor に取り込んだあらゆるデータを Application Insights のデータに関連付けることができます。

たとえば、"myLA" という Log Analytics ワークスペース内の "LabLocations_CL" というテーブルに、ラボのインベントリや場所を取り込むことができます。 その後、"myAI" という Application Insights アプリで追跡した要求を調査し、その要求を処理したマシン名と、前述したカスタム テーブルに格納されている対応するマシンの場所を関連付けたい場合は、Application Insights または Azure Monitor のコンテキストから次のクエリを実行します。

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>次の手順

- [データ コレクター API](../logs/data-collector-api.md) に関するリファレンスを確認します。
- [リソースをまたいだ結合操作](../logs/cross-workspace-query.md)の詳細を確認します。
