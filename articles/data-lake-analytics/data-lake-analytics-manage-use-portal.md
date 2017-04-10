---
title: "Azure Portal を使用して Azure Data Lake Analytics を管理する | Microsoft Docs"
description: "Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブの管理方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
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
translationtype: Human Translation
ms.sourcegitcommit: 86711ba89442c3569b570bbf3ea2d1661a469011
ms.openlocfilehash: 34be29749075b9953950ffd64d2526430307d33e


---
# <a name="manage-azure-data-lake-analytics-using-azure-portal"></a>Azure ポータルを使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure Portal を使用して、Azure Data Lake Analytics のアカウント、アカウント データ ソース、ユーザー、およびジョブを管理する方法について説明します。 他のツールを使用した管理のトピックを表示する場合は、ページの上部にあるタブ セレクターをクリックしてください。

**前提条件**

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>アカウントの管理
Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Azure HDInsight とは異なり、Data Lake Analytics アカウントではジョブの実行時にのみ課金されます。  ジョブの実行時にのみ課金されます。  詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。  

**Data Lake Analytics アカウントを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. **[新規]**、**[インテリジェンス + 分析]**、**[Data Lake Analytics]** の順にクリックします。
3. 次の値を入力または選択します。
   
    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **名前**: Data Lake Analytics アカウント名を指定します。
   * **サブスクリプション**: Analytics アカウントに使用する Azure サブスクリプションを選択します。
   * **リソース グループ**。 既存の Azure リソース グループを選択するか、新しいものを作成します。 Azure リソース マネージャーを使用すると、アプリケーション内の複数リソースを&1; つのグループと見なして作業できます。 詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 
   * **場所**:  Data Lake Analytics アカウントの Azure データ センターを選択します。 
   * **Data Lake Store**: 各 Data Lake Analytics アカウントには、従属する Data Lake Store アカウントがあります。 Data Lake Analytics アカウントと従属する Data Lake Store アカウントは、同じ Azure データ センターに配置する必要があります。 以下の指示に従って、新しい Data Lake Store アカウントを作成するか、既存のものを選択します。
4. **[作成]**をクリックします。 ポータルのホーム画面が表示されます。 新しいタイルはスタート画面に追加され、"Azure Data Lake Analytics のデプロイ" を示すラベルが付けられます。 Data Lake Analytics アカウントの作成にはしばらく時間がかかります。 アカウントが作成されると、ポータルの新しいブレードにアカウントが開きます。

Data Lake Analytics アカウントが作成されたら、Data Lake Store アカウントや Azure ストレージ アカウントを追加できます。 手順については、 [Data Lake Analytics アカウント データ ソースの管理](data-lake-analytics-manage-use-portal.md#manage-account-data-sources)に関する記述を参照してください。

<a name="access-adla-account"></a> **Data Lake Analytics アカウントにアクセスして開くには**

1. [Azure ポータル](https://portal.azure.com/)にサインオンします。
2. 左側のメニューで **[Data Lake Analytics]** をクリックします。  表示されていない場合は、**[その他のサービス]** をクリックし、**[インテリジェンス + 分析]** の下にある **[Data Lake Analytics]** をクリックします。
3. アクセスする Data Lake Analytics アカウントをクリックします。 新しいブレードでアカウントが開きます。

**Data Lake Analytics アカウントを削除するには**

1. 削除する Data Lake Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. ブレードの上部にあるボタン メニューで、 **[削除]** をクリックします。
3. アカウント名を入力してから、 **[削除]**をクリックします。

Data Lake Analytics アカウントを削除しても、従属する Data Lake Store アカウントは削除されません。 Data Lake ストレージ アカウントを削除する手順については、 [Data Lake Store アカウントの削除](../data-lake-store/data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account)に関する記述を参照してください。

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>アカウント データ ソースの管理
Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Data Lake Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake Store アカウントを指定する必要があります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Data Lake Analytics アカウントを作成したら、さらに Data Lake Store アカウントや Azure ストレージ アカウントを追加することができます。 

<a name="default-adl-account"></a>**既定の Data Lake ストレージ アカウントを検索するには**

* 管理する Data Lake Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。 既定の Data Lake Store は以下のように **[要点]**に表示されます。
  
    ![Azure Data Lake Analytics でのデータ ソースの追加](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**データ ソースをさらに追加するには**

1. 管理する Data Lake Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. **[設定]** をクリックしてから、**[データ ソース]** をクリックします。 そこに既定の Data Lake Store アカウントがリストされます。 
3. **[データ ソースの追加]**をクリックします。
   
    ![Azure Data Lake Analytics のデータ ソースの追加](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)
   
    Azure Data Lake Store アカウントを追加するには、アカウント名と、クエリを実行できるようアカウントへのアクセスが必要です。
    Azure Blob Storage を追加するには、ストレージ アカウントとアカウント キーが必要です。これらは、ポータルでストレージ アカウントに移動して確認できます。

<a name="explore-data-sources"></a>**データ ソースを検索するには**    

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. **[設定]** をクリックしてから、**[データ エクスプローラー]** をクリックします。 
   
    ![Azure Data Lake Analytics データ エクスプ ローラー](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
3. Data Lake Store アカウントをクリックしてアカウントを開きます。
   
    ![Azure Data Lake Analytics データ エクスプ ローラーのストレージ アカウント](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
   
    Data Lake Store アカウントごとに、以下を行うことができます。
   
   * **新しいフォルダー**: 新しいフォルダーを追加します。
   * **アップロード**: ワークステーションからストレージ アカウントにファイルをアップロードします。
   * **アクセス**: アクセス許可を構成します。
   * **フォルダー名の変更**: フォルダーの名前を変更します。
   * **フォルダーのプロパティ**: WASB パス、WEBHDFS パス、最終変更時刻など、ファイルまたはフォルダーのプロパティを表示します。
   * **フォルダーの削除**: フォルダーを削除します。

<a name="upload-data-to-adls"></a> **Data Lake Store アカウントにファイルをアップロードするには**

1. ポータルで、左側のメニューにある **[参照]** をクリックしてから **[Data Lake Store]** をクリックします。
2. データをアップロードする Data Lake Store アカウントをクリックします。 既定の Data Lake ストレージ アカウントを検索する場合は、 [こちら](#default-adl-account)を参照してください。
3. 上部のメニューにある **[データ エクスプローラー]** をクリックします。
4. **[新しいディレクトリ]** をクリックして新しいフォルダーを作成するか、フォルダー名をクリックしてフォルダーを変更します。
5. 上部のメニューにある **[アップロード]** をクリックしてファイルをアップロードします。

<a name="upload-data-to-wasb"></a> **Azure BLOB ストレージ アカウントにファイルをアップロードするには**

「 [HDInsight での Hadoop ジョブ用データのアップロード](../hdinsight/hdinsight-upload-data.md)」を参照してください。  この情報は Data Lake Analytics に適用されます。

## <a name="manage-users"></a>ユーザーの管理
Data Lake Analytics では、Azure Active Directory でのロール ベースの Access Control を使用します。 Data Lake Analytics アカウントの作成時に、"サブスクリプション管理者" ロールがアカウントに追加されます。 以下のロールを持つユーザーとセキュリティ グループをさらに追加することができます。

| 役割 | 説明 |
| --- | --- |
| 所有者 |リソースへのアクセスを含め、すべてを管理できます。 |
| 共同作成者 |ポータルにアクセスし、ジョブを送信および監視します。 ジョブを送信するには、共同作成者に、Data Lake Store アカウントに対する読み取りアクセス許可または書き込みアクセス許可が必要になります。 |
| DataLakeAnalyticsDeveloper |ジョブを送信、監視、キャンセルします。  これらのユーザーがキャンセルできるのは自分のジョブのみです。 ユーザーの追加、アクセス許可の変更、アカウントの削除などを行って、自分のアカウントを管理することはできません。 ジョブを実行するには、Data Lake Store アカウントに対する読み取りまたは書き込みアクセス許可が必要です。 |
| 閲覧者 |すべてを表示できますが、変更することはできません。 |
| DevTest Labs ユーザー |すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます。 |
| ユーザーアクセスの管理者 |Azure リソースに対するユーザー アクセスを管理します。 |

Azure Active Directory ユーザーとセキュリティ グループの作成については、「 [Azure Active Directory とは](../active-directory/active-directory-whatis.md)」を参照してください。

**Data Lake Analytics アカウントにユーザーまたはセキュリティ グループを追加するには**

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. **[設定]** をクリックし、**[ユーザー]** をクリックします。 以下のスクリーンショットに示されているように、**[要点]** タイトル バーの **[アクセス]** をクリックすることもできます。
   
    ![Azure Data Lake Analytics アカウントのユーザーの追加](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. **[ユーザー]** ブレードで、**[追加]** をクリックします。
4. ロールを選択し、ユーザーを追加してから **[OK]**をクリックします。

>[!NOTE]
>このユーザーまたはセキュリティ グループは、ジョブを送信する必要がある場合、Data Lake Store に対するアクセス許可も与えられている必要があります。 詳細については、[Data Lake Store に格納されているデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)に関する記事を参照してください。
>

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>ジョブの管理
U-SQL ジョブを実行するには、Data Lake Analytics アカウントが必要です。  詳細については、「 [Data Lake Analytics アカウントの管理](#manage-data-lake-analytics-accounts)」を参照してください。

<a name="create-job"></a>**ジョブを作成するには**

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. **[新しいジョブ]**をクリックします。
   
    ![Azure Data Lake Analytics U-SQL ジョブの作成](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)
   
    次のような新しいブレードが表示されます。
   
    ![Azure Data Lake Analytics U-SQL ジョブの作成](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)
   
    ジョブごとに、以下を構成することができます。

    |名前|説明|
    |----|-----------|
    |ジョブ名|ジョブの名前を入力します。|
    |優先順位|数が小さいほど優先順位が高くなります。 2 つのジョブが両方ともキューに登録されている場合は、優先順位の低いものが最初に実行されます。|
    |並列処理 |同時に発生する可能性があるコンピューティング プロセスの最大数。 この数を増やすことで、パフォーマンスを向上させることができますが、コストが増加することもあります。|
    |スクリプト|ジョブの U-SQL スクリプトを入力します。|

    同じインターフェイスを使用して、リンク データ ソースを検索し、リンクされているデータ ソースにファイルをさらに追加することもできます。 
1. ジョブを送信する場合は、 **[ジョブの送信]** をクリックします。

**ジョブを送信するには**

[Data Lake Analytics ジョブの作成](#create-job)に関する記述を参照してください。

<a name="monitor-jobs"></a>**ジョブを監視するには**

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。 以下のように、[ジョブ管理] パネルには基本的なジョブ情報が示されます。
   
    ![Azure Data Lake Analytics U-SQL ジョブの管理](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)
2. 前述のスクリーンショットに示されている **[ジョブ管理]** をクリックします。
   
    ![Azure Data Lake Analytics U-SQL ジョブの管理](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)
3. リストのジョブをクリックします。 または、以下のように **[フィルター]** をクリックします。これは、ジョブを検索するのに役立ちます。
   
    ![Azure Data Lake Analytics U-SQL ジョブのフィルター処理](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)
   
    ジョブは、**時間範囲**、**ジョブ名**、および**作成者**でフィルター処理できます。
4. ジョブを再送信する場合は、 **[再送信]** をクリックします。

**ジョブを再送信するには**

[Data Lake Analytics ジョブの監視](#monitor-jobs)に関する記述を参照してください。

## <a name="monitor-account-usage"></a>アカウントの使用状況の監視
**アカウントの使用状況を監視するには**

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。 使用状況は、以下のように [使用状況] パネルに表示されます。
   
    ![Azure Data Lake Analytics 使用状況の監視](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)
2. ペインをダブルクリックすると、詳細が示されます。

## <a name="view-u-sql-catalog"></a>U-SQL カタログの表示
[U-SQL カタログ](data-lake-analytics-use-u-sql-catalog.md) を使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。 カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。 Azure Portal で、U-SQL カタログを表示することができます。

**U-SQL カタログを参照するには**

1. 管理する Analytics アカウントを開きます。 手順については、[Data Lake Analytics アカウントへのアクセス](#access-adla-account)に関する記述を参照してください。
2. 上部のメニューにある **[データ エクスプローラー]** をクリックします。
3. **[カタログ]**、**[master]** の順に展開し、**[テーブル]、**[テーブル値関数]** または **[アセンブリ]** を展開します。 次のスクリーンショットには、1 つのテーブル値関数が示されています。
   
    ![Azure Data Lake Analytics データ エクスプ ローラーのストレージ アカウント](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-azure-resource-manager-groups"></a>Azure リソース マネージャー グループの使用
アプリケーションは通常、Web アプリ、データベース、データベース サーバー、ストレージ、サードパーティのサービスなどの、複数のコンポーネントで構成されます。 Azure Resource Manager を使用すると、アプリケーション内の複数のリソースを&1; つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。 デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。 詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 

Data Lake Analtyics サービスには、次のコンポーネントを含めることができます。

* Azure Data Lake Analytics アカウント
* 必要な既定の Azure Data Lake Store アカウント
* 追加の Azure Data Lake Store アカウント
* 追加の Azure Storage アカウント

管理しやすくするために&1; つの Resource Management グループの下にこれらすべてのコンポーネントを作成することができます。

![Azure Data Lake Analytics のアカウントとストレージ](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics アカウントと従属するストレージ アカウントは同じ Azure データ センターに配置する必要があります。
ただし、Resource Management グループは別のデータ センターに配置できます。  

## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
* [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Dec16_HO2-->


