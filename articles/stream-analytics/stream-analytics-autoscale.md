---
title: Stream Analytics ジョブの自動スケーリング
description: この記事では、定義済みスケジュールやジョブ メトリックの値に基づいて Stream Analytics ジョブを自動スケーリングする方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016288"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Azure Automation を使用した Stream Analytics ジョブの自動スケーリング

自動スケーリングを構成すると、Stream Analytics ジョブのコストを最適化することができます。 自動スケーリングを使用すると、入力負荷の変化に合わせて、ジョブのストリーミング ユニット数 (SU) が増減します。 ジョブを過剰にプロビジョニングする代わりに、スケールアップおよびダウンを必要に応じて行うことができます。 自動スケーリングするようにジョブを構成する方法には、次の 2 つがあります。
1. **スケジュールを事前定義する**: 予測可能な入力負荷がある場合に使用します。 たとえば、日中に入力イベントの割合が高いことが予想され、より多くの SU を使用してジョブを実行したい場合に使用できます。
2. **ジョブ メトリックに基づいてスケールアップおよびスケールダウン操作をトリガーする**: 予測可能な入力負荷がない場合に使用します。 入力イベントやバックログされた入力イベントの数などのジョブ メトリックに基づいて、SU の数を動的に変更できます。

## <a name="prerequisites"></a>前提条件
ジョブの自動スケーリングの構成を開始する前に、次の手順を実行します。
1. [並列トポロジ](./stream-analytics-parallelization.md)になるよう、ジョブを最適化する。 ジョブの実行中にジョブのスケールを変更できる場合、ジョブは並列トポロジであり、自動スケーリングできるように構成できます。
2. "RunAsAccount" オプションを有効にして [Azure Automation アカウントを作成する](../automation/automation-create-standalone-account.md)。 このアカウントには、Stream Analytics ジョブを管理するためのアクセス許可が必要です。

## <a name="set-up-azure-automation"></a>Azure Automation の設定
### <a name="configure-variables"></a>変数の構成
以下の変数を Azure Automation アカウント内に追加します。 これらの変数は、次の手順で説明する Runbook で使用されます。

| 名前 | Type | 値 |
| --- | --- | --- |
| **jobName** | String | 自動スケーリングする Stream Analytics ジョブの名前。 |
| **resourceGroupName** | String | ジョブが存在するリソース グループの名前。 |
| **subId** | String | ジョブが存在するサブスクリプション ID。 |
| **increasedSU** | Integer | スケジュール内でジョブをスケーリングする高い方の SU 値。 この値は、ジョブの実行中にその **[スケーリング]** 設定に表示される有効な SU オプションのいずれかである必要があります。 |
| **decreasedSU** | Integer | スケジュール内でジョブがスケーリングする低い方の SU 値。 この値は、ジョブの実行中にその **[スケーリング]** 設定に表示される有効な SU オプションのいずれかである必要があります。 |
| **maxSU** | Integer | 負荷に応じて自動スケーリングする際に、ジョブが段階的にスケーリングするときの SU の最大値。 この値は、ジョブの実行中にその **[スケーリング]** 設定に表示される有効な SU オプションのいずれかである必要があります。 |
| **minSU** | Integer | 負荷に応じて自動スケーリングする際に、ジョブが段階的にスケーリングするときの SU の最小値。 この値は、ジョブの実行中にその **[スケーリング]** 設定に表示される有効な SU オプションのいずれかである必要があります。 |

![Azure Automation への変数の追加](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbook を作成する
次の手順として、PowerShell Runbook を 2 つ作成します。 1 つはスケールアップ操作用で、もう 1 つはスケールダウン操作用です。
1. Azure Automation アカウントで、 **[プロセス オートメーション]** の下にある **[Runbook]** に移動し、 **[Create Runbook]\(Runbook の作成\)** を選択します。
2. 最初の Runbook に *ScaleUpRunbook* と名前を付け、種類を PowerShell に設定します。 GitHub で入手できる [ScaleUpRunbook PowerShell スクリプト](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1)を使用します。 これを保存して公開します。
3. *ScaleDownRunbook* という名前で別の Runbook を作成し、種類を PowerShell に指定します。 GitHub で入手できる [ScaleDownRunbook PowerShell スクリプト](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1)を使用します。 これを保存して公開します。

![Azure Automation で Runbook を自動スケーリングする](./media/autoscale/runbooks.png)

これで、Stream Analytics ジョブで自動的にスケールアップおよびスケールダウン操作がトリガーされる Runbook を作成できました。 これらの Runbook は、定義済みスケジュールを使用してトリガーすることも、ジョブ メトリックに基づいて動的に設定することもできます。

## <a name="autoscale-based-on-a-schedule"></a>スケジュールに基づいた自動スケール
Azure Automation では、Runbook をトリガーするスケジュールを構成できます。
1. Azure Automation アカウントで、 **[共有リソース]** の下にある **[スケジュール]** を選択します。 次に、 **[スケジュールの追加]** を選択します。
2. たとえば、2 つのスケジュールを作成できます。 1 つはジョブがスケールアップするタイミングを表し、もう 1 つはジョブがスケールダウンするタイミングを表します。 これらのスケジュールの繰り返しを定義できます。

   ![Azure Automation のスケジュール](./media/autoscale/schedules.png)

3. **ScaleUpRunbook** を開き、 **[リソース]** の下にある **[スケジュール]** を選択します。 次に、前の手順で作成したスケジュールに Runbook をリンクします。 同一の Runbook に対して複数のスケジュールをリンクすることができます。これは、1 日の異なる時間に同じスケーリング操作を実行する必要がある場合に役立ちます。

![Azure Automation での Runbook のスケジュール設定](./media/autoscale/schedulerunbook.png)

1. **ScaleDownRunbook** について、前の手順を繰り返します。

## <a name="autoscale-based-on-load"></a>負荷に基づく自動スケーリング
入力負荷を予測できない場合もあります。 そのような場合、最小値と最大値の範囲内で段階的にスケールアップまたはダウンする方がより適しています。 Stream Analytics ジョブのアラート ルールで、ジョブ メトリックがしきい値を上回った場合や下回った場合に Runbook をトリガーするように構成できます。
1. Azure Automation アカウントで、**minSU** と **maxSU** という名前の 2 つの整数変数を作成します。 これにより、ジョブが段階的にスケーリングする境界が設定されます。
2. 新しい Runbook を 2 つ作成します。 ジョブの SU を **maxSU** 値まで段階的に増加させる [StepScaleUp PowerShell スクリプト](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1)を使用できます。 また、ジョブの SU を **minSU** 値に達するまで段階的に減少させる [StepScaleDown PowerShell スクリプト](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1)も使用できます。 または、SU 値をどの値にスケーリングしたいかが具体的に決まっているなら、前のセクションの Runbook を使用することもできます。
3. Stream Analytics ジョブで、 **[監視]** の下にある **[アラート ルール]** を選択します。 
4. 2 つのアクション グループを作成します。 1 つはスケールアップ操作用で、もう 1 つはスケールダウン操作用です。 **[アクションの管理]** を選択し、 **[アクション グループの追加]** をクリックします。 
5. 必須フィールドに入力します。 **[アクションの種類]** は、 **[Automation Runbook]** を選択します。 アラートが発生したときにトリガーする Runbook を選択します。 次に、アクション グループを作成します。

   ![アクション グループの作成](./media/autoscale/create-actiongroup.png)
6. ジョブで、[ **[新しいアラート ルール]**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) を作成します。 選択したメトリックに基づいて条件を指定します。 自動スケールリング ロジックの定義で使用をお勧めするメトリックは、[ *[入力イベント]* 、 *[SU % 使用率]* 、または *[バックログされた入力イベント]*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) です。 また、スケールアップ操作をトリガーする場合は、 *[集約粒度]* と *[評価の頻度]* を 1 分間に指定して使用することをお勧めします。 そうすることにより、ジョブで入力ボリュームの急増に対処するために必要となる十分なリソースを確保できます。
7. 最後の手順で作成したアクション グループを選択し、アラートを作成します。
8. ジョブ メトリックの条件に基づいてトリガーする追加のスケーリング操作ごとに、手順 2 から 4 を繰り返します。

ジョブを運用環境で実行する前に、スケーリングのテストを実行することをお勧めします。 さまざまな入力負荷に対してジョブをテストすることにより、異なる入力スループットに対してどれほどの SU がジョブに必要かを確認できます。 これは、アラート ルールで定義する、スケールアップ操作とスケールダウン操作のトリガー条件を決定するのに役立ちます。 

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics での並列化可能クエリの作成](stream-analytics-parallelization.md)
* [スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)