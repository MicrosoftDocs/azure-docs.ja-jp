---
title: Azure portal でのディスク メトリックの非推奨 | Microsoft Docs
description: 非推奨となったディスク メトリックと、新しいメトリックを使用するためのメトリック アラートの更新方法について説明します。
services: azure-monitor
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 607ccacbfff86decaecaad0a869c8c0fe7500680
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033403"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure portal でのディスク メトリックの非推奨

非推奨となったディスク関連メトリックは、間もなく Azure portal から削除されます。 非推奨となった各メトリックの新しいバージョンを使用できます。 この記事では、新しいメトリックと、それらを使用するためのメトリック アラートの更新方法を示します。

## <a name="list-of-new-metrics"></a>新しいメトリックの一覧

この表では、非推奨となった各メトリックが、対応する新しいメトリックにマップされています。 

|非推奨のメトリック|新しい (置換) メトリック|
|----|----|
|データ ディスク QD (非推奨)|データ ディスクのキューの深さ (プレビュー)|
|データ ディスク読み取りバイト数/秒 (非推奨)|データ ディスク読み取りバイト数/秒 (プレビュー)|
|データ ディスク読み取り操作数/秒 (非推奨)|データ ディスク読み取り操作数/秒 (プレビュー)|
|データ ディスク書き込みバイト数/秒 (非推奨)|データ ディスク書き込みバイト数/秒 (プレビュー)|
|データ ディスク書き込み操作数/秒 (非推奨)|データ ディスク書き込み操作数/秒 (プレビュー)|
|OS QD (非推奨)|OS キューの深さ (プレビュー)|
|OS 読み取りバイト数/秒 (非推奨)|OS 読み取りバイト数/秒 (プレビュー)|
|OS 読み取り操作数/秒 (非推奨)|OS 読み取り操作数/秒 (プレビュー)|
|OS 書き込みバイト数/秒 (非推奨)|OS 書き込みバイト数/秒 (プレビュー)|
|OS 書き込み操作数/秒 (非推奨)|OS 書き込み操作数/秒 (プレビュー)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>メトリック アラートでメトリックを移行する

新しいメトリックを使用するようにメトリック アラートを更新します。

1. Azure portal で、「**アラート**」 を検索します。 次に、 **[サービス]** セクションで、 **[アラート]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![アラート サービス](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. **[アラート]** ページで、 **[アラート ルール の管理]** ボタンを選択します。 

   > [!div class="mx-imgBorder"]
   > ![アラート ルールの管理](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. **[リソース グループ]** ドロップダウン リストから **[Virtual Machines]** チェックボックスを選択し、 **[Signal type]\(シグナルの種類\)** ドロップダウン リストから **[メトリック]** チェックボックスを選択します。 

   > [!div class="mx-imgBorder"]
   > ![アラートのフィルター処理](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. メトリックの一覧で、ディスクに関連する条件を特定します。 ルールの名前をクリックします。 

   名前は、テーブルの **[名前]** 列にハイパーリンクとして表示されます。

   > [!div class="mx-imgBorder"]
   > ![ディスク条件を見つける](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. **[ルール管理]** ページの **[条件]** セクションで、アラートの条件をクリックします。 

   条件はハイパーリンクとして表示されます。  

   > [!div class="mx-imgBorder"]
   > ![条件を調整する](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   **[シグナル ロジックの構成]** ページが表示され、条件の設定がそのページの **[アラート ロジック]** セクションに表示されます。

6. これらの設定は、非推奨のメトリックを削除すると表示されなくなるため、記録しておいてください。

   > [!div class="mx-imgBorder"]
   > ![条件の規則](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > これらの設定は、スクリーンショットまたはテキスト ファイルでキャプチャすることを検討してください。 

7. **[シグナルの選択に戻る]** リンクをクリックします。

   > [!div class="mx-imgBorder"]
   > ![シグナルの選択に戻る](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. **[シグナル ロジックの構成]** ページで、適切な置換メトリック (新しいメトリック) を選択します。 この記事の前半にある[表](#update-metrics)を使用して、新しいメトリックの名前を識別します。

   > [!TIP] 
   > 検索バーへの入力を開始して、メトリック名の一覧を絞り込みます。 

   > [!div class="mx-imgBorder"]
   > ![新しいメトリックの選択](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. **[完了]** ボタンを選択します。 

   > [!div class="mx-imgBorder"]
   > ![新しいメトリックの設定](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. **[保存]** ボタンを選択して変更をコミットします。 

    > [!div class="mx-imgBorder"]
    > ![新しいメトリックの保存](./media/portal-disk-metrics-deprecation/save-new-metric.png)






