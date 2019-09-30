---
title: Azure Data Factory を視覚的に監視する | Microsoft Docs
description: Azure Data Factory を視覚的に監視する方法について説明します
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 99ae0139d7b12c0bfb87030d2b749b12834a4f96
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141098"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory を視覚的に監視する
Azure Data Factory は、クラウドベースのデータ統合サービスです。 これを使用して、データ移動とデータ変換を調整し、自動化するためのデータ主導型ワークフローをクラウド内に作成できます。 Azure Data Factory を使用して、以下を実行できます。

- 各種のデータ ストアからデータを取り込むことができるデータ主導型のワークフロー (パイプライン) を作成し、スケジューリングする。
- そのデータを Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning などのコンピューティング サービスを使って処理または変換する。
- ビジネス インテリジェンス (BI) アプリケーションから利用できるよう、Azure SQL Data Warehouse などのデータ ストアに出力データを公開する。

このクイック スタートでは、Data Factory パイプラインを視覚的に監視する方法について説明します。コードの記述は一切必要ありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="monitor-data-factory-pipelines"></a>Data Factory パイプラインを監視する

単純なリスト ビュー インターフェイスでパイプラインとアクティビティの実行を監視します。 すべての実行はブラウザーのローカル タイム ゾーンで表示されます。 タイム ゾーンを変更した場合、すべての日付/時刻フィールドは選択したものにスナップされます。  

1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこの 2 つの Web ブラウザーでのみサポートされています。
2. [Azure portal](https://portal.azure.com/) にサインインします。
3. Azure portal で、作成済みのデータ ファクトリのブレードに移動します。 **[Monitor & Manage]\(監視と管理\)** タイルを選択して、Data Factory ビジュアル監視エクスペリエンス を開始します。

## <a name="monitor-pipeline-runs"></a>パイプラインの実行を監視する
リスト ビューには、Data Factory パイプラインの各パイプラインの実行が表示されます。 次の列が含まれます。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前 |
| Actions | アクティビティの実行の表示に使用できる単一のアクション |
| Run Start (実行の開始) | パイプラインの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Triggered By (トリガー元) | 手動トリガーまたはスケジュール済みのトリガー |
| Status | **失敗**、 **成功**、または**進行中** |
| parameters | パイプラインの実行のパラメーター (名前/値のペア) |
| Error | パイプラインの実行エラー (発生した場合) |
| Run ID (実行 ID) | パイプライン実行の ID。 |

![パイプラインの実行を監視するためのリスト ビュー](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>アクティビティの実行を監視する
リスト ビューには、各パイプラインの実行に対応するアクティビティの実行が表示されます。 各パイプライン実行のアクティビティの実行を表示するには、 **[アクション]** 列の **[アクティビティの実行]** アイコンを選択します。 リスト ビューには、次の列が含まれます。

| **列名** | **説明** |
| --- | --- |
| Activity Name (アクティビティ名) | パイプライン内のアクティビティの名前 |
| Activity Type (アクティビティの種類) | アクティビティの種類 (**Copy**、**HDInsightSpark**、**HDInsightHive** など) |
| Run Start (実行の開始) | アクティビティの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Status | **失敗**、 **成功**、または**進行中** |
| 入力 | アクティビティの入力を記述する JSON 配列 |
| Output | アクティビティの出力を記述する JSON 配列 |
| Error | アクティビティの実行エラー (発生した場合) |

![アクティビティの実行を監視するためのリスト ビュー](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> パイプラインとアクティビティの実行一覧を最新の情報に更新するには、上部の **[最新の情報に更新]** ボタンを選択する必要があります。 現在、自動更新はサポートされていません。

![[最新の情報に更新] ボタン](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>監視するデータ ファクトリの選択
左上の **[Data Factory]** アイコンにマウス ポインターを移動します。 矢印アイコンを選択して、監視できる Azure サブスクリプションとデータ ファクトリのリストを表示します。

![データ ファクトリの選択](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>リスト ビューを構成する

### <a name="apply-rich-ordering-and-filtering"></a>高度な並べ替えとフィルター処理を適用する

パイプラインの実行順序を、実行の開始時刻に従って昇順または降順で並べ替えます。 次の列を使用して、パイプラインの実行をフィルター処理します。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前。 オプションには **[過去 24 時間]** 、 **[先週]** 、 **[過去 30 日間]** のクイック フィルターがあります。 または、カスタムの日付と時刻を選択します。 |
| Run Start (実行の開始) | パイプラインの実行の開始日時。 |
| Run Status (ジョブの状態) | 次の状態別に実行をフィルター処理:**成功**、 **失敗**、または**進行中**。 |

![フィルターのオプション](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>列を追加または削除する
リスト ビューの見出しを右クリックし、リスト ビューに表示する列を選択します。

![列のオプション](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>列幅を調整する
リスト ビューの列幅を拡大または縮小するには、列見出しにマウス ポインターを移動します。

## <a name="promote-user-properties-to-monitor"></a>監視対象のユーザー プロパティを昇格する

監視可能なエンティティになるように、任意のパイプラインのアクティビティ プロパティをユーザー プロパティとして昇格できます。 たとえば、パイプラインのコピー アクティビティの **Source** プロパティと **Destination** プロパティをユーザー プロパティとして昇格できます。 また、 **[自動生成]** を選択して、コピー アクティビティの **Source** および **Destination** ユーザー プロパティを生成することもできます。

![ユーザー プロパティの作成](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> ユーザー プロパティとして昇格できるパイプライン アクティビティ プロパティは、最大 5 つです。

作成したユーザー プロパティは、監視リスト ビューで監視できます。 コピー アクティビティのソースがテーブル名の場合、アクティビティの実行のリスト ビューでソースのテーブル名を列として監視できます。

![ユーザー プロパティを持たないアクティビティの実行リスト](media/monitor-visually/monitor-user-properties-image2.png)

![アクティビティの実行の一覧へのユーザー プロパティの列の追加](media/monitor-visually/monitor-user-properties-image3.png)

![アクティビティ実行の一覧とユーザー プロパティの列](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>パイプライン内のアクティビティを再実行する

パイプライン内のアクティビティを再実行できる状態になりました。 **[View activity runs]\(アクティビティの実行の表示\)** を選択し、パイプラインからその再実行の起点となるアクティビティを選択します。

![アクティビティの実行の表示](media/monitor-visually/rerun-activities-image1.png)

![アクティビティの実行の選択](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>再実行履歴を表示する

パイプラインの全実行の再実行履歴は、リスト ビューで確認することができます。

![履歴を表示する](media/monitor-visually/rerun-history-image1.png)

特定のパイプラインの実行について、再実行履歴を確認することもできます。

![パイプラインの実行履歴の表示](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>ガント ビュー

ガント ビューを使用すると、パイプラインとアクティビティの実行をすばやく可視化できます。 ガント ビューをパイプラインごとに表示することも、パイプラインで作成した注釈やタグでグループ化することもできます。

![ガント チャートの例](media/monitor-visually/gantt1.png)

![ガント チャートの注釈](media/monitor-visually/gantt2.png)

バーの長さはパイプラインの期間を示します。 バーを選択して詳細を表示することもできます。

![ガント チャートの期間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>ガイド ツアー
左下の**情報**アイコンを選択します。 次に、**ガイド ツアー**を選択して、パイプラインの実行やアクティビティの実行を監視する方法について詳細な手順を確認します。

![ガイド ツアー](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>フィードバック
**フィードバック** アイコンを選択して、さまざまな機能や、発生している問題についてフィードバックをお寄せください。

![フィードバック](media/monitor-visually/feedback.png)

## <a name="alerts"></a>アラート

Data Factory で、サポートされるメトリックに対してアラートを生成できます。 Data Factory の監視ページで **[監視]**  >  **[Alerts & Metrics]\(アラートとメトリック\)**   の順に選択して開始します。

![Data Factory の監視ページ](media/monitor-visually/alerts01.png)

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>アラートを作成する

1.  **[新しいアラート ルール]**   を選択して新しいアラートを作成します。

    ![[新しいアラート ルール] ボタン](media/monitor-visually/alerts02.png)

1.  ルール名を指定し、アラートの重大度を選択します。

    ![ルール名と重要度のボックス](media/monitor-visually/alerts03.png)

1.  アラートの条件を選択します。

    ![対象の条件のボックス](media/monitor-visually/alerts04.png)

    ![条件の一覧](media/monitor-visually/alerts05.png)

1.  アラート ロジックを構成します。 すべてのパイプラインと対応するアクティビティについて、選択したメトリックのアラートを作成できます。 また、特定のアクティビティの種類、アクティビティ名、パイプライン名、またはエラーの種類を選択することもできます。

    ![アラート ロジックの構成のオプション](media/monitor-visually/alerts06.png)

1.  アラートに対して、メール、SMS、プッシュ、および音声での通知を構成します。 アラート通知用にアクション グループを作成するか、既存のものを選択します。

    ![通知の構成のオプション](media/monitor-visually/alerts07.png)

    ![通知の追加のオプション](media/monitor-visually/alerts08.png)

1.  アラート ルールを作成します。

    ![アラート ルールの作成のオプション](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>次の手順

パイプラインの監視と管理の詳細については、[プログラムでのパイプラインの監視と管理](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)に関する記事をご覧ください。
