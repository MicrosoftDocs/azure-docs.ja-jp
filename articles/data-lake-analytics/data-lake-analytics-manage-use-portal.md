---
title: "Azure Portal を使用した Azure Data Lake Analytics の管理 | Microsoft Docs"
description: "Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブの管理方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: e49d1a0e0ccc6567d0a6841817667717ff5dba76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Azure Portal を使用した Azure Data Lake Analytics の管理
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure Portal を使用して、Azure Data Lake Analytics のアカウント、アカウント データ ソース、ユーザー、ジョブを管理する方法について説明します。 他のツールを使用した管理のトピックを表示する場合は、ページの上部にあるタブをクリックしてください。

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントの管理

### <a name="create-an-account"></a>アカウントの作成

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]** > **[インテリジェンス + 分析]** > **[Data Lake Analytics]** の順にクリックします。
3. 次の項目の値を選択します。 
   1. **[名前]**: Data Lake Analytics アカウントの名前。
   2. **[サブスクリプション]**: アカウントに使用する Azure サブスクリプション。
   3. **[リソース グループ]**: アカウントの作成先の Azure リソース グループ。 
   4. **[場所]**: Data Lake Analytics アカウントの Azure データセンター。 
   5. **[Data Lake Store]**: Data Lake Analytics アカウントのために使用される既定のストア。 Azure Data Lake Store アカウントと Data Lake Analytics アカウントは、同じ場所にある必要があります。
4. **Create** をクリックしてください。 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントの削除

Data Lake Analytics アカウントを削除する前に、その既定の Data Lake Store アカウントを削除します。

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. [ **削除**] をクリックします。
3. アカウント名を入力します。
4. [ **削除**] をクリックします。

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>データ ソースを管理する

Data Lake Analytics では、次のデータ ソースがサポートされています。

* Data Lake Store
* Azure Storage (Azure Storage)

データ エクスプローラーを使用して、データ ソースを参照し、基本的なファイル管理操作を実行することができます。 

### <a name="add-a-data-source"></a>データ ソースの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[データ ソース]** をクリックします。
3. **[データ ソースの追加]**をクリックします。
    
   * Data Lake Store アカウントを追加するには、アカウント名と、クエリを実行できるようにするためのアカウントへのアクセスが必要です。
   * Azure Blob Storage を追加するには、ストレージ アカウントとアカウント キーが必要です。 それらを探すには、ポータルでストレージ アカウントに移動します。

## <a name="set-up-firewall-rules"></a>ファイアウォール規則の設定

Data Lake Analytics を使用すると、ネットワーク レベルで Data Lake Analytics アカウントへのアクセスをさらにロックダウンできます。 ファイアウォールを有効にして、IP アドレスを指定するか、信頼されているクライアントの IP アドレス範囲を定義することができます。 こうした方法を有効にすると、定義された範囲内の IP アドレスを使用しているクライアントだけがストアに接続できます。

Azure Data Factory や VM など、他の Azure サービスが Data Lake Analytics アカウントに接続する場合は、**[Allow Azure Services]\(Azure サービスを許可する\)** が **[オン]** になっていることを確認してください。 

### <a name="set-up-a-firewall-rule"></a>ファイアウォール規則の設定

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. 左側のメニューで **[ファイアウォール]** をクリックします。

## <a name="add-a-new-user"></a>新しいユーザーの追加

**ユーザー追加ウィザード**を使用すると、新しい Data Lake ユーザーを簡単にプロビジョニングできます。

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. 左側の **[作業の開始]** で、**[ユーザー追加ウィザード]** をクリックします。
3. ユーザーを選択し、**[選択]** をクリックします。
4. ロールを選択し、**[選択]** をクリックします。 Azure Data Lake を使用する新しい開発者を設定するには、**Data Lake Analytics の開発者**ロールを選択します。
5. U-SQL データベースのアクセス制御リスト (ACL) を選択します。 正しく選択できたら、**[選択]** をクリックします。
6. ファイルの ACL を選択します。 既定のストアの場合は、ルート フォルダー "/" と /system フォルダーの ACL を変更しないでください。 **[選択]**をクリックします。
7. 選択したすべての変更を確認し、**[実行]** をクリックします。
8. ウィザードが終了したら、**[完了]** をクリックします。

## <a name="manage-role-based-access-control"></a>ロールベースのアクセス制御の管理

他の Azure サービスと同様に、ロールベースのアクセス制御 (RBAC) を使用して、ユーザーがサービスと対話する方法を制御できます。

標準の RBAC ロールには、次のような機能があります。
* **所有者**: ジョブの送信、ジョブの監視、任意のユーザーのジョブの取り消し、アカウントの構成を行えます。
* **共同作成者**: ジョブの送信、ジョブの監視、任意のユーザーのジョブの取り消し、アカウントの構成を行えます。
* **閲覧者**: ジョブの監視を行えます。

U-SQL 開発者が Data Lake Analytics サービスを使用できるようにするには、Data Lake Analytics の開発者ロールを使用します。 Data Lake Analytics の開発者ロールを使用すると、次の操作を行えます。
* ジョブを送信する。
* ジョブの状態と、任意のユーザーによって送信されたジョブの進行状況を監視する。
* 任意のユーザーによって送信されたジョブの U-SQL スクリプトを確認する。
* 自分のジョブだけを取り消す。

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントへのユーザーまたはセキュリティ グループの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[アクセス制御 (IAM)]** > **[追加]** の順にクリックします。
3. ロールを選択します。
4. ユーザーを追加します。
5. **[OK]**をクリックします。

>[!NOTE]
>ユーザーまたはセキュリティ グループがジョブを送信する必要がある場合は、ストア アカウントに対するアクセス許可も必要です。 詳細については、[Data Lake Store に格納されているデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)に関する記事を参照してください。
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>ジョブの管理

### <a name="submit-a-job"></a>ジョブの送信

1. Azure Portal で Data Lake Analytics アカウントに移動します。

2. **[新しいジョブ]**をクリックします。 各ジョブで、以下の項目を構成します。

    1. **[ジョブ名]**: ジョブの名前。
    2. **[優先度]**: 数値が小さいほど優先度が高くなります。 2 つのジョブがキューに登録されている場合は、優先度の値が小さい方のジョブが先に実行されます。
    3. **[並行処理]**: このジョブのために予約する計算プロセスの最大数。

3. **[ジョブの送信]**をクリックします。

### <a name="monitor-jobs"></a>ジョブの監視

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[すべてのジョブの表示]** をクリックします。 アカウントのすべてのアクティブなジョブと最近終了したジョブの一覧が表示されます。
3. 必要に応じて **[フィルター]** をクリックして、**[時間の範囲]**、**[ジョブ名]**、および **[作成者]** の値でジョブを見つけやすくします。 

### <a name="monitoring-pipeline-jobs"></a>パイプライン ジョブの監視
パイプラインの一部であるジョブは、特定のシナリオを実現するために、通常は順次実行されます。 たとえば、Customer Insights 用に利用状況をクリーンアップ、抽出、変換、集約するパイプラインがあります。 パイプライン ジョブは、ジョブの送信時に "Pipeline" プロパティを使用して識別されます。 ADF V2 を使用してスケジュールされたジョブには、このプロパティが自動的に設定されます。 

パイプラインの一部である U-SQL ジョブの一覧を表示するには、次の手順を実行します。 

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[ジョブ洞察]** をクリックします。 既定では [すべてのジョブ] タブに移動し、実行中のジョブ、キューに登録済みのジョブ、終了したジョブの一覧が表示されます。
3. **[パイプライン ジョブ]** タブをクリックします。パイプライン ジョブの一覧が、各パイプラインの集計された統計と共に表示されます。

### <a name="monitoring-recurring-jobs"></a>定期的なジョブの監視
定期的なジョブとは、ビジネス ロジックは同一で、入力データが実行するたびに異なるジョブです。 定期的なジョブは、常に成功し、実行時間も比較的安定していることが理想的です。定期的なジョブの動作を監視することは、ジョブの正常性を保つのに役立ちます。 定期的なジョブは、"Recurrence" プロパティを使用して識別されます。 ADF V2 を使用してスケジュールされたジョブには、このプロパティが自動的に設定されます。

定期的に実行される U-SQL ジョブの一覧を表示するには、次の手順を実行します。 

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[ジョブ洞察]** をクリックします。 既定では [すべてのジョブ] タブに移動し、実行中のジョブ、キューに登録済みのジョブ、終了したジョブの一覧が表示されます。
3. **[定期的なジョブ]** タブをクリックします。定期的なジョブの一覧が、それぞれのジョブの集計された統計と共に表示されます。

## <a name="manage-policies"></a>ポリシーの管理

### <a name="account-level-policies"></a>アカウントレベルのポリシー

これらのポリシーは、Data Lake Analytics アカウント内のすべてのジョブに適用されます。

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントでの AU の最大数
ポリシーを使用して、Data Lake Analytics アカウントが使用できる Analytics ユニット (AU) の合計数を制御します。 既定では、値は 250 に設定されます。 たとえば、この値が 250 AU に設定されている場合、250 AU が割り当てられている 1 つのジョブを実行するか、それぞれ 25 AU が割り当てられている 10 個のジョブを実行することができます。 送信される追加のジョブは、実行中のジョブが完了するまで、キューに登録されます。 実行中のジョブが完了すると、キューに登録されているジョブを実行するために AU が解放されます。

Data Lake Analytics アカウントの AU の数を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[プロパティ]**をクリックします。
3. **[最大 AU]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。 
4. [ **Save**] をクリックします。

> [!NOTE]
> 既定値 (250) より多い AU が必要な場合は、ポータルで **[ヘルプとサポート]** をクリックし、サポート要求を送信します。 Data Lake Analytics アカウントで利用できる AU の数は、増やすことができます。
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>同時に実行できるジョブの最大数
ポリシーを使用して、同時に実行できるジョブの数を制御します。 既定では、この値は 20 に設定されます。 Data Lake Analytics に利用可能な AU がある場合は、実行中のジョブの合計数がこのポリシーの値に達するまで、新しいジョブがすぐに実行されるようにスケジュールされます。 同時に実行できるジョブの最大数に達すると、後続のジョブは、1 つ以上の実行中のジョブ (使用できる AU に基づいて) が完了するまで、優先度に従ってキューに登録されます。

同時に実行できるジョブの最大数を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[プロパティ]**をクリックします。
3. **[実行ジョブの最大数]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。 
4. [ **Save**] をクリックします。

> [!NOTE]
> 既定値 (20) より多い数のジョブを実行する必要がある場合は、ポータルで **[ヘルプとサポート]** をクリックし、サポート要求を送信します。 Data Lake Analytics アカウントで同時に実行できるジョブの数は、増やすことができます。
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>ジョブのメタデータとリソースを保持する期間 
ユーザーが U-SQL ジョブを実行すると、Data Lake Analytics サービスは関連するすべてのファイルを保持します。 関連するファイルには、U-SQL スクリプト、U-SQL スクリプトで参照される DLL ファイル、コンパイルされたリソース、統計情報が含まれます。 ファイルは、既定の Azure Data Lake ストレージ アカウントの /system/ フォルダーにあります。 このポリシーでは、これらのリソースが格納されてから自動的に削除されるまでの期間を制御します (既定値は 30 日間です)。 これらのファイルは、デバッグのほか、今後再実行するジョブのパフォーマンス チューニングのために使用できます。

ジョブのメタデータとリソースを保持する期間を変更するには:

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[プロパティ]**をクリックします。
3. **[ジョブ クエリを保持する日数]** で、スライダーを移動して値を選択するか、テキスト ボックスに値を入力します。  
4. [ **Save**] をクリックします。

### <a name="job-level-policies"></a>ジョブレベルのポリシー
ジョブレベルのポリシーでは、個々のユーザー (または特定のセキュリティ グループのメンバー) が自分の送信するジョブに設定できる最大 AU と最大優先度を制御できます。 これで、ユーザーによって生じるコストを制御できます。 また、スケジュールされたジョブが同じ Data Lake Analytics アカウントで実行されている優先度の高い実稼働ジョブに与える可能性がある影響を制御することもできます。

Data Lake Analytics には、ジョブ レベルで設定できる次の 2 つのポリシーがあります。

* **ジョブあたりの AU 制限**: ユーザーは、最大でこの AU 数になる範囲でのみ、ジョブを送信できます。 既定では、この制限はアカウントの最大 AU 制限と同じです。
* **優先度**: ユーザーは、優先度がこの値以下のジョブだけを送信できます。 値が大きいほど優先度が低くなることに注意してください。 既定では、1 に設定されています。これは、最も高い優先度です。

すべてのアカウントに、既定のポリシー セットがあります。 既定のポリシーは、アカウントのすべてのユーザーに適用されます。 特定のユーザーとグループに対して、追加のポリシーを設定することができます。 

> [!NOTE]
> アカウントレベルのポリシーとジョブレベルのポリシーは、同時に適用されます。
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>特定のユーザーまたはグループへのポリシーの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[プロパティ]**をクリックします。
3. **[ジョブの送信の制限]** で、**[ポリシーの追加]** ボタンをクリックします。 次に、以下の設定を選択するか入力します。
    1. **[計算ポリシー名]**: ポリシーの目的がわかるようなポリシー名を入力します。
    2. **[ユーザーまたはグループの選択]**: このポリシーの適用対象となるユーザーまたはグループを選択します。
    3. **[ジョブ AU の制限を設定します]**: 選択したユーザーまたはグループに適用される AU の制限を設定します。
    4. **[Set the Priority Limit]\(優先度の制限を設定します\)**: 選択したユーザーまたはグループに適用される優先度の制限を設定します。

4. **[OK]**をクリックします。

5. **[ジョブの送信の制限]** の下にある **[既定]** ポリシー テーブルの一覧に、新しいポリシーが表示されます。 

#### <a name="delete-or-edit-an-existing-policy"></a>既存のポリシーの削除または編集

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[プロパティ]**をクリックします。
3. **[ジョブの送信の制限]** で、編集するポリシーを検索します。
4.  **[削除]** および **[編集]** オプションを表示するには、テーブルの右端の列で **[...]** をクリックします。

### <a name="additional-resources-for-job-policies"></a>ジョブ ポリシーの追加リソース
* [ポリシーの概要に関するブログの投稿](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [アカウントレベルのポリシーに関するブログの投稿](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [ジョブレベルのポリシーに関するブログの投稿](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-powershell.md)

