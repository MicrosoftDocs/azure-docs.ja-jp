---
title: 移行アクティビティの監視 - Azure Database Migration Service
description: Azure Database Migration Service を使用して移行アクティビティを監視する方法について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297472"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Azure Database Migration Service を使用して移行アクティビティを監視する
この記事では、データベース レベルとテーブル レベルの両方で移行の進行状況を監視する方法について説明します。

## <a name="monitor-at-the-database-level"></a>データベース レベルの監視
データベース レベルでアクティビティを監視するには、データベースレベルのブレードを表示します。

![データベースレベルのブレード](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> データベースのハイパーリンクを選択すると、テーブルの一覧と移行の進行状況が表示されます。

次の表では、データベースレベルのブレードにあるフィールドを示すほか、各フィールドに関連したさまざまな状態の値について説明します。

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>フィールド名</strong></th>
      <th><strong>フィールドの副状態</strong></th>
      <th><strong>説明</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>アクティビティの状態</strong></td>
      <td>実行中</td>
      <td>移行アクティビティが実行されています。</td>
    </tr>
    <tr>
      <td>成功</td>
      <td>移行アクティビティが問題なく成功しました。</td>
    </tr>
    <tr>
      <td>フォールト</td>
      <td>移行に失敗しました。 移行の詳細にある [エラー詳細の参照] リンクを選択すると、完全なエラー メッセージが表示されます。</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>状態</strong></td>
      <td>初期化中</td>
      <td>DMS によって移行パイプラインが設定されています。</td>
    </tr>
    <tr>
      <td>実行中</td>
      <td>DMS パイプラインが実行され、移行が行われています。</td>
    </tr>
    <tr>
      <td>完了</td>
      <td>移行が完了しました。</td>
    </tr>
    <tr>
      <td>失敗</td>
      <td>移行に失敗しました。 移行の詳細をクリックすると、移行のエラーが表示されます。</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>移行の詳細</strong></td>
      <td>移行パイプラインを初期化しています</td>
      <td>DMS によって移行パイプラインが設定されています。</td>
    </tr>
    <tr>
      <td>全体のデータを読み込み中です</td>
      <td>DMS によって初期読み込みが実行されています。</td>
    </tr>
    <tr>
      <td>一括準備完了</td>
      <td>初期読み込みの完了後、DMS によって、データベースが一括準備完了としてマークされます。 ユーザーは、データが継続的同期に追い付いているかどうかをチェックする必要があります。</td>
    </tr>
    <tr>
      <td>すべての変更が適用されました</td>
      <td>初期読み込みと継続的同期が完了しました。 データベースの一括移行が正常に完了した後もこの状態になります。</td>
    </tr>
    <tr>
      <td>エラー詳細の参照</td>
      <td>リンクをクリックすると、エラーの詳細が表示されます。</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>期間</strong></td>
      <td>該当なし</td>
      <td>移行アクティビティが初期化されてから移行が完了するまで、または移行が失敗するまでの合計時間。</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>テーブル レベルの監視 - 簡単な概要
テーブル レベルでアクティビティを監視するには、テーブルレベルのブレードを表示します。 ブレードの上部には、全体の読み込みと増分更新で移行された行の詳細な数が表示されます。 

ブレードの下部には、テーブルが一覧表示されるほか、移行の進行状況の簡単な概要が表示されます。

![テーブルレベルのブレード - 簡単な概要](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

次の表では、テーブルレベルの詳細で表示されるフィールドについて説明します。

| フィールド名        | 説明       |
| ------------- | ------------- |
| **全体の読み込みが完了しました**      | 全体のデータ読み込みが完了したテーブルの数。 |
| **全体の読み込みがキューに登録されました**      | 全体の読み込みでキューに登録されているテーブルの数。      |
| **全体の読み込みの実行中** | 失敗したテーブルの数。      |
| **増分更新**      | ターゲットに適用された行に含まれている変更データ キャプチャ (CDC) 更新の数。 |
| **増分挿入**      | ターゲットに適用された行に含まれている CDC 挿入の数。      |
| **増分削除** | ターゲットに適用された行に含まれている CDC 削除の数。      |
| **保留中の変更**      | ターゲットに適用されるのをまだ待機している行の CDC 挿入の数。 |
| **変更が適用されました**      | ターゲットに適用された行に含まれている CDC の更新、挿入、削除の総数。      |
| **エラー状態のテーブル** | 移行中の状態が "エラー" であるテーブルの数。 テーブルがエラー状態になる可能性がある場合の例を挙げると、ターゲットで重複が特定された場合や、データがターゲット テーブルへの読み込みに対応していない場合などがあります。      |

## <a name="monitor-at-table-level--detailed-summary"></a>テーブル レベルの監視 - 詳細な概要
全体の読み込みと増分データ同期での移行の進行状況を示す 2 つのタブがあります。
    
![[全体の読み込み] タブ](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![[増分データ同期] タブ](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

次の表では、テーブル レベルの移行の進行状況で表示されるフィールドについて説明します。

| フィールド名        | 説明       |
| ------------- | ------------- |
| **状態 - 同期中**      | 継続的同期が実行されています。 |
| **[挿入]**      | ターゲットに適用された行に含まれている CDC 挿入の数。      |
| **アップデート** | ターゲットに適用された行に含まれている CDC 更新の数。      |
| **削除**      | ターゲットに適用された行に含まれている CDC 削除の数。 |
| **適用の合計**      | ターゲットに適用された行に含まれている CDC の更新、挿入、削除の総数。 |
| **データ エラー** | このテーブルで発生したデータ エラーの数。 エラーの例には、"*511: 1 行のサイズ %d が許容最大行サイズ %d を超えているので行を作成できません" や "8114: データ型 %ls を %ls に変換中にエラーが発生しました*" などがあります。  Azure ターゲットの dms_apply_exceptions テーブルでクエリを実行して、エラーの詳細を確認する必要があります。    |

> [!NOTE]
> [挿入]、[更新]、[削除]、[適用の合計] の CDC 値は、データベースが一括移行されたとき、または移行が再起動されたときに減少することがあります。

## <a name="next-steps"></a>次のステップ
- 「[Microsoft Database Migration Guide](https://datamigration.microsoft.com/)」にある移行ガイドを確認する。