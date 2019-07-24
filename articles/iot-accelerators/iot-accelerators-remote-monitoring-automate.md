---
title: リモート監視ソリューションでデバイスの問題を検出するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、ルールとアクションを使用して、リモート監視ソリューションでしきい値に基づくデバイスの問題を自動的に検出する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 91ee5087e5f41cda3648c2ecadcfcf16fd32a249
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598683"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>チュートリアル:監視ソリューションに接続されているデバイスの問題の検出する

このチュートリアルでは、リモート監視ソリューション アクセラレータを構成して、接続されている IoT デバイスの問題を検出します。 デバイスの問題を検出するには、ソリューションのダッシュボードでアラートを生成するルールを追加します。

このチュートリアルでは、シミュレートされた冷却装置を使用して、ルールとアラートを紹介します。 冷却装置は Contoso という組織によって管理され、リモート監視ソリューション アクセラレータに接続されています。 Contoso には、冷却装置の圧力が 298 PSI を超える場合に重大アラートを生成するルールが既に存在します。 Contoso の運用者として、初期の圧力の急上昇を探すことで、問題のあるセンサーを搭載している可能性のある冷却装置を特定する必要があります。 このような装置を特定するために、冷却装置の圧力が 150 PSI を超えたときに警告のアラートを生成するルールを追加します。

また、直近の 5 分間にわたって冷却装置の平均湿度が 80% を超え、温度が華氏 75 度を超えたときに重大なアラートを生成するルールを作成するよう依頼を受けています。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * ソリューション内のルールを表示する
> * 規則を作成する
> * 複数の条件でルールを作成する
> * 既存のルールを編集する
> * ルールのオンとオフを切り替える

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>既存のルールを確認する

ソリューション アクセラレータの **[ルール]** ページには、現在のすべてのルールの一覧が表示されます。

[![[ルール] ページ](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

冷却装置に適用されるルールだけを表示するには、フィルターを適用します。 一覧でルールを選択すると、詳細情報の表示と編集ができます。

[![ルールの詳細を表示する](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>規則を作成する

冷却装置の圧力が 150 PSI を超えるときに警告を生成する新しいルールを作成するには、**[新しいルール]** をクリックします。 次の値を使用してルールを作成します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 規則の名前        | Chiller 警告                       |
| 説明      | Chiller の圧力が 150 PSI を超えました |
| デバイス グループ     | **Chillers** デバイス グループ             |
| 計算      | すぐに                               |
| 条件 1 フィールド| pressure                              |
| 条件 1 演算子 | より大きい                      |
| 条件 1 値    | 150                               |
| 重大度レベル  | 警告                               |

[![警告ルールを作成する](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

新しいルールを保存するには、**[適用]** をクリックします。

**[ルール]** ページまたは、**[ダッシュボード]** ページでルールがトリガーされた時間を確認できます。

[![警告ルールがトリガーされた時間](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>高度なルールを作成する

直近の 5 分間にわたって冷却装置の平均湿度が 80% を超え、温度が華氏 75 度を超えたときに重大なアラートを生成する、複数の条件を持つルールを作成するには、**[新しいルール]** をクリックします。 次の値を使用してルールを作成します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 規則の名前        | Chiller の湿度と温度が危険    |
| 説明      | 湿度と温度が危険です |
| デバイス グループ     | **Chillers** デバイス グループ             |
| 計算      | 平均                               |
| 期間      | 5                                     |
| 条件 1 フィールド| 湿度                              |
| 条件 1 演算子 | より大きい                      |
| 条件 1 値    | 80                                |
| 重大度レベル  | 重大                              |

[![複数の条件を持つルールを作成する: パート 1](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

2 つ目の条件を追加するには、[+ 条件の追加] をクリックします。 新しい条件で次の値を使用します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 条件 2 フィールド| 温度                           |
| 条件 2 演算子 | より大きい                      |
| 条件 2 値    | 75                                |

[![複数の条件を持つルールを作成する: パート 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

新しいルールを保存するには、**[適用]** をクリックします。

**[ルール]** ページまたは、**[ダッシュボード]** ページでルールがトリガーされた時間を確認できます。

[![複数の条件を持つルールがトリガーされた時間](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>既存のルールを編集する

既存のルールを変更するには、ルールの一覧から対象のルールを選択し、**[編集]** をクリックします。

[![ルールを編集する](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>ルールを無効にする

一時的にルールをオフに切り替えるには、ルールの一覧で無効にできます。 無効にするルールを選択し、**[無効にする]** を選択します。 一覧のルールの **[ステータス]** が、ルールが無効化されたことを示す状態に変わります。 同じ手順を使用して、以前無効にしたルールを再度有効にすることができます。

[![ルールを無効にする](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

一覧で複数のルールを選択して、複数のルールを同時に有効または無効にすることができます。

## <a name="delete-a-rule"></a>規則を削除する

ルールを完全に削除するには、ルールの一覧から削除します。 削除するルールを選択し、**[削除]** を選択します。

[![ルールの削除](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

ルールの削除ことを確認した後に、ルールに関連付けられているアラートを **[メンテナンス]** ページから削除する機会が与えられます。

[![ルールの削除](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

一度に削除できるルールは 1 つのみです。

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータの **[ルール]** ページを使用して、ソリューション内でアラートをトリガーするルールを作成および管理する方法について説明しました。 ソリューション アクセラレータを使用して接続されているデバイスを管理および構成する方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [監視ソリューションに接続されているデバイスの構成および管理](iot-accelerators-remote-monitoring-manage.md)