---
title: Azure IoT Central でルールとアクションを構成する | Microsoft Docs
description: このハウツー記事では、作成者として Azure IoT Central アプリケーションでテレメトリベースのルールとアクションを構成する方法について説明します。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 987e8d9e667339d7526e8acbc3164e58abcd3e4d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895744"
---
# <a name="configure-rules-preview-features"></a>ルールを構成する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*この記事は、オペレーター、ビルダー、および管理者に適用されます。*

IoT Central のルールは、接続されているデバイスからアクティブに監視されているイベントでトリガーする、カスタマイズ可能な応答ツールとして機能します。 以下のセクションで、ルールの評価方法について説明します。

## <a name="select-target-devices"></a>ターゲット デバイスの選択

このルールが適用されるデバイスの種類を選択するには、[ターゲット デバイス] セクションを使用します。 フィルターを使用すると、対象デバイスをさらに絞り込むことができます。 フィルターは、デバイス テンプレートのプロパティを使用して、デバイスのセットを絞り込みます。 フィルター自体はアクションをトリガーしません。 次のスクリーンショットでは、対象に指定されようとしているデバイスは、デバイス テンプレートの種類が **[冷蔵庫]** であるデバイスです。 フィルターで、 **[Manufactured State]\(製造された州\)** プロパティが **[ワシントン]** と等しい **[冷蔵庫]** のみをルールの対象とするように指定しています。

![条件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>複数の条件を使用する

条件とは、ルールがトリガーされる基になるものです。 現在、複数の条件をルールに追加すると、それらの条件は論理的に AND 処理されます。 言い換えると、ルールが true と評価されるには、すべての条件が満たされている必要があります。  

次のスクリーンショットでは、温度が 90 度より高く、かつ湿度が 10 未満になるタイミングを条件で確認します。 これらのステートメントが両方とも true である場合、ルールの評価結果は true になり、アクションがトリガーされます。

![条件](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>集計ウィンドウを使用する

ルールは、集計時間枠をタンブリング ウィンドウとして評価します。 次のスクリーンショットでは、時間枠は 5 分です。 5 分ごとに、このルールは最後の 5 分間のデータを評価します。 データは、対応するウィンドウ内で 1 回だけ評価されます。

![タンブリング ウィンドウ](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge モジュールでのルールの使用

IoT Edge モジュールに適用されるルールには制限が適用されます。 モジュールが異なるテレメトリに基づくルールは、有効なルールとして評価されません。 次の例で考えてみましょう。 ルールの最初の条件はモジュール A の温度テレメトリに基づくもので、ルールの 2 番目の条件はモジュール B の湿度テレメトリに基づくものであるとします。この場合、2 つの条件はモジュールが異なっているため、無効な条件のセットになります。 このルールは有効ではないため、ルールを保存しようとするとエラーがスローされます。

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでルールを構成する方法を習得したので、次を実行できます。

> [!div class="nextstepaction"]
> [データをすばやく分析する](howto-create-analytics.md)
