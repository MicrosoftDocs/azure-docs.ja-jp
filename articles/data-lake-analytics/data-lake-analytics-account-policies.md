---
title: Azure Data Lake Analytics アカウント ポリシーを管理する
description: AU やジョブの最大数など、アカウント ポリシーを使用して Data Lake Analytics アカウントの使用を制御する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013311"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>アカウント ポリシーを使用して Azure Data Lake Analytics を管理する

アカウント ポリシーを使用すると、Azure Data Lake Analytics アカウントのリソースの使用方法を制御できます。 これらのポリシーを使用すると、Azure Data Lake Analytics を使用するコストを制御できます。 たとえば、これらのポリシーを使用すると、アカウントで同時に使用できる AU 数を制限することで、予期しないコストの急増を防ぐことができます。## アカウントレベルのポリシー

これらのポリシーは、Data Lake Analytics アカウント内のすべてのジョブに適用されます。

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントでの AU の最大数

ポリシーを使用して、Data Lake Analytics アカウントが使用できる Analytics ユニット (AU) の合計数を制御します。 既定では、値は 250 に設定されます。 たとえば、この値が 250 AU に設定されている場合、250 AU が割り当てられている 1 つのジョブを実行するか、それぞれ 25 AU が割り当てられている 10 個のジョブを実行することができます。 送信される追加のジョブは、実行中のジョブが完了するまで、キューに登録されます。 実行中のジョブが完了すると、キューに登録されているジョブを実行するために AU が解放されます。

Data Lake Analytics アカウントの AU の数を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[制限とポリシー]** をクリックします。
3. **[最大 AU]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。
4. **[保存]** をクリックします。

   > [!NOTE]
   > 既定値 (250) より多い AU が必要な場合は、ポータルで **[ヘルプとサポート]** をクリックし、サポート要求を送信します。 Data Lake Analytics アカウントで利用できる AU の数は、増やすことができます。

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>同時に実行できるジョブの最大数

このポリシーを使うと、同時に実行できるジョブ数が制限されます。 既定では、この値は 20 に設定されます。 Data Lake Analytics に利用可能な AU がある場合は、実行中のジョブの合計数がこのポリシーの値に達するまで、新しいジョブがすぐに実行されるようにスケジュールされます。 同時に実行できるジョブの最大数に達すると、後続のジョブは、1 つ以上の実行中のジョブ (使用できる AU に応じて変わります) が完了するまで、優先度に従ってキューに登録されます。

同時に実行できるジョブの最大数を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[制限とポリシー]** をクリックします。
3. **[実行ジョブの最大数]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。
4. **[保存]** をクリックします。

   > [!NOTE]
   > 既定値 (20) より多い数のジョブを実行する必要がある場合は、ポータルで **[ヘルプとサポート]** をクリックし、サポート要求を送信します。 Data Lake Analytics アカウントで同時に実行できるジョブの数は、増やすことができます。

## <a name="how-long-to-keep-job-metadata-and-resources"></a>ジョブのメタデータとリソースを保持する期間

ユーザーが U-SQL ジョブを実行すると、Data Lake Analytics サービスによって関連するすべてのファイルが保持されます。 こうしたファイルには、U-SQL スクリプト、U-SQL スクリプトで参照される DLL ファイル、コンパイルされたリソース、統計情報が含まれます。 ファイルは、既定の Azure Data Lake ストレージ アカウントの /system/ フォルダーにあります。 このポリシーでは、これらのリソースが格納されてから自動的に削除されるまでの期間を制御します (既定値は 30 日間です)。 これらのファイルは、デバッグのほか、今後再実行するジョブのパフォーマンス チューニングのために使用できます。

ジョブのメタデータとリソースを保持する期間を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[制限とポリシー]** をクリックします。
3. **[ジョブ クエリを保持する日数]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。  
4. **[保存]** をクリックします。

## <a name="job-level-policies"></a>ジョブレベルのポリシー

ジョブレベルのポリシーを使うと、個々のユーザー (または特定のセキュリティ グループのメンバー) が自分の送信するジョブに設定できる最大 AU と最大優先度を制御できます。 このポリシーで、ユーザーによって生じるコストを制御できます。 また、スケジュールされたジョブが同じ Data Lake Analytics アカウントで実行されている優先度の高い実稼働ジョブに与える可能性がある影響を制御することもできます。

Data Lake Analytics には、ジョブ レベルで設定できる次の 2 つのポリシーがあります。

- **ジョブあたりの AU 制限**: ユーザーは、最大でこの AU 数になる範囲でのみ、ジョブを送信できます。 既定では、この制限はアカウントの最大 AU 制限と同じです。

- **[優先度]** :ユーザーは、優先度がこの値以下のジョブだけを送信できます。 値が大きいほど優先度が低くなります。 この制限は既定では 1 に設定されています。これは、最も高い優先度です。

すべてのアカウントに、既定のポリシー セットがあります。 既定のポリシーは、アカウントのすべてのユーザーに適用されます。 特定のユーザーとグループに対して、追加のポリシーを作成することができます。

> [!NOTE]
> アカウントレベルのポリシーとジョブレベルのポリシーは、同時に適用されます。

## <a name="add-a-policy-for-a-specific-user-or-group"></a>特定のユーザーまたはグループへのポリシーの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。

2. **[制限とポリシー]** をクリックします。

3. **[ジョブの送信の制限]** で、 **[ポリシーの追加]** ボタンをクリックします。 次に、以下の設定を選択するか入力します。

   1. **[計算ポリシー名]** :ポリシーの目的がわかるようなポリシー名を入力します。

   2. **[ユーザーまたはグループの選択]** :このポリシーの適用対象となるユーザーまたはグループを選択します。

   3. **[ジョブ AU の制限を設定します]** :選択したユーザーまたはグループに適用される AU の制限を設定します。

   4. **[Set the Priority Limit]\(優先度の制限を設定します\)** :選択したユーザーまたはグループに適用される優先度の制限を設定します。

4. **[OK]** をクリックします。

5. **[ジョブの送信の制限]** の下にある **[既定]** ポリシー テーブルの一覧に、新しいポリシーが表示されます。

## <a name="delete-or-edit-an-existing-policy"></a>既存のポリシーの削除または編集

1. Azure Portal で Data Lake Analytics アカウントに移動します。

2. **[制限とポリシー]** をクリックします。

3. **[ジョブの送信の制限]** で、編集するポリシーを検索します。

4. **[削除]** および **[編集]** オプションを表示するには、テーブルの右端の列で `...` をクリックします。## ジョブ ポリシーの追加リソース

- [ポリシーの概要に関するブログの投稿](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [アカウントレベルのポリシーに関するブログの投稿](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [ジョブレベルのポリシーに関するブログの投稿](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>次のステップ

- [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-powershell.md)