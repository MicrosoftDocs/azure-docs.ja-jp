---
title: Azure ストレージ アカウントの監視方法 | Microsoft Docs
description: Azure ポータルを使用して Azure でストレージ アカウントを監視する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 64cfac0d689df88c4d432e772bcd0a0cc7ab4ade
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317682"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Azure ポータルでのストレージ アカウントの監視

[Azure Storage Analytics](storage-analytics.md) では、すべてのストレージ サービスのメトリックと、BLOB、キュー、およびテーブルのログを利用できます。 [Azure Portal](https://portal.azure.com) を使用すると、アカウントに対してどのメトリックとログを記録するか、また、そのメトリック データを視覚的に表現するグラフを構成できます。

> [!NOTE]
> Azure Portal で監視データを調査するとコストがかかります。 詳細については、「[Storage Analytics](storage-analytics.md)」を参照してください。
>
> 現在、Azure Files は、Storage Analytics のメトリックをサポートしますが、ログ記録はまだサポートされていません。
>
> Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「 [Microsoft Azure ストレージの監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。
>

## <a name="configure-monitoring-for-a-storage-account"></a>ストレージ アカウントの監視の設定

1. [Azure Portal](https://portal.azure.com) で **[ストレージ アカウント]** を選択し、目的のストレージ アカウントの名前を選択して、アカウント ダッシュボードを開きます。
1. メニュー ブレードの **[監視]** セクションで **[診断]** を選択します。

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. 監視する**サービス**ごとにメトリック データの**型**を選択し、データの**保持ポリシー**を選択します。 **[ステータス]** を **[オフ]** に設定して、監視を無効にすることもできます。

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   データ保持ポリシーを設定するには、 **[保有期間 (日)]** スライダーを動かすか、データを保有する日数 (1 ～ 365) を入力します。 新しいストレージ アカウントの既定値は 7 日間です。 保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。 保有ポリシーがない場合、監視データを削除する責任はユーザーが負います。

   > [!WARNING]
   > メトリック データを手動で削除するときは課金されます。 システムによって古い分析データ (保持ポリシーよりも古いデータ) が削除されるときは費用がかかりません。 アカウントのストレージ分析データを保持する必要がある期間に基づいて、データ保持ポリシーを設定することをお勧めします。 詳細については、「[Billing on storage metrics](storage-analytics-metrics.md#billing-on-storage-metrics)」 (ストレージ メトリックへの課金) を参照してください。
   >

1. 監視の構成が完了したら、**[保存]** を選択します。

既定のメトリック セットは、ストレージ アカウント ブレードのグラフと、個別のサービス ブレード (BLOB、キュー、テーブル、およびファイル) に表示されます。 サービスのメトリックを有効にした場合、データがグラフに表示されるまでに最大で 1 時間かかる場合があります。 メトリック グラフで **[編集]** を選択すると、グラフに表示するメトリックを構成できます。

メトリックの収集とログ記録を無効にするには、**[ステータス]** を **[オフ]** に設定します。

> [!NOTE]
> Azure Storage では[テーブル ストレージ](storage-introduction.md#table-storage)を使用して、ストレージ アカウントのメトリックが保存されます。メトリックが保存されるのはアカウントのテーブルです。 詳細については、 「[メトリックの保存](storage-analytics-metrics.md#how-metrics-are-stored)」を参照してください。
>

## <a name="customize-metrics-charts"></a>メトリック グラフのカスタマイズ

メトリック グラフに表示するストレージ メトリックを選択するには、次の手順に従います。

1. 最初に Azure Portal でストレージのメトリック グラフを表示します。 サービス (BLOB、キュー、テーブル、ファイル) ごとに、**ストレージ アカウント ブレード**と **[メトリック]** ブレードにグラフがあります。

   この例では、**ストレージ アカウント ブレード**に表示される次のグラフを使用します。

   ![Azure Portal でのグラフの選択](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. グラフを編集するために、グラフ内の任意の場所をクリックします。

1. 次に、グラフに表示するメトリックの**時間範囲**と、表示するメトリックの**サービス** (BLOB、キュー、テーブル、ファイル) を選択します。 ここでは、Blob service の過去 1 週間のメトリックが表示されるように選択しました。

   ![[グラフの編集] ブレードでの時間の範囲とサービスの選択](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. グラフに表示されている必要な個別の**メトリック**を選択し、**[OK]** をクリックします。

   ![[グラフの編集] ブレードでの個別のメトリックの選択](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

グラフの設定は、ストレージ アカウントの監視データの収集、集計、または保存には影響しません。

### <a name="metrics-availability-in-charts"></a>グラフのメトリックの可用性

使用可能なメトリックの一覧は、ドロップダウン リストで選択したサービスと、編集しているグラフの単位の種類に基づいて変わります。 たとえば、*PercentNetworkError*、*PercentThrottlingError* などの割合メトリックを選択できるのは、単位の割合が表示されているグラフを編集している場合だけです。

![Azure Portal の要求エラーの割合グラフ](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>メトリック解像度

**[診断]** で選択したメトリックによって、アカウントで利用可能なメトリックの解像度が決まります。

* **集計**: 受信/送信、空き時間情報、遅延時間、成功のパーセンテージなどのメトリックを監視します。 このメトリックは、BLOB、テーブル、ファイル、およびキューのサービスごとに集計されます。
* **API ごと**: さらに細かな解像度が、サービス レベル集計のほか、個別のストレージ操作で使用できるメトリックと共に示されます。

## <a name="configure-metrics-alerts"></a>メトリック アラートの構成

ストレージ リソース メトリックのしきい値に達したときに通知するアラートを作成できます。

1. **警告ルール ブレード**を開くには、下にスクロールして**メニュー ブレード**の **[監視]** セクションを表示し、**[アラート (クラシック)]** を選択します。
2. **[メトリック アラートの追加 (クラシック)]** を選択して、**[アラート ルールの追加]** ブレードを開きます
3. 新しい警告ルールの**名前**と**説明**を指定します。
4. アラートを追加する**メトリック**を選択し、アラートの**条件**、および**しきい値**を選択します。 しきい値の単位の種類は、選択したメトリックによって異なります。 たとえば、"回数" は *ContainerCount* の単位の種類ですが、*PercentNetworkError* の単位は割合です。
5. **期間**を選択します。 この期間内にメトリックがしきい値以上になると、アラートがトリガーされます。
6. (省略可能) **電子メール**と **webhook** の通知を構成します。 webhook の詳細については、「[Azure メトリック アラートでの webhook の構成](../../azure-monitor/platform/alerts-webhooks.md)」を参照してください。 電子メールまたは webhook の通知を構成しない場合は、Azure Portal にのみアラートが表示されます。

![Azure Portal の [アラート ルールの追加] ブレード](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>ポータル ダッシュボードへのメトリック グラフの追加

ストレージ アカウントの Azure Storage メトリック グラフを、ポータルのダッシュボードに追加できます。

1. [Azure Portal](https://portal.azure.com) にダッシュボードが表示されている間に、**[ダッシュボードの編集]** をクリックします。
1. **[タイル ギャラリー]** で **[タイルの検索方法]**  >  **[種類]** の順に選択します。
1. **[種類]**  >  **[ストレージ アカウント]** の順に選択します。
1. **[リソース]** で、ダッシュボードに追加するメトリックのストレージ アカウントを選択します。
1. **[カテゴリ]**  >  **[監視]** の順に選択します。
1. グラフのタイルを、メトリックを表示したいダッシュボードにドラッグ アンド ドロップします。 ダッシュボードに表示するすべてのメトリックについて、この操作を繰り返します。 次の図では、"BLOB - 要求の合計" グラフが例として強調表示されますが、すべてのグラフをダッシュボードに配置することができます。

   ![Azure Portal のタイル ギャラリー](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. グラフの追加が完了したら、ダッシュボードの上部にある **[カスタマイズ完了]** を選択します。

グラフをダッシュボードに追加したら、「メトリック グラフのカスタマイズ」の説明に従って、そのグラフをさらにカスタマイズすることができます。

## <a name="configure-logging"></a>ログの構成

BLOB、テーブル、およびキューごとに、読み取り要求、書き込み要求、および削除要求の診断ログを保存するよう Azure Storage に指示できます。 設定したデータの保持ポリシーはこうしたログにも適用されます。

> [!NOTE]
> 現在、Azure Files は、Storage Analytics のメトリックをサポートしますが、ログ記録はまだサポートされていません。
>

1. [Azure Portal](https://portal.azure.com) で **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択して、ストレージ アカウント ブレードを開きます。
1. メニュー ブレードの **[監視]** セクションで **[診断]** を選択します。

    ![Azure Portal の [監視] の [診断] メニュー。](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. **[ステータス]** を **[オン]** に設定して、ログを有効にする**サービス**を選択します。

    ![Azure Portal でログを構成する。](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. **[Save]** をクリックします。

診断ログは、ストレージ アカウントの *$logs* という名前の BLOB コンテナーに保存されます。 ログ データを表示するには、[Microsoft Storage Explorer](https://storageexplorer.com) などのストレージ エクスプローラーを使用するか、プログラムによってストレージ クライアント ライブラリまたは PowerShell を使用します。

$Logs コンテナーへのアクセスについては、[ストレージ分析ログ](storage-analytics-logging.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* Storage Analytics の[メトリック、ログ、および課金](storage-analytics.md)の詳細について確認する。
