---
title: Metrics Advisor のメトリックのグラフ
titleSuffix: Azure Cognitive Services
description: メトリックのグラフを構成し、データ内の関連する異常を視覚化する方法です。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043155"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>方法: メトリックのグラフを作成して関連するメトリックを分析する

Metrics Advisor の各メトリックは、履歴データから学習して将来の傾向を予測するモデルによって、個別に監視されます。 各メトリックには、それぞれ別のモデルが適用されます。 ただし、場合によっては、複数のメトリックが相互に関連しており、複数のメトリックにわたる異常の分析が必要となります。 これには、**Metrics Graph** が役に立ちます。 

たとえば、個別のメトリックに異なるテレメトリのストリームがある場合、Metrics Advisor ではそれらが個別に監視されます。 あるメトリックの異常が原因で他のメトリックに異常が発生した場合は、インシデントに対処する場合に、それらの関係や、データ内にある根本原因を検出することが有益です。 メトリックのグラフを使用すると、検出された異常のビジュアル トポロジ グラフを作成できます。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>最初のノードをグラフに配置するためのメトリックを選択します。

ナビゲーション バーの **[Metrics Graph]** タブをクリックします。 メトリックのグラフを作成するための最初の手順は、ノードをグラフに配置することです。 ページの上部でデータ フィードとメトリックを選択します。 ノードが下部のパネルに表示されます。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="メトリックを選択する":::

## <a name="add-a-noderelation-on-existing-node"></a>既存のノードにノードと関係を追加する

次に、別のノードを追加して、既存のノードとの関係を指定する必要があります。 既存のノードを選択して、右クリックします。 コンテキスト メニューに、いくつかのオプションが表示されます。 

**[Add relation]\(関係の追加\)** をクリックすると、別のメトリックを選択して、2 つのノード間の関係の種類を指定できます。 また、特定のディメンション フィルターを適用することもできます。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="ノードと関係を追加します":::

上記の手順を繰り返すと、関連するすべてのメトリック間の関係を示すメトリックのグラフが作成されます。
**ノードの色に関するヒント**
> [!TIP]
> - メトリックとディメンションのフィルターを選択すると、それと同じメトリックとディメンションのフィルターが含まれる、グラフ内のノードがすべて **<font color=blue>青色</font>** に色付けされます。
> - グラフ内のメトリックを表す未選択のノードは、 **<font color=green>緑色</font>** に色付けされます。
> - 現在のメトリックに異常が見られた場合、ノードは **<font color=red>赤色</font>** に色付けされます。

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>関連するメトリックの異常状態をインシデント ハブに表示する

メトリックのグラフが作成され、そのグラフ内のメトリックに異常が検出された場合は、関連する異常ステータスを表示したり、インシデントの概要を把握したりすることができます。 

グラフ内のインシデントをクリックし、下にスクロールして、診断情報の下にある **[cross metrics analysis]\(クロス メトリック分析\)** に移動します。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="関連するメトリックと異常を表示します":::

## <a name="next-steps"></a>次のステップ

- [フィードバックを使用して異常検出を調整する](anomaly-feedback.md)
- [インシデントを診断する](diagnose-incident.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
