---
title: Azure Portal を使用した Azure Data Lake Analytics の管理
description: このアーティクルでは、Data Lake Analytics アカウント、データ ソース、ユーザー、およびジョブを管理するために Azure portal を使用する方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8b2f16f45be1d095e9be8042611de328af36f064
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724062"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Azure portal を使用した Azure Data Lake Analytics の管理
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

このアーティクルでは、Azure portal を使用して、Azure Data Lake Analytics アカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントの管理

### <a name="create-an-account"></a>アカウントの作成

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[リソースの作成]** > **[インテリジェンス + 分析]** > **Data Lake Analytics** をクリックします。
3. 次の項目の値を選択します。 
   1. **[名前]**:Data Lake Analytics アカウントの名前。
   2. **サブスクリプション**:アカウントに使用する Azure サブスクリプション。
   3. **[リソース グループ]**:アカウントの作成先の Azure リソース グループ。 
   4. **[場所]**:Data Lake Analytics アカウントの Azure データセンター。 
   5. **[Data Lake Store]**:Data Lake Analytics アカウントのために使用される既定のストア。 Azure Data Lake Store アカウントと Data Lake Analytics アカウントは、同じ場所にある必要があります。
4. **Create** をクリックしてください。 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントの削除

Data Lake Analytics アカウントを削除する前に、その既定の Data Lake Store アカウントを削除します。

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[削除]** をクリックします。
3. アカウント名を入力します。
4. **[削除]** をクリックします。

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>データ ソースを管理する

Data Lake Analytics では、次のデータ ソースがサポートされています。

* Data Lake Store
* Azure Storage

データ エクスプローラーを使用して、データ ソースを参照し、基本的なファイル管理操作を実行することができます。 

### <a name="add-a-data-source"></a>データ ソースの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[データ ソース]** をクリックします。
3. **[データ ソースの追加]** をクリックします。
    
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
6. ファイルの ACL を選択します。 既定のストアの場合は、ルート フォルダー "/" と /system フォルダーの ACL を変更しないでください。 **[選択]** をクリックします。
7. 選択したすべての変更を確認し、**[実行]** をクリックします。
8. ウィザードが終了したら、**[完了]** をクリックします。

## <a name="manage-role-based-access-control"></a>ロールベースのアクセス制御の管理

他の Azure サービスと同様に、ロールベースのアクセス制御 (RBAC) を使用して、ユーザーがサービスと対話する方法を制御できます。

標準の RBAC ロールには、次のような機能があります。
* **所有者**:ジョブの送信、ジョブの監視、任意のユーザーのジョブの取り消し、アカウントの構成を行えます。
* **共同作成者**:ジョブの送信、ジョブの監視、任意のユーザーのジョブの取り消し、アカウントの構成を行えます。
* **閲覧者**:ジョブを監視できます。

U-SQL 開発者が Data Lake Analytics サービスを使用できるようにするには、Data Lake Analytics の開発者ロールを使用します。 Data Lake Analytics の開発者ロールを使用すると、次の操作を行えます。
* ジョブを送信する。
* ジョブの状態と、任意のユーザーによって送信されたジョブの進行状況を監視する。
* 任意のユーザーによって送信されたジョブの U-SQL スクリプトを確認する。
* 自分のジョブだけを取り消す。

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントへのユーザーまたはセキュリティ グループの追加

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[アクセス制御 (IAM)]** > **[ロールの割り当ての追加]** の順にクリックします。
3. ロールを選択します。
4. ユーザーを追加します。
5. Click **OK**.

>[!NOTE]
>ユーザーまたはセキュリティ グループがジョブを送信する必要がある場合は、ストア アカウントに対するアクセス許可も必要です。 詳細については、[Data Lake Store に格納されているデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)に関する記事を参照してください。
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>ジョブの管理

### <a name="submit-a-job"></a>ジョブの送信

1. Azure Portal で Data Lake Analytics アカウントに移動します。

2. **[新しいジョブ]** をクリックします。 各ジョブで、以下の項目を構成します。

    1. **[ジョブ名]**:ジョブの名前。
    2. **[優先度]**:数値が小さいほど優先度が高くなります。 2 つのジョブがキューに登録されている場合は、優先度の値が小さい方のジョブが先に実行されます。
    3. **[並行処理]**:このジョブのために予約する計算プロセスの最大数。

3. **[ジョブの送信]** をクリックします。

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

## <a name="next-steps"></a>次の手順

* [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-powershell.md)
* [ポリシーを使用して Azure Data Lake Analytics を管理する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
