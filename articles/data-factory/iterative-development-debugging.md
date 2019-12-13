---
title: Azure Data Factory での反復開発とデバッグ
description: Azure Portal で Data Factory パイプラインの開発とデバッグを反復して実行する方法について説明します。
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 3a771181f8f2785339cbc47e0a0234b9c4e39adc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926840"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory での反復開発とデバッグ

Azure Data Factory では、Data Factory パイプラインの開発とデバッグを反復して実行できます。

この機能の概要とデモンストレーションについては、以下の 8 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>反復デバッグ機能
パイプラインを作成し、パイプライン キャンバスの**デバッグ**機能を使用して、1 行もコードを記述せずにテストを実行できます。

![パイプライン キャンバスのデバッグ機能](media/iterative-development-debugging/iterative-development-image1.png)

テストの実行結果は、パイプライン キャンバスの**出力**ウィンドウに表示されます。

![パイプライン キャンバスの出力ウィンドウ](media/iterative-development-debugging/iterative-development-image2.png)

テストの実行が成功したら、パイプラインにさらにアクティビティを追加し、反復的な方法でデバッグを続行します。 テストの実行中に、実行を **[キャンセル]** することもできます。

![テストの実行をキャンセルする](media/iterative-development-debugging/iterative-development-image3.png)

テストの実行を行うときは、 **[デバッグ]** を選択する前に、Data Factory に変更を発行する必要はありません。 この機能は、Data Factory ワークフローを更新する前に、変更が期待どおりに動作することを確認する場合に便利です。

> [!IMPORTANT]
> **[デバッグ]** を選択すると、パイプラインが実際に実行されます。 そのため、たとえばパイプラインにコピー アクティビティが含まれていれば、テストの実行では、データがコピー元からコピー先にコピーされます。 その結果、デバッグ時のコピー アクティビティとその他のアクティビティでは、テスト フォルダーを使用することをお勧めします。 パイプラインのデバッグが終わったら、通常の操作で使用する実際のフォルダーに切り替えます。

## <a name="visualizing-debug-runs"></a>デバッグ実行の可視化

1 つの場所で、データ ファクトリの進行中のすべてのデバッグの実行を可視化することができます。 ページの右上隅にある **View debug runs (デバッグ実行を表示)** を選択します｡ この機能は、子パイプラインに対してマスター パイプラインにデバッグ実行を開始させていて､1 つのビューでアクティブなデバッグ実行のすべてを確認したい場合に便利です｡

![[View active debug runs] アイコンを選択](media/iterative-development-debugging/view-debug-runs-image1.png)

![アクティブなデバッグ実行のサンプル一覧](media/iterative-development-debugging/view-debug-runs-image2.png)

アクティブなデータ フロー デバッグ セッションがある場合、これらのセッションはアクティブなデバッグ ウィンドウの下部に表示されます。 アクティブなデータ フロー セッションを選択し、それぞれのクラスターを停止することができます。

![アクティブなデータ フロー デバッグ実行の一覧 (サンプル)](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>デバッグ実行の監視

**デバッグ**機能を使用して開始するテストの実行は、 **[モニター]** タブの一覧では利用できません。 **[モニター]** タブの **[Trigger now]\(今すぐトリガー\)** 、 **[スケジュール]** 、または **[タンブリング ウィンドウ]** トリガーでトリガーされた実行のみを確認できます。パイプライン キャンバスの**出力**ウィンドウで、**デバッグ**機能を使用して開始された最後のテストの実行を確認できます。

## <a name="setting-breakpoints-for-debugging"></a>デバッグ用のブレークポイントの設定

Data Factory では、パイプライン キャンバスの特定のアクティビティに到達するまでデバッグすることもできます。 そこまでテストするアクティビティにブレークポイントを設定し、 **[デバッグ]** を選択します。 Data Factory は、パイプライン キャンバスでブレークポイント アクティビティに達するまで、テストが実行されることを保証します。 この*特定の場所までデバッグする*機能は、パイプライン全体ではなく、パイプライン内のアクティビティのサブセットのみをテストする場合に便利です。

![パイプライン キャンバス上のブレークポイント](media/iterative-development-debugging/iterative-development-image4.png)

ブレークポイントを設定するには、パイプライン キャンバス上で要素を選択します。 *[Debug Until]\(特定の場所までデバッグする\)* オプションは、要素の右上隅に空の赤い円として表示されます。

![選択した要素にブレークポイントを設定する前](media/iterative-development-debugging/iterative-development-image5.png)

*[Debug Until]\(特定の場所までデバッグする\)* オプションを選択した後は、赤い円が塗りつぶされ、ブレークポイントが有効になったことが示されます。

![選択した要素にブレークポイントを設定した後](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>次の手順
[Azure Data Factory における継続的インテグレーションとデプロイ](continuous-integration-deployment.md)
