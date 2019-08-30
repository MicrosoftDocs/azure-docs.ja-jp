---
title: Azure Data Factory を視覚的に監視する | Microsoft Docs
description: Azure Data Factory を視覚的に監視する方法について説明します
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720613"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory を視覚的に監視する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。

このクイック スタートでは、Data Factory パイプラインを視覚的に監視する方法について説明します。コードの記述は一切必要ありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="monitor-data-factory-pipelines"></a>Data Factory パイプラインを監視する

単純なリスト ビュー インターフェイスでパイプラインとアクティビティの実行を監視します。 すべての実行はローカル ブラウザーのタイム ゾーンで表示されます。 タイム ゾーンは変更できます。すべての日時フィールドは選択したタイム ゾーンにスナップされます。  

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com/) にログインします。
3. Azure portal で、作成した Data Factory ブレードに移動し、[Monitor & Manage]\(監視と管理\) タイルをクリックして Data Factory ビジュアル監視エクスペリエンスを起動します。

## <a name="monitor-pipeline-runs"></a>パイプラインの実行を監視する
リスト ビューには、Data Factory v2 パイプラインの各パイプラインの実行が表示されます。 次のような列があります。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前。 |
| Actions | アクティビティ実行の表示に使用できる単一のアクション。 |
| Run Start (実行の開始) | パイプラインの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Triggered By (トリガー元) | 手動トリガー、スケジュール トリガー |
| Status | 失敗、成功、進行中 |
| parameters | パイプラインの実行パラメーター (名前、値のペア) |
| Error | パイプラインの実行エラー (発生した場合) |
| Run ID (実行 ID) | パイプライン実行の ID。 |

![パイプラインの実行を監視する](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>アクティビティの実行を監視する
リスト ビューには、各パイプラインの実行に対応するアクティビティの実行が表示されます。 **[アクション]** 列の **[アクティビティの実行]** アイコンをクリックして、各パイプラインの実行のアクティビティの実行を表示します。 次のような列があります。

| **列名** | **説明** |
| --- | --- |
| Activity Name (アクティビティ名) | パイプライン内のアクティビティの名前。 |
| Activity Type (アクティビティの種類) | アクティビティの種類 (Copy、HDInsightSpark、HDInsightHive など) |
| Run Start (実行の開始) | アクティビティの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duration | 実行期間 (HH:MM:SS) |
| Status | 失敗、成功、進行中 |
| 入力 | アクティビティの入力を記述する JSON 配列 |
| Output | アクティビティの出力を記述する JSON 配列 |
| Error | アクティビティの実行エラー (発生した場合) |

![アクティビティの実行を監視する](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> パイプラインとアクティビティの実行一覧を更新するには、上部の **[更新]** アイコンをクリックする必要があります。 現在、自動更新はサポートされていません。

![更新](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>監視するデータ ファクトリの選択
左上の **[Data Factory]** アイコンにマウス ポインターを移動します。 [矢印] アイコンをクリックすると、監視できる Azure サブスクリプションと Data Factory のリストが表示されます。

![データ ファクトリの選択](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>リスト ビューを構成する

### <a name="apply-rich-ordering-and-filtering"></a>高度な並べ替えとフィルター処理を適用する

パイプラインの実行順序は、[Run Start]\(実行の開始\) で昇順または降順で並べ替えます。パイプラインの実行は次の列でフィルターします。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前。 オプションには [過去 24 時間]、[先週]、[過去 30 日間] のクイック フィルターがあります。または、カスタムの日時を選択します。 |
| Run Start (実行の開始) | パイプラインの実行の開始日時 |
| Run Status (ジョブの状態) | 状態 (成功、失敗、進行中) で実行をフィルター処理します |

![filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>列を追加または削除する
リスト ビューの見出しを右クリックし、リスト ビューに表示する列を選択します。

![列](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>列幅を調整する
リスト ビューの列幅を増減するには、列見出しにマウス ポインターを移動します。

## <a name="promote-user-properties-to-monitor"></a>監視対象のユーザー プロパティを昇格する

監視可能なエンティティになるように、任意のパイプラインのアクティビティ プロパティをユーザー プロパティとして昇格できます。 たとえば、パイプラインのコピー アクティビティの **Source** プロパティと **Destination** プロパティをユーザー プロパティとして昇格できます。 また、 **[自動生成]** を選択して、コピー アクティビティの **Source** および **Destination** ユーザー プロパティを生成することもできます。

![ユーザー プロパティの作成](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> ユーザー プロパティとして昇格できるのは、最大 5 つのパイプライン アクティビティ プロパティのみです。

作成したユーザー プロパティは、監視リスト ビューで監視することができます。 コピー アクティビティのソースがテーブル名の場合、アクティビティの実行リスト ビューでソースのテーブル名を列として監視できます。

![ユーザー プロパティを持たないアクティビティの実行リスト](media/monitor-visually/monitor-user-properties-image2.png)

![アクティビティ実行の一覧にユーザー プロパティの列を追加する](media/monitor-visually/monitor-user-properties-image3.png)

![アクティビティ実行の一覧とユーザー プロパティの列](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>パイプライン内のアクティビティを再実行する

パイプライン内のアクティビティを再実行できる状態になりました。 **[View activity runs]\(アクティビティの実行の表示\)** をクリックし、パイプラインからその再実行の起点となるアクティビティを選択します。

![アクティビティの実行の表示](media/monitor-visually/rerun-activities-image1.png)

![アクティビティの実行の選択](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>再実行履歴を表示する

パイプラインの全実行の再実行履歴は、リスト ビューで確認することができます。

![履歴を表示する](media/monitor-visually/rerun-history-image1.png)

特定のパイプラインの実行について、再実行履歴を確認することもできます。

![パイプラインの実行履歴の表示](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>ガント ビュー

ガント ビューを使用すると、パイプラインとアクティビティの実行をすばやく可視化できます。 ガント ビューをパイプラインごとに表示することも、パイプラインで作成した注釈やタグでグループ化することもできます。

![ガント チャート](media/monitor-visually/gantt1.png)

![ガント チャートの注釈](media/monitor-visually/gantt2.png)

バーの長さはパイプラインの期間を示します。 バーをクリックして詳細を表示することもできます。

![ガント チャートの期間](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>ガイド ツアー
左下の [情報] アイコンをクリックし、[Guided Tours]\(ガイド ツアー\) をクリックすると、パイプラインとアクティビティの実行を監視する方法に関する手順が表示されます。

![ガイド ツアー](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>フィードバック
[フィードバック] アイコンをクリックして、さまざまな機能や、発生している問題についてフィードバックをお寄せください。

![フィードバック](media/monitor-visually/feedback.png)

## <a name="alerts"></a>アラート

Data Factory で、サポートされるメトリックに対してアラートを生成できます。 Data Factory の [監視] ページで **[監視]、[Alerts & Metrics]\(アラートとメトリック\)**   の順に選択して開始します。

![](media/monitor-visually/alerts01.png)

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>アラートを作成する

1.  **[+ 新しいアラート ルール]**   をクリックして新しいアラートを作成します。

    ![](media/monitor-visually/alerts02.png)

1.  ルール名を指定し、アラートの **[重大度]** を選択します。

    ![](media/monitor-visually/alerts03.png)

1.  [アラートの条件] を選択します

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  [アラート ロジック] を構成します。 すべてのパイプラインと対応するアクティビティについて、選択したメトリックのアラートを作成できます。 また、特定のアクティビティの種類、アクティビティ名、パイプライン名、またはエラーの種類を選択することもできます。

    ![](media/monitor-visually/alerts06.png)

1.  アラートの**メール/SMS/プッシュ/音声**通知を構成します。 アラートの通知について **[アクション グループ]** を作成するか、既存のものを選択します。

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  アラート ルールを作成します。

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>次の手順

パイプラインの監視と管理の詳細については、「[Azure Data Factory をプログラムで監視する](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)」を参照してください。
