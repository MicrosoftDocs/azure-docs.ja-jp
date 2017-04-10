---
title: "Microsoft Azure StorSimple データ マネージャーの UI | Microsoft Docs"
description: "StorSimple データ マネージャー サービス UI を使用する方法について説明します (プライベート プレビュー)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 692f00bf342e2cd6d3d76754d18751c773aeedc5
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0

---

# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>StorSimple データ マネージャー サービス UI を使用して管理する (プライベート プレビュー)

この記事では、StorSimple データ マネージャー UI を使用して、StorSimple 8000 シリーズ デバイス上にあるデータでデータ変換を実行する方法について説明します。 変換されたデータは、Azure Media Services、Azure HDInsight、Azure Machine Learning、Azure Search など、他の Azure サービスが使用できます。 


## <a name="use-storsimple-data-transformation"></a>StorSimple データ変換を使用する

StorSimple データ マネージャーは、その内部でデータ変換をインスタンス化できるリソースです。 データ変換サービスを使用すると、StorSimple オンプレミス デバイスのデータを Azure Storage の BLOB に移動できます。 このため、ワークフローでは、ストレージ アカウントに移動する StorSimple デバイスと対象データの詳細を指定する必要があります。

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple データ マネージャー サービスを作成する

StorSimple データ マネージャー サービスを作成するには、次の手順を実行します。

1. StorSimple データ マネージャー サービスを作成するには、[https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager) に移動します

2. **+** アイコンをクリックして、StorSimple データ マネージャーを検索します。 StorSimple データ マネージャー サービスをクリックし、**[作成]** をクリックします。

3. このサービスを作成するためのサブスクリプションが有効になっている場合は、次のブレードが表示されます。

    ![StorSimple データ マネージャー リソースの作成](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. 情報を入力して、**[作成]** をクリックします。 指定した場所には、ストレージ アカウントと、StorSimple Manager サービスが含まれている必要があります。 現時点でサポートされているリージョンは、米国西部と西ヨーロッパのみです。 このため、StorSimple Manager サービス、データ マネージャー サービス、および関連するストレージ アカウントすべてが、このサポートされているリージョンに配置されている必要があります。 サービスの作成には約 1 分かかります。

### <a name="create-a-data-transformation-job-definition"></a>データ変換ジョブの定義の作成

StorSimple データ マネージャー サービス内に、データ変換ジョブの定義を作成する必要があります。 ジョブ定義では、ネイティブ形式でストレージ アカウントに移動したいと考えているデータの詳細を指定します。 

新しいデータ変換ジョブの定義を作成するには、次の手順を実行します。

1.  作成したサービスに移動します。 **[+ ジョブ定義]** をクリックします。

    ![[+ ジョブ定義] をクリック](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. [New job definition (新しいジョブ定義)] ブレードが表示されます。 ジョブ定義に名前を付けて、**[ソース]** をクリックします。 **[データ ソースの構成]** ブレードで、StorSimple デバイスと対象データの詳細を指定します。

    ![ジョブ定義の作成](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. これは新しいデータ マネージャー サービスであるため、データ リポジトリは構成されていません。 StorSimple Manager をデータ リポジトリとして追加するには、データ リポジトリのドロップダウンで **[新規追加]** をクリックし、**[Add Data Repository (データ リポジトリの追加)]** をクリックします。

4. リポジトリの種類として **[StorSimple 8000 series Manager (StorSimple Manager 8000 シリーズ)]** を選択し、**StorSimple Manager** のプロパティを入力します。 **[リソース ID]** フィールドには、StorSimple Manager の登録キーの **:** の前の番号を入力する必要があります。

    ![データ ソースの作成](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  完了したら、**[OK]** をクリックします。 これにより、データ リポジトリが保存され、この StorSimple Manager を他のジョブ定義で再利用できます。その際、このパラメーターを再入力する必要はありません。 **[OK]** をクリックしてから数秒後に、StorSimple Manager がドロップダウン リストに表示されます。

6.  **[データ ソースの構成]** ブレードで、デバイス名と対象データを含むボリューム名を入力します。

7.  **[フィルター]** サブセクションで、対象データを含むルート ディレクトリを入力します (入力するデータの先頭は `\` にします)。 任意のファイル フィルターをここで追加することもできます。

8.  データ変換サービスは、スナップショットを使用して Azure にプッシュされたデータで機能します。 このジョブの実行中、ジョブを実行するたびに (最新のデータで機能する) バックアップを作成するように選択できます。また、クラウド内の既存のバックアップを使用することもできます (アーカイブされたデータで作業している場合)。

    ![新しいデータ ソースの詳細](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. 次に、ターゲットの設定を構成する必要があります。 ターゲットとしてサポートされているのは、Azure ストレージ アカウントと Azure Media Services アカウントの 2 つです。 ストレージ アカウントを選択すると、そのアカウントの BLOB にファイルが配置されます。 Media Services アカウントを選択すると、そのアカウントの資産にファイルが配置されます。 ここでも、リポジトリを追加する必要があります。 ドロップダウンで **[新規追加]** を選択し、**[設定の構成]** を選択します。

    ![データ シンクの作成](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. ここでは、追加するリポジトリの種類と、そのリポジトリに関連付けられている他のパラメーターを選択できます。 どちらの場合も、ジョブの実行時にストレージ キューが作成されます。 変換された BLOB の準備が完了すると、キューにはその BLOB に関するメッセージが設定されます。 このキューの名前は、ジョブ定義の名前と同じです。 リポジトリの種類として **Media Services** を選択した場合、キューが作成された場所でストレージ アカウントの資格情報を入力することもできます。

    ![新しいデータ シンクの詳細](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. 追加したデータ リポジトリは、追加してから数秒後に **[Target account name (対象アカウント名)]** のドロップダウンに表示されます。  必要なターゲットを選択します。

12. **[OK]** をクリックして、ジョブ定義を作成します。 これでジョブ定義が設定されました。 このジョブ定義は UI を介して複数回使用することができます。

    ![新しいジョブ定義の追加](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>ジョブ定義を実行する

StorSimple のデータを、ジョブ定義で指定したストレージ アカウントに移動する必要がある場合は必ず、そのジョブを呼び出す必要があります。 ジョブを呼び出すたびに、パラメーターを柔軟に変更できます。 手順は次のとおりです。

1. StorSimple データ マネージャー サービスを選択し、**[監視]** に移動します。 **[今すぐ実行]** をクリックします。

    ![ジョブ定義のトリガー](./media/storsimple-data-manager-ui/run-now.png)

2. 実行するジョブ定義を選択します。 **[実行設定]** をクリックして、このジョブの実行に対する設定を変更します。

    ![ジョブ設定の実行](./media/storsimple-data-manager-ui/run-settings.png)

3. **[OK]** をクリックし、**[実行]** をクリックしてジョブを起動します。 このジョブを監視するには、StorSimple データ マネージャーの **[ジョブ]** ページに移動します。

    ![ジョブの一覧と状態](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. **[ジョブ]** ブレードでは監視だけでなく、StorSimple からストレージ アカウントにファイルが移動するたびにメッセージが追加されるストレージ キューをリッスンすることもできます。


## <a name="next-steps"></a>次のステップ

[.NET SDK を使用して StorSimple データ マネージャー ジョブを起動します](storsimple-data-manager-dotnet-jobs.md)。


<!--HONumber=Nov16_HO4-->


