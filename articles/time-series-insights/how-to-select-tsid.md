---
title: 時系列 ID の選択のベスト プラクティス - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 で時系列 ID を選択する場合のベスト プラクティスについて説明します。
author: shipramishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: seodec18
ms.openlocfilehash: 94abdf8735fa487f46d423f17f7e1ff7bc853eb7
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289873"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>タイム シリーズ ID の選択に関するベスト プラクティス

この記事では、Azure Time Series Insights Gen2 環境での時系列 ID の重要性と、それを選択するためのベスト プラクティスをまとめています。

## <a name="choose-a-time-series-id"></a>タイム シリーズ ID の選択

適切な時系列 ID を選択することが重要です。 タイム シリーズ ID の選択は、データベースでのパーティション キーの選択のようなものです。 これは、Azure Time Series Insights Gen2 環境を作成するときに必要です。 

> [!IMPORTANT]
> 時系列 ID：
>
> * *大文字と小文字を区別する* プロパティ： 大文字と小文字の区別は、検索、比較、更新、およびパーティション化で使用されます。
> * *変更できません* プロパティ： 一度作成した後は変更できません。

> [!TIP]
> イベント ソースが IoT ハブの場合、時系列 ID は、***iothub-connection-device-id*** になる可能性があります。

従うべき主なベストプラクティスは、次のとおりです：

* 多数 (たとえば数百から数千) の個別の値を備えたパーティション キーを選択します。 多くの場合、これは JSON のデバイス ID、センサー ID、またはタグ ID である可能性があります。
* タイム シリーズ ID は、[タイム シリーズ モデル](./concepts-model-overview.md)のリーフ ノード レベルで一意である必要があります。
* 時系列 ID のプロパティ名文字列の文字制限は、128 です。 時系列 ID のプロパティ値の場合、文字数の制限は 1024 です。
* 時系列 ID の一意のプロパティ値がない場合は、null 値として扱われ、一意性制約と同じルールに従います。
* 時系列 ID が複雑な JSON オブジェクト内で入れ子になっている場合、プロパティ名を指定するとき、イングレス [フラット化ルール](./concepts-json-flattening-escaping-rules.md)に従います。 例 [B](concepts-json-flattening-escaping-rules.md#example-b) を確認してください。 
* 時系列 ID として最大 *3* 個のキー プロパティも選択できます。 これらの組み合わせは、時系列 ID を表す複合キーになります。  
  > [!NOTE]
  > 3 個のキー プロパティは文字列である必要があります。
  > 一度に 1 つのプロパティではなく、この複合キーに対してクエリを実行する必要があります。

## <a name="select-more-than-one-key-property"></a>複数のキープロパティを選択する

次のシナリオでは、時系列 ID として複数のキー プロパティを選択する場合について説明します。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>例 1:一意のキーを持つ時系列 ID

* 従来の保有機材の資産があります。 それぞれには一意のキーがあります。
* 1 つの車両は、プロパティ **deviceId** によって一意に識別されます。 別の車両では、一意のプロパティは **objectId** です。 どの車両にも、他の車両の一意プロパティは含まれません。 この例では、一意キーとして 2 つのキー **deviceId** と **objectId** を選択します。
* null 値が受け付けられ、イベント ペイロードにプロパティが存在しない場合は null 値としてカウントされます。 またこれは、2 つのイベント ソースにデータを送信し、各イベント ソースのデータが一意の時系列 ID を持っている場合の処理にも適した方法です。

### <a name="example-2-time-series-id-with-a-composite-key"></a>例 2:複合キーを持つ時系列 ID

* 資産の同じフリート内で複数のプロパティが一意である必要があります。
* あなたはスマート ビルディングのメーカーで、すべての部屋にセンサーをデプロイしています。 通常、各部屋の **sensorId** の値は同じです。 例として、**sensor1**、**sensor2**、および **sensor3** となります。
* 建物には、プロパティ **flrRm** のサイト間で、重複しているフロア番号と部屋番号があります。 これらの数値には、**1a**、**2b**、**3a** などの値があります。
* プロパティ **location** には、**Redmond**、**Barcelona**、**Tokyo** などの値が含まれています。 一意性を作成するには、時系列 ID のキーとして、3 つのプロパティ **sensorId**、**flrRm**、**location** を指定します。

未加工イベントの例:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure portal では、次のようにして、複合キーを入力できます：
 
[![環境に合わせてタイム シリーズ ID を構成します。](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > Azure portal では、複数のプロパティ名をコンマで区切って 1 つのテキストボックスに入力しないでください。このように入力すると、コンマが含まれる単一のプロパティ名として処理されます。
  > 各プロパティ名を独自のテキストボックスに入力してください。

## <a name="next-steps"></a>次のステップ

* [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。

* [Azure Time Series Insights Gen2 環境](./time-series-insights-update-plan.md)を計画します。
