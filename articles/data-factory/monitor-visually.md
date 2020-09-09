---
title: Azure Data Factory を視覚的に監視する
description: Azure Data Factory を視覚的に監視する方法について説明します
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b65b5b847712ce32c6723d725be9cf2ed1f99050
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801532"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory を視覚的に監視する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory でパイプラインを作成して公開した後、それをトリガーに関連付けたり、アドホック実行を手動で開始したりできます。 Azure Data Factory ユーザー エクスペリエンスで、すべてのパイプラインの実行をネイティブに監視できます。 監視エクスペリエンスを開くには、[Azure portal](https://portal.azure.com/) の [データ ファクトリ] ブレードで **[監視と管理]** タイルを選択します。 ADF UX を既に使用している場合は、左側のサイドバーにある **[監視]** アイコンをクリックします。

すべてのデータ ファクトリの実行は、ブラウザーのローカル タイム ゾーンで表示されます。 タイム ゾーンを変更した場合、すべての日付/時刻フィールドは選択したものにスナップされます。

## <a name="monitor-pipeline-runs"></a>パイプラインの実行を監視する

既定の監視ビューは、選択した期間内のパイプラインの実行の一覧です。 次の列が表示されます。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前 |
| Actions | アクティビティの詳細の表示、キャンセル、またはパイプラインの再実行を可能にするアイコン |
| Run Start (実行の開始) | パイプラインの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Triggered By (トリガー元) | パイプラインを開始したトリガーの名前 |
| Status | **失敗**、**成功**、**進行中**、**キャンセル済**、または **キューに登録済み** |
| 注釈 | パイプラインに関連付けられたフィルター可能なタグ  |
| パラメーター | パイプラインの実行のパラメーター (名前/値のペア) |
| エラー | パイプラインが失敗した場合の実行エラー |
| Run ID (実行 ID) | パイプライン実行の ID。 |

![パイプラインの実行を監視するためのリスト ビュー](media/monitor-visually/pipeline-runs.png)

パイプラインとアクティビティの実行一覧を最新の情報に更新するには、 **[最新の情報に更新]** ボタンを手動で選択する必要があります。 現在、自動更新はサポートされていません。

![[最新の情報に更新] ボタン](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>アクティビティの実行を監視する

各パイプラインの実行でのアクティビティの実行を表示するには、 **[アクション]** 列の **[アクティビティの実行の表示]** アイコンを選択します。 リスト ビューには、各パイプラインの実行に対応するアクティビティの実行が表示されます。

| **列名** | **説明** |
| --- | --- |
| Activity Name (アクティビティ名) | パイプライン内のアクティビティの名前 |
| Activity Type (アクティビティの種類) | アクティビティの種類 (**Copy**、**ExecuteDataFlow**、**AzureMLExecutePipeline**  など) |
| Actions | JSON 入力情報、JSON 出力情報、またはアクティビティ固有の詳細な監視エクスペリエンスの表示を可能にするアイコン | 
| Run Start (実行の開始) | アクティビティの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Status | **失敗**、**成功**、**進行中**、または**キャンセル済** |
| 統合ランタイム | アクティビティが実行された統合ランタイム |
| ユーザー プロパティ | アクティビティのユーザー定義プロパティ |
| エラー | アクティビティが失敗した場合の実行エラー |
| Run ID (実行 ID) | アクティビティ実行の ID。 |

![アクティビティの実行を監視するためのリスト ビュー](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>監視対象のユーザー プロパティを昇格する

監視対象のエンティティになるように、任意のパイプラインのアクティビティ プロパティをユーザー プロパティとして昇格します。 たとえば、パイプラインのコピー アクティビティの **Source** プロパティと **Destination** プロパティをユーザー プロパティとして昇格できます。 **[自動生成]** を選択して、コピー アクティビティの **Source** および **Destination** ユーザー プロパティを生成します。

![ユーザー プロパティの作成](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> ユーザー プロパティとして昇格できるパイプライン アクティビティ プロパティは、最大 5 つです。

作成したユーザー プロパティは、監視リスト ビューで監視できます。 コピー アクティビティのソースがテーブル名の場合、アクティビティの実行のリスト ビューでソースのテーブル名を列として監視できます。

![ユーザー プロパティを持たないアクティビティの実行リスト](media/monitor-visually/monitor-user-properties-image2.png)

![アクティビティの実行の一覧へのユーザー プロパティの列の追加](media/monitor-visually/monitor-user-properties-image3.png)

![アクティビティ実行の一覧とユーザー プロパティの列](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>リスト ビューを構成する

### <a name="order-and-filter"></a>順序とフィルター

パイプラインの実行が、実行の開始時刻に従って降順になるか昇順になるかを切り替えます。 次の列を使用して、パイプラインの実行をフィルター処理します。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前でフィルター処理します。 |
| Run Start (実行の開始) |  表示されるパイプラインの実行の時間範囲を決定します。 オプションには **[過去 24 時間]** 、 **[先週]** 、および **[過去 30 日間]** のクイック フィルターが含まれています。または、カスタムの日時を選択します。 |
| Run Status (ジョブの状態) | 次の状態別に実行をフィルター処理:**成功**、**失敗**、**キューに登録済み**、**キャンセル済**、または**進行中**。 |
| 注釈 | 各パイプラインに適用されるタグでフィルター処理する |
| 実行 | パイプラインを表示するかどうかでフィルター処理する |

![フィルターのオプション](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>列を追加または削除する
リスト ビューの見出しを右クリックし、リスト ビューに表示する列を選択します。

![列のオプション](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>列幅を調整する
リスト ビューの列幅を拡大または縮小するには、列見出しにマウス ポインターを移動します。

## <a name="rerun-activities-inside-a-pipeline"></a>パイプライン内のアクティビティを再実行する

パイプライン内のアクティビティを再実行できます。 **[View activity runs]\(アクティビティの実行の表示\)** を選択し、パイプラインからその再実行の起点となるアクティビティを選択します。

![アクティビティの実行の表示](media/monitor-visually/rerun-activities-image1.png)

![アクティビティの実行の選択](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>失敗したアクティビティから再実行する

アクティビティが失敗した場合、タイムアウトした場合、または取り消された場合、 **[Rerun from failed activity]\(失敗したアクティビティから再実行する\)** を選択して、失敗したアクティビティからパイプラインを再実行できます。

![失敗したアクティビティを再実行する](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>再実行履歴を表示する

パイプラインの全実行の再実行履歴は、リスト ビューで確認することができます。

![履歴を表示する](media/monitor-visually/rerun-history-image1.png)

特定のパイプラインの実行について、再実行履歴を確認することもできます。

![パイプラインの実行履歴の表示](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>消費量の監視

実行の横にある使用量アイコンをクリックすると、パイプラインの実行によって消費されているリソースを確認できます。 

![消費量の監視](media/monitor-visually/monitor-consumption-1.png)

このアイコンをクリックすると、そのパイプラインの実行によって使用されているリソースの消費量レポートが開きます。 

![消費量の監視](media/monitor-visually/monitor-consumption-2.png)

これらの値を [Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/data-factory/)に組み込んで、パイプラインの実行のコストを見積もることができます。 Azure Data Factory 料金の詳細については、[価格に関するページ](pricing-concepts.md)を参照してください。

> [!NOTE]
> 料金計算ツールによって返される値は、推定値です。 Azure Data Factory で課金される正確な金額は反映されません。 

## <a name="gantt-views"></a>ガント ビュー

ガント ビューを使用すると、パイプラインとアクティビティの実行をすばやく可視化できます。

![ガント チャートの例](media/monitor-visually/gantt1.png)

ガント ビューをパイプラインごとに表示することも、パイプラインで作成した注釈/タグでグループ化することもできます。

![ガント チャートの注釈](media/monitor-visually/gantt2.png)

バーの長さはパイプラインの期間を示します。 バーを選択して詳細を表示することもできます。

![ガント チャートの期間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>ガイド ツアー
左下の**情報**アイコンを選択します。 次に、**ガイド ツアー**を選択して、パイプラインの実行やアクティビティの実行を監視する方法について詳細な手順を確認します。

![ガイド ツアー](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>警告

Data Factory で、サポートされるメトリックに対してアラートを生成できます。 Data Factory の監視ページで **[監視]**  >  **[Alerts & Metrics]\(アラートとメトリック\)** の順に選択して開始します。

![Data Factory の監視ページ](media/monitor-visually/start-page.png)

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>アラートを作成する

1.  **[新しいアラート ルール]** を選択して新しいアラートを作成します。

    ![[新しいアラート ルール] ボタン](media/monitor-visually/new-alerts.png)

1.  ルール名を指定し、アラートの重大度を選択します。

    ![ルール名と重要度のボックス](media/monitor-visually/name-and-severity.png)

1.  アラートの条件を選択します。

    ![対象の条件のボックス](media/monitor-visually/add-criteria-1.png)

    ![条件の一覧](media/monitor-visually/add-criteria-2.png)

    ![条件の一覧](media/monitor-visually/add-criteria-3.png)

    アラートは、さまざまなメトリックに対して作成できます。これには、ADF エンティティの数またはサイズ、アクティビティ、パイプライン、またはトリガーの実行、Integration Runtime (IR) CPU の使用率、メモリ、ノード数、またはキューに加えて、SSIS パッケージ実行および SSIS IR の開始または停止操作などのメトリックが含まれます。

1.  アラート ロジックを構成します。 すべてのパイプラインと対応するアクティビティについて、選択したメトリックのアラートを作成できます。 また、特定のアクティビティの種類、アクティビティ名、パイプライン名、またはエラーの種類を選択することもできます。

    ![アラート ロジックの構成のオプション](media/monitor-visually/alert-logic.png)

1.  アラートに対して、メール、SMS、プッシュ、および音声での通知を構成します。 アラート通知用にアクション グループを作成するか、既存のものを選択します。

    ![通知の構成のオプション](media/monitor-visually/configure-notification-1.png)

    ![通知の追加のオプション](media/monitor-visually/configure-notification-2.png)

1.  アラート ルールを作成します。

    ![アラート ルールの作成のオプション](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>次のステップ

パイプラインの監視と管理の詳細については、[プログラムでのパイプラインの監視と管理](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)に関する記事をご覧ください。
