---
title: Metrics Advisor でデータ フィードを管理する
titleSuffix: Azure Cognitive Services
description: Metrics Advisor に追加したデータ フィードを管理する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184724"
---
# <a name="how-to-manage-your-data-feeds"></a>方法:データ フィードを管理する

Metrics Advisor でオンボード データ フィードを管理する方法について説明します。 この記事では、Metrics Advisor でデータ フィードを管理する手順について説明します。

## <a name="edit-a-data-feed"></a>データ フィードを編集する

> [!NOTE]
> 次の詳細をデータ フィードの作成後に変更することはできません。 
> * データ フィード ID
> * Created Time
> * Dimension
> * ソースの種類
> * 粒度

データ フィードの管理者だけが変更を加えることができます。 

データ フィードを一時停止または再アクティブ化するには:

1. データ フィードの一覧ページで、データ フィードに対して実行する操作をクリックします。

2. データ フィード詳細ページで、 **[状態]** 切り替えボタンをクリックします。

データ フィードを削除するには: 

1. データ フィードの一覧ページで、データ フィードに対して **[削除]** をクリックします。

2. データ フィードの詳細ページで、 **[削除]** をクリックします。

開始時刻を変更するときは、もう一度スキーマを確認する必要があります。 これを変更するには、 **[パラメーターの編集]** を使用します。

##  <a name="backfill-your-data-feed"></a>データ フィードをバックフィルする

**[Backfill]\(バックフィル\)** ボタンを選択すると、タイムスタンプですぐにインジェストを開始したり、失敗したインジェストを修正したり、既存のデータをオーバーライドしたりできます。
- 開始時刻は含まれます。
- 終了時刻は含まれません。
- 異常検出は選択された範囲でのみ再トリガーされます。

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="データ フィードをバックフィルする":::

## <a name="manage-permission-of-a-data-feed"></a>データ フィードのアクセス許可を管理する

ワークスペースへのアクセスは、認証に Azure Active Directory を使用する Metrics Advisor リソースによって制御されます。 メトリック データには、別のアクセス許可制御レイヤーが適用されます。

Metrics Advisor を使用すると、さまざまなデータ フィードに対するアクセス許可をさまざまなユーザー グループに付与できます。 ロールには次の 2 種類があります。 

- 管理者: 変更や削除など、データ フィードを管理するための完全なアクセス許可を持つユーザー。
- 閲覧者:データ フィードの読み取り専用ビューにアクセスできるユーザー。
 

## <a name="advanced-settings"></a>詳細設定

新しいデータ フィードを作成する場合、オプションの詳細設定がいくつかあります。これらはデータ フィードの詳細ページで変更できます。

### <a name="ingestion-options"></a>インジェスト オプション

* **Ingestion time offset\(インジェスト時間のオフセット\)** :既定では、データは指定された細分性に従ってインジェストされます。 たとえば、"*日単位*" のタイムスタンプを持つメトリックは、タイムスタンプの 1 日後にインジェストされます。 オフセットを使用して、"*正*" の数でインジェストの時刻を遅らせることも、"*負*" の数で早めることもできます。

* **最大コンカレンシー**:制限された同時実行がデータ ソースでサポートされている場合は、このパラメーターを設定します。 それ以外の場合は、既定の設定のままにします。

* **Stop Retry After\(再試行の停止までの時間\)** :データ インジェストに失敗した場合は、一定時間内に自動的に再試行されます。 その時間の開始点は、最初のデータ インジェストが発生した時刻です。 時間の長さは、細分性に従って定義されます。 既定値 (-1) のままにした場合、値は次のように細分性に従って決定されます。
    
    | 粒度       | Stop Retry After\(再試行の停止までの時間\)           |
    | :------------ | :--------------- |
    | 毎日、カスタム (1 日以上)、毎週、毎月、毎年     | 7 日          |
    | 毎時、カスタム (1 日未満)       | 72 時間 |

* **Minimum Retry Interval\(最小再試行間隔\)** :ソースからのデータのプルを再試行するときの最小間隔を指定できます。 既定値 (-1) のままにした場合、再試行間隔は次のように細分性に従って決定されます。
    
    | 粒度       | Minimum Retry Interval\(再試行の最小間隔\)           |
    | :------------ | :--------------- |
    | 毎日、カスタム (1 日以上)、毎週、毎月     | 30 分          |
    | 毎時、カスタム (1 日未満)      | 10 分 |
    | 年単位 | 1 日          |
 
### <a name="fill-gap-when-detecting"></a>Fill gap when detecting\(検出時の欠損の埋め合わせ\): 

> [!NOTE]
> この設定はデータ ソースに影響を与えず、ポータルに表示されるデータ グラフに影響を与えません。 自動入力は異常検出中にのみ行われます。

一部の時系列が連続していません。 一部のデータ ポイントが欠損している場合、Metrics Advisor では指定された値を使用して、精度を高めるために異常検出の前にそれらの設定が行われます。
オプションは次のとおりです。 

* 以前の実際のデータ ポイントの値を使用します。 既定ではこれが使用されます。
* 特定の値を使用します。

### <a name="action-link-template"></a>アクション リンク テンプレート: 

アクション リンク テンプレートは、実行可能な HTTP URL を事前に定義するために使用されます。これは、プレースホルダー `%datafeed`、`%metric`、`%timestamp`、`%detect_config`、および `%tagset` で構成されます。 テンプレートを使用して、異常またはインシデントから特定の URL にリダイレクトし、ドリルダウンすることができます。

:::image type="content" source="../media/action-link-template.png" alt-text="アクション リンク テンプレート" lightbox="../media/action-link-template.png":::

アクション リンクを入力したら、インシデントの一覧のアクション オプションで **[Go to action link]\(アクション リンクに移動\)** をクリックし、インシデント ツリーの右クリック メニューをクリックします。 アクション リンク テンプレート内のプレースホルダーを、異常またはインシデントの対応する値に置き換えます。

| プレースホルダー | 例 | コメント |
| ---------- | -------- | ------- |
| `%datafeed` | - | データ フィード ID |
| `%metric` | - | メトリック ID |
| `%detect_config` | - | 構成 ID の検出 |
| `%timestamp` | - | 異常のタイムスタンプ、または永続的なインシデントの終了時刻 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | インシデントの異常または最上位異常のディメンション値。   <br> `filterVal` は、角かっこ内の一致する値をフィルターで除外するために使用されます。   |

例:

* アクション リンク テンプレートが `https://action-link/metric/%metric?detectConfigId=%detect_config` の場合、
  * アクション リンク `https://action-link/metric/1234?detectConfigId=2345` はメトリック `1234` の異常またはインシデントにアクセスし、構成 `2345` が検出されます。

* アクション リンク テンプレートが `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` の場合、 
    * 異常が `{ "Dim1": "Val1", "Dim2": "Val2" }` のとき、アクション リンクは `https://action-link?Dim1=Val1&Dim2=Val2` になります。 
    * ディメンション値の空の文字列に対して `[Dim1=***&]` がスキップされるため、異常が `{ "Dim1": "", "Dim2": "Val2" } ` のとき、アクション リンクは `https://action-link?Dim2=Val2` になります。 

* アクション リンク テンプレートが `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` の場合、 
    * 異常が `{ "Dim1": "Val1", "Dim2": "Val2" }` のとき、アクション リンクは `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` になります。 
    * ディメンション値の空の文字列に対して `[Name/Dim1 eq '***' and ]` がスキップされるため、異常が `{ "Dim1": "", "Dim2": "Val2" }` のとき、アクション リンクは `https://action-link?filter=Name/Dim2 eq 'Val2'` になります。 
   
### <a name="data-feed-not-available-alert-settings"></a>"Data feed not available (データ フィードを使用できません)" アラートの設定

データ フィードによるインジェストの開始時から指定された猶予期間内にソースからデータがインジェストされていない場合、データ フィードは使用不可と見なされます。 この場合は、アラートがトリガーされます。

アラートを構成するには、最初に[フックを作成する](alerts.md#create-a-hook)必要があります。 アラートは、構成されているフックを介して送信されます。

* **猶予期間**:[猶予期間] 設定は、データ ポイントがインジェストされない場合にアラートを送信するタイミングを決定するために使用されます。 参照ポイントは、最初のインジェストの時刻です。 インジェストが失敗した場合、Metrics Advisor では、細分性によって指定された一定の間隔で試行が継続されます。 猶予期間が切れて失敗し続ける場合は、アラートが送信されます。

* **Auto snooze (自動再通知)** :このオプションが 0 に設定されている場合は、"*使用不可*" のタイムスタンプによって警告がトリガーされます。 0 以外の設定が指定されている場合、"*使用不可*" の最初のタイムスタンプの後の連続するタイムスタンプは、指定された設定に従ってトリガーされません。

## <a name="next-steps"></a>次のステップ
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
- [フィードバックを使用して異常検出を調整する](anomaly-feedback.md)
- [インシデントを診断する](diagnose-incident.md)
