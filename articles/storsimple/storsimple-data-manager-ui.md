---
title: Microsoft Azure StorSimple Data Manager の UI
description: StorSimple Data Manager UI を使用し、StorSimple 8000 シリーズ デバイス上にあるデータを変換する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94957930"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Azure Portal で StorSimple Data Manager サービスを管理する

この記事では、StorSimple Data Manager UI を使って、StorSimple 8000 シリーズ デバイス上にあるデータを変換する方法について説明します。 変換されたデータは、Azure Media Services、Azure HDInsight、Azure Machine Learning、Azure Cognitive Search など、他の Azure サービスが使用できます。


## <a name="use-storsimple-data-transformation"></a>StorSimple データ変換を使用する

StorSimple Data Manager は、その内部でデータ変換がインスタンス化されるリソースです。 データ変換サービスを使うと、StorSimple 形式から BLOB または Azure Files のネイティブ形式にデータを変換できます。 StorSimple ネイティブ形式のデータを変換するには、StorSimple 8000 シリーズ デバイスについての詳細と、変換対象のデータを指定する必要があります。

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple データ マネージャー サービスを作成する

StorSimple データ マネージャー サービスを作成するには、次の手順を実行します。

1. Microsoft アカウントの資格情報を使用して、[Azure Portal](https://portal.azure.com/) にログオンします。

2. **[+ リソースの作成]** をクリックして、StorSimple Data Manager を検索します。

    ![StorSimple Data Manager サービスを作成する 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. StorSimple Data Manager をクリックし、 **[作成]** をクリックします。
    
    ![StorSimple Data Manager サービスを作成する 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. 新しいサービスの場合、以下の情報を指定します。

   1. StorSimple Data Manager の一意の **[サービス名]** を指定します。 これは、サービスの識別に使用できるフレンドリ名です。 名前の長さは 3 ～ 24 文字で、文字、数字、ハイフンを使うことができます。 名前の最初と最後は、文字か数字とする必要があります。

   2. ドロップダウン リストから **[サブスクリプション]** を選びます。 サブスクリプションは、課金アカウントにリンクされます。 持っているサブスクリプションが 1 つだけの場合、このフィールドは自動的に設定されます (選択不可)。

   3. 既存のリソース グループを選ぶか、新しいリソース グループを作成します。 詳細については、[Azure のリソース グループ](../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事をご覧ください。

   4. ストレージ アカウントを含むサービスと StorSimple Data Manager サービスの **[場所]** を指定します。 StorSimple Device Manager サービス、Data Manager サービス、および関連するストレージ アカウントはすべて、サポートされているリージョンに存在する必要があります。
    
   5. ダッシュボードにこのサービスへのリンクを作成するには、 **[ダッシュボードにピン留めする]** をオンにします。
    
   6. **Create** をクリックしてください。

      ![StorSimple Data Manager サービスを作成する 3](./media/storsimple-data-manager-ui/create-service-4.png)

サービスの作成には数分かかります。 サービスが正常に作成されると通知が示され、新しいサービスが表示されます。

### <a name="create-a-data-transformation-job-definition"></a>データ変換ジョブの定義の作成

StorSimple データ マネージャー サービス内に、データ変換ジョブの定義を作成する必要があります。 ジョブ定義では、ネイティブ形式でストレージ アカウントに移動する StorSimple データの詳細を指定します。 ジョブ定義を作成した後は、そのジョブを異なるランタイム設定で再び実行できます。

ジョブ定義を作成するには、次の手順を実行します。

1. 作成したサービスに移動します。 **[管理] > [ジョブ定義]** に移動します。

2. **[+ ジョブ定義]** をクリックします。

    ![[+ ジョブ定義] をクリック](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. ジョブ定義の名前を指定します。 名前は 3 から 63 文字で指定できます。 名前には、大文字と小文字、数字、ハイフンを含めることができます。

4. ジョブを実行する場所を指定します。 この場所は、サービスが展開される場所と異なっていてもかまいません。

5. **[ソース]** をクリックして、ソース データ リポジトリを指定します。

    ![ソース データ リポジトリを構成する](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. これは新しいデータ マネージャー サービスであるため、データ リポジトリは構成されていません。 **[データ ソースの構成]** で、StorSimple 8000 シリーズ デバイスと対象データの詳細を指定します。

   StorSimple Device Manager をデータ リポジトリとして追加するには、データ リポジトリのドロップダウンで **[新規追加]** をクリックし、 **[データ リポジトリの追加]** をクリックします。

    ![新しいデータ リポジトリを追加する](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. データ リポジトリの種類としては、 **[StorSimple 8000 series Manager]\(StorSimple 8000 シリーズ マネージャー\)** を選びます。
    
   2. ソース データ リポジトリのフレンドリ名を入力します。
    
   3. ドロップダウン リストから、StorSimple Device Manager サービスに関連付けられているサブスクリプションを選びます。
    
   4. **[リソース]** で StorSimple Device Manager の名前を指定します。

   5. StorSimple Device Manager サービスの **[サービス データ暗号化キー]** を入力します。 

      ![ソース データ リポジトリを構成する 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      完了したら、 **[OK]** をクリックします。 データ リポジトリが保存されます。 これらのパラメーターをもう一度入力することなく、他のジョブ定義でこの StorSimple Device Manager を再利用できます。 **[OK]** をクリックしてから数秒後に、新しく作成したソース データ リポジトリがドロップダウンに表示されます。

7. **[データ リポジトリ]** ドロップダウン リストから、作成したデータ リポジトリを選びます。 

   1. 対象データが格納されている StorSimple 8000 シリーズ デバイスの名前を入力します。

   2. 対象データが存在する、StorSimple デバイス上のボリュームの名前を指定します。

   3. **[フィルター]** サブセクションで、対象データを含むルート ディレクトリを、 _\MyRootDirectory\Data_ の形式で入力します。 _\C:\Data_ などのドライブ文字はサポートされていません。 任意のファイル フィルターをここで追加することもできます。

   4. データ変換サービスは、Azure にプッシュされたデータの最新スナップショットでのみ機能します。

   5. **[OK]** をクリックします。

      ![ソース データ リポジトリを構成する 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. 次に、ターゲット データ リポジトリを構成する必要があります。 ストレージ アカウントを選択すると、そのアカウントの BLOB にファイルが配置されます。 ドロップダウンで **[新規追加]** を選択し、 **[設定の構成]** を選択します。

9. 追加するターゲット リポジトリの種類と、そのリポジトリに関連付けられている他のパラメーターを選びます。

    ストレージ アカウントの種類のターゲットを選ぶ場合は、フレンドリ名、サブスクリプション (サービスまたはその他と同じサブスクリプションを選びます)、およびストレージ アカウントを指定できます。
        ![ターゲット データ リポジトリを構成する 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    ジョブの実行時にストレージ キューが作成されます。 変換された BLOB の準備が完了すると、キューにはその BLOB に関するメッセージが設定されます。 このキューの名前は、ジョブ定義の名前と同じです。
    
10. データ リポジトリを追加した後、数分待ちます。
    
    1. **[Target repository]\(ターゲット リポジトリ\)** のドロップダウン リストから、作成したリポジトリをターゲットとして選びます。

    2. ストレージの種類として、BLOB またはファイルを選びます。 変換後のデータが存在するストレージ コンテナーの名前を指定します。 **[OK]** をクリックします。

        ![ターゲット データ リポジトリのストレージ アカウントを構成する](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. ジョブを実行する前に、ジョブの実行期間の見積もりを表示するオプションをオンにすることもできます。 **[OK]** をクリックして、ジョブ定義を作成します。 これでジョブ定義は完了です。 このジョブ定義を、UI から、異なる実行時設定で、複数回使うことができます。

    ![ジョブ定義の完了](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    新しく作成したジョブ定義が、このサービスのジョブ定義の一覧に追加されます。

### <a name="run-the-job-definition"></a>ジョブ定義を実行する

StorSimple のデータを、ジョブ定義で指定したストレージ アカウントに移動する必要がある場合は必ず、そのジョブを実行する必要があります。 実行時に、一部のパラメーターに異なる値を指定できます。 手順は次のとおりです。

1. StorSimple Data Manager サービスを選び、 **[管理] > [ジョブ定義]** に移動します。 実行するジョブ定義を選んでクリックします。
     
     ![ジョブの実行を開始する 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. **[今すぐ実行]** をクリックします。
     
     ![ジョブの実行を開始する 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. **[実行設定]** をクリックして、このジョブの実行に対する設定を変更します。 **[OK]** をクリックし、 **[実行]** をクリックしてジョブを起動します。

    ![ジョブの実行を開始する 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. このジョブを監視するには、StorSimple Data Manager で **[ジョブ]** に移動します。 **[ジョブ]** ブレードでは監視だけでなく、StorSimple からストレージ アカウントにファイルが移動するたびにメッセージが追加されるストレージ キューをリッスンすることもできます。

    ![ジョブの実行を開始する 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>ジョブの完了後にログを表示する

ジョブの完了後、ジョブの状態を表示できます。 ジョブの状態には **成功**、**一部成功**、**失敗** があります。 正常にコピーされたファイルとコピーできなかったファイルを一覧表示できます。 このような一覧は、ターゲット ストレージ アカウント内で **"storsimple-data-manager-joblogs"** という名称のコンテナーで見つかります。 このコンテナー内では、ジョブ定義と同じ名前のフォルダーを探してください。 このコンテナーでは、ジョブが実行されるたびにフォルダーが作成され、その中に一覧が入ります。 このフォルダーの名前はジョブの GUID になりますが、これはジョブの詳細ページで確認できます。 あるいは、ほとんどの場合、ジョブ ページ自体の中にコピー ログのリンクがあります。
このフォルダーには csv ファイルが 2 セットあります。 **copiedfilelist...** で始まるファイルにはすべて、正常にコピーされたファイルの一覧が含まれます。 **failedfilelist...** で始まるファイルにはすべて、コピーできなかったファイルとエラー メッセージが含まれます。


## <a name="next-steps"></a>次のステップ

[.NET SDK を使用して StorSimple データ マネージャー ジョブを起動します](storsimple-data-manager-dotnet-jobs.md)。