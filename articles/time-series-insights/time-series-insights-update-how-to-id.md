---
title: Azure Time Series Insights プレビューでタイム シリーズ ID を選択する場合のベスト プラクティス | Microsoft Docs
description: Azure Time Series Insights プレビューでタイム シリーズ ID を選択するときのベスト プラクティスについて説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 44c6c8a55bbcbf76cf48fd17a6e52d188a5e99fc
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556613"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>タイム シリーズ ID の選択に関するベスト プラクティス

この記事では、Azure Time Series Insights プレビューのパーティション キー、タイム シリーズ ID、および ID を選択するときのベスト プラクティスについて説明します。

## <a name="choose-a-time-series-id"></a>タイム シリーズ ID の選択

タイム シリーズ ID の選択は、データベースでのパーティション キーの選択のようなものです。 設計時に行う必要のある重要な決定です。 別のタイム シリーズ ID を使うように既存の Time Series Insights プレビュー環境を更新することはできません。 つまり、タイム シリーズ ID を使用して環境を作成すると、ポリシーは変更できない不変プロパティになります。

> [!IMPORTANT]
> タイム シリーズ ID は大文字と小文字が区別され、不変です (設定した後では変更できません)。

これを念頭に置いて、適切なタイム シリーズ ID を選択することが重要です。 タイム シリーズ ID を選択するときは、以下のベスト プラクティスに従ってください。
* 値が多岐にわたっていて、なおかつ均等なアクセス パターンを持ったプロパティ名を選択します。 多数 (たとえば数百から数千) の個別の値を備えたパーティション キーにすることをお勧めします。 多くの場合、これは JSON での DeviceID や SensorID のようなものになります。
* タイム シリーズ ID は、[タイム シリーズ モデル](./time-series-insights-update-tsm.md)のリーフ ノード レベルで一意である必要があります。
* タイム シリーズ ID プロパティの名前の文字列は最大 128 文字まで、タイム シリーズ ID プロパティの値は最大 1024 文字まで使用できます。
* 一意のタイム シリーズ ID プロパティの値が一部不足している場合、それらの値は null 値として扱われます。これは一意性制約に含まれているためです。

さらに、タイム シリーズ ID として最大 *3* 個のキー プロパティを選択できます。

  > [!NOTE]
  > *3* 個のキー プロパティは文字列である必要があります。

次のシナリオでは、タイム シリーズ ID として複数のキー プロパティを選択する場合について説明します。  

### <a name="scenario-1"></a>シナリオ 1

* それぞれが一意のキーを持つ資産の従来のフリートがあります。 
* たとえば、1 つのフリートはプロパティ *deviceId* によって一意に識別され、別のフリートの一意プロパティは *objectId* です。 どのフリートにも、他のフリートの一意プロパティは含まれません。 この例では、一意キーとして 2 つのキー deviceId と objectId を選択します。 
* null 値が受け付けられ、イベント ペイロードにプロパティが存在しない場合は `null` 値としてカウントされます。 またこれは、2 つの異なるイベント ソースにデータを送信し、各イベント ソースのデータが一意のタイム シリーズ ID を持っている場合の処理にも適した方法です。

### <a name="scenario-2"></a>シナリオ 2

* 資産の同じフリート内で複数のプロパティが一意である必要があります。 
* たとえばスマート ビルディングの製造元で、すべての部屋にセンサーを設置するものとします。 通常、各部屋の *sensorId* の値は同じです (*sensor1*、*sensor2*、*sensor3* など)。
* さらに、プロパティ *flrRm* ではサイト間でフロアと部屋の番号が重複しています (*1a*、*2b*、*3a* など)。
* 最後に、プロパティ *location* には、*Redmond*、*Barcelona*、*Tokyo* などの値が含まれます。 一意性を作成するには、タイム シリーズ ID のキーとして 3 つのプロパティ *sensorId*、*flrRm*、*location* を指定します。

## <a name="next-steps"></a>次の手順

* [データ モデリング](./time-series-insights-update-tsm.md)の詳細を確認します。
* [Azure Time Series Insights (プレビュー) の環境](./time-series-insights-update-plan.md)を計画します。
