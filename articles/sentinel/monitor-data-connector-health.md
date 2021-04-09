---
title: この Azure Sentinel ブックを使用してデータ コネクタの正常性を監視する | Microsoft Docs
description: 正常性監視ブックを使用して、データ コネクタの接続性とパフォーマンスを追跡します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656992"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>この Azure Sentinel ブックを使用してデータ コネクタの正常性を監視する

**データ コネクタの正常性監視ブック** を使用すると、Azure Sentinel 内からデータ コネクタの正常性、接続性、およびパフォーマンスを追跡できます。 このブックには追加のモニターが用意されており、異常を検出して、ワークスペースのデータ インジェストの状態に関する分析情報を得ることができます。 ブックのロジックを使用して、取り込まれたデータの全般的な正常性を監視したり、カスタム ビューやルールベースのアラートを作成したりすることができます。

## <a name="use-the-health-monitoring-workbook"></a>正常性監視ブックを使用する

1. Azure Sentinel ポータルの **[脅威管理]** メニューで、 **[ブック]** を選択します。

1. **[ブック]** ギャラリーで、検索バーに「*正常性*」と入力し、結果から **[データ収集の正常性の監視]** を選択します。

1. ブックをそのまま使用するには **[テンプレートの表示]** を選択し、ブックの編集可能なコピーを作成するには **[保存]** を選択します。 コピーが作成されたら、 **[保存されたブックの表示]** を選択します。

1. ブック内で、最初に表示したい **サブスクリプション** と **ワークスペース** を選択した後、必要に応じてデータをフィルター処理するための **TimeRange** を定義します。 **[ヘルプの表示]** トグルを使用すると、ブックの組み込みの説明が表示されます。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="データ コネクタの正常性監視ブックのランディング ページ" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

このブックには、次の 3 つのタブ付きセクションがあります。

1. **[概要**] タブには、選択したワークスペースでのデータ インジェストの全般的な状態 (ボリュームのメジャー、EPS 率、および最後のログ受信時刻) が表示されます。

1. **[データ収集の異常]** タブでは、テーブルとデータ ソースによってデータ収集プロセスの異常を検出できます。 各タブには、特定のテーブルの異常が表示されます ( **[全般]** タブには、テーブルのコレクションが含まれています)。 異常は、**異常スコア** を返す **series_decompose_anomalies()** 関数を使用して計算されます。 この関数については、[こちら](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx)を参照してください。 評価する関数に次のパラメーターを設定します。

    - **AnomaliesTimeRange**:この時刻の選択は、データ収集の異常ビューにのみ適用されます。
    - **SampleInterval**:指定された時間範囲内にデータがサンプリングされる時間間隔。 異常スコアは、最後の間隔のデータに対してのみ計算されます。
    - **PositiveAlertThreshold**:この値は、正の異常スコアのしきい値を定義します。 これは 10 進値を受け入れます。
    - **NegativeAlertThreshold**:この値は、負の異常スコアのしきい値を定義します。 これは 10 進値を受け入れます。

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="データ コネクタの正常性監視ブックの異常ページ" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. **[エージェントの情報]** タブには、さまざまなマシン (Azure VM、他のクラウド VM、オンプレミスの VM、または物理) にインストールされている Log Analytics エージェントの正常性に関する情報が表示されます。 以下のものを監視できます。

   - システムの場所

   - ハートビートの状態と待機時間

   - 使用可能なメモリとディスク領域

   - エージェントの操作

    このセクションでは、マシンの環境を説明するタブを選択する必要があります。Azure Arc で管理されているマシンのみを表示する場合は、 **[Azure で管理されているマシン]** タブを選択します。Log Analytics エージェントがインストールされている管理対象および Azure 以外のマシンの両方を表示するには、 **[すべてのマシン]** タブを選択します。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="データ コネクタの正常性監視ブックのエージェントの情報ページ" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>次のステップ
[Azure Sentinel へのデータのオンボード](quickstart-onboard.md)、[データ ソースの接続](connect-data-sources.md)、および[データや潜在的な脅威の視覚化](quickstart-get-visibility.md)の方法を確認します。