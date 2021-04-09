---
title: Azure Stream Analytics ジョブを開始する方法
description: この記事では、Azure portal、PowerShell、Visual Studio から Stream Analytics ジョブを開始する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016254"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブを開始する方法

Azure portal、Visual Studio、PowerShell を使用して、Azure Stream Analytics ジョブを開始できます。 ジョブを開始するときは、ジョブが出力の作成を開始する時刻を選択します。 Azure portal、Visual Studio、PowerShell は、開始時刻を設定する方法がそれぞれ異なります。 それらの方法について以下に説明します。

## <a name="start-options"></a>開始オプション
ジョブを開始するには、次の 3 つのオプションを使用できます。 以下の説明で言及されている時刻は、すべて [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) に指定される時刻である点に注意してください。 TIMESTAMP BY の指定がない場合は、到着時刻が使用されます。
* **[今すぐ]** : 出力イベント ストリームの開始点をジョブの開始時刻と同じにします。 時間演算子 (時間枠、LAG、JOIN など) が使用された場合、Azure Stream Analytics では、入力ソース内のデータが自動的にルックバックされます。 たとえば、ジョブを [今すぐ] で開始したときに、クエリで 5 分間のタンブリング ウィンドウが使用される場合、Azure Stream Analytics では、入力内の 5 分前のデータがシークされます。
起こり得る最初の出力イベントのタイムスタンプは現在時刻と同じであるかそれよりも後の時刻になり、論理的に出力を生成するために使用できる可能性があるすべての入力イベントが ASA によって確実に処理されます。 たとえば、部分的なウィンドウ集計は生成されません。 それは常に完全な集計値になります。

* **[カスタム]** : 出力の開始点を選択できます。 **[今すぐ]** オプションと同じように、Azure Stream Analytics では、時間演算子が使用された場合は、この時刻より前のデータが自動的に読み取られます。 

* **最終停止時刻**。 このオプションは、以前にジョブが開始されたが、手動で停止されたか失敗した場合に使用できます。 このオプションを選択すると、Azure Stream Analytics では、データが失われないように、最後の出力時刻を使用してジョブが再開されます。 前のオプションと同じように、Azure Stream Analytics では、時間演算子が使用された場合は、この時刻より前のデータが自動的に読み取られます。 複数の入力パーティションで時刻が異なっている可能性があるため、すべてのパーティションの中で最も早い停止時刻が使用され、その結果、出力内で多少の重複が発生する場合があります。 正確に 1 回のみの処理の詳細については、「[Event Delivery Guarantees (イベント配信の保証)](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)」を参照してください。


## <a name="azure-portal"></a>Azure portal

Azure portal でジョブに移動し、概要ページで **[開始]** を選択します。 **ジョブ出力の開始時刻** を選択し、**[開始]** を選択します。

**[ジョブ出力の開始時刻]** のいずれかのオプションを選択します。 オプションは、*[今すぐ]*、*[カスタム]*、および *[最終停止時刻]* です。 これらのオプションの詳細については、上記を参照してください。

## <a name="visual-studio"></a>Visual Studio

ジョブ ビューで、緑色の矢印ボタンを選択してジョブを開始します。 **[ジョブ出力の開始モード]** を設定し、**[開始]** を選択します。 ジョブの状態が **[実行中]** に変わります。

**[ジョブ出力の開始モード]** には、*JobStartTime*、*CustomTime*、*LastOutputEventTime* の 3 つのオプションがあります。 このプロパティが指定されていない場合、既定値は *JobStartTime* です。 これらのオプションの詳細については、上記を参照してください。


## <a name="powershell"></a>PowerShell

PowerShell を使用してジョブを開始するには、次のコマンドレットを使用します。

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode** には、*JobStartTime*、*CustomTime*、*LastOutputEventTime* の 3 つのオプションがあります。 このプロパティが指定されていない場合、既定値は *JobStartTime* です。 これらのオプションの詳細については、上記を参照してください。

`Start-AzStreamAnalyitcsJob` コマンドレットの詳細については、[Start-AzStreamAnalyticsJob リファレンス](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート: Visual Studio の Azure Stream Analytics ツールを使用した Stream Analytics ジョブの作成](stream-analytics-quick-create-vs.md)