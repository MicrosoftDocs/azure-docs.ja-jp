---
title: "Azure Backup のレポートを構成する"
description: "この記事では、Recovery Services コンテナーを使用する Azure Backup の Power BI レポートを構成する方法について説明します。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/13/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 597c3eb49bc3d4f9b2311f232e4280027636266c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="configure-azure-backup-reports"></a>Azure Backup のレポートを構成する
この記事では、Recovery Services コンテナーを使用する Azure Backup のレポートを構成し、Power BI を使用してそのレポートにアクセスする手順を説明します。 この手順を実行すると、Power BI に直接アクセスしてすべてのレポートを表示し、レポートのカスタマイズおよび作成を行えるようになります。 

## <a name="supported-scenarios"></a>サポートされるシナリオ
1. Azure Backup のレポートは、Azure Recovery Services エージェントを使用したクラウドへの Azure 仮想マシンのバックアップおよびファイル/フォルダーのバックアップに対してサポートされます。
2. 現時点では、Azure SQL、DPM、および Azure Backup Server に対するレポートはサポートされていません。
3. 各コンテナーに対して同じストレージ アカウントが構成されている場合は、複数のコンテナーおよび複数のサブスクリプションにわたるレポートを表示できます。 選択するストレージ アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。
4. Power BI での、レポートのスケジュールされた更新の頻度は 24 時間ごとです。 Power BI ではレポートのアドホック更新も実行できます。その場合、顧客のストレージ アカウント内の最新データを使用してレポートがレンダリングされます。 

## <a name="prerequisites"></a>前提条件
1. レポート用に構成する [Azure ストレージ アカウントを作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)します。 このストレージ アカウントは、レポート関連のデータを格納するために使用されます。
2. Power BI ポータルを使用して独自のレポートを表示、カスタマイズ、および作成するための [Power BI アカウントを作成](https://powerbi.microsoft.com/landing/signin/)します。
3. ストレージ アカウントのサブスクリプションと Recovery Services コンテナーのサブスクリプションにリソース プロバイダー **Microsoft.insights** がまだ登録されていない場合は登録して、レポート データをストレージ アカウントにフローできるようにします。 そのためには、Azure Portal で [サブスクリプション] > [リソース プロバイダー] に移動し、このプロバイダーを探して登録します。 

## <a name="configure-storage-account-for-reports"></a>レポート用のストレージ アカウントを構成する
Azure Portal で次の手順を使用して、Recovery Services コンテナー用のストレージ アカウントを構成します。 これは 1 回限りの構成であり、ストレージ アカウントをいったん構成すれば、Power BI に直接アクセスしてコンテンツパックを表示し、レポートを利用できるようになります。
1. 既に Recovery Services コンテナーが開かれている場合は、次の手順に進みます。 Recovery Services コンテナーが開かれていない場合は、Azure Portal でハブ メニューの **[参照]** をクリックします。

   * リソース ボックスに「 **Recovery Services**」と入力します。
   * 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**が表示されたら、それをクリックします。

      ![Create Recovery Services Vault step 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Recovery Services コンテナーの一覧が表示されます。 Recovery Services コンテナーの一覧で、コンテナーを選択します。

     選択したコンテナーのダッシュボードが開きます。
2. コンテナーの下に表示される項目の一覧で、[監視およびレポート] セクションの下の **[バックアップ レポート]** をクリックして、レポート用のストレージ アカウントを構成します。

      ![[バックアップ レポート] メニュー項目を選択する (手順 2)](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. [バックアップ レポート] ブレードで、**[構成]** ボタンをクリックします。 これにより、[Azure Application Insights] ブレードが開きます。このブレードは、顧客のストレージ アカウントにデータを発行するために使用されます。

      ![ストレージ アカウントを構成する (手順 3)](./media/backup-azure-configure-reports/configure-storage-account.PNG)
4. [状態] トグル ボタンを **[オン]** に設定し、**[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにして、ストレージ アカウントへのレポート データのフローを開始できるようにします。

      ![診断を有効化する (手順 4)](./media/backup-azure-configure-reports/set-status-on.png)
5. [ストレージ アカウント] ピッカーをクリックし、一覧からレポート データを格納するストレージ アカウントを選択して、**[OK]** をクリックします。

      ![ストレージ アカウントを選択する (手順 5)](./media/backup-azure-configure-reports/select-storage-account.png)
6. **[AzureBackupReport]** チェック ボックスをオンにし、スライダーを動かして、このレポート データのリテンション期間を選択します。 ストレージ アカウント内のレポート データは、このスライダーで選択した期間のあいだ保持されます。

      ![ストレージ アカウントを選択する (手順 6)](./media/backup-azure-configure-reports/save-configuration.png)
7. すべての変更を確認し、図に示した一番上の **[保存]** ボタンをクリックします。 このアクションにより、すべての変更が保存され、ストレージ アカウントがレポート データを格納するように構成されます。

> [!NOTE]
> ストレージ アカウントを保存してレポートを構成した後は、初期データのプッシュが完了するまで**24 時間待つ**必要があります。 その時間が経過した後でのみ、Power BI に Azure Backup コンテンツ パックをインポートする必要があります。 詳しくは、「[よく寄せられる質問](#frequently-asked-questions)」をご覧ください。 
>
>

## <a name="view-reports-in-power-bi"></a>Power BI でレポートを表示する 
Recovery Services コンテナーを使用するレポート用にストレージ アカウントを構成した後、レポート データのフローが開始されるまでに約 24 時間かかります。 ストレージ アカウントを設定してから 24 時間が経過したら、次の手順を使用して Power BI でレポートを表示します。
1. Power BI に[サインイン](https://powerbi.microsoft.com/landing/signin/)します。
2. **[データの取得]** をクリックし、[コンテンツ パック ライブラリ]の **[サービス]** の下にある [取得] をクリックします。 [Power BI のドキュメントで説明されているコンテンツ パックへのアクセス手順](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/)を使用します。

     ![コンテンツ パックをインポートする](./media/backup-azure-configure-reports/content-pack-import.png)
3. 検索バーに「**Azure Backup**」と入力し、**[今すぐ入手する]** をクリックします。

      ![コンテンツパックを入手する](./media/backup-azure-configure-reports/content-pack-get.png)
4. 上記の手順 5 で構成したストレージ アカウント名を入力し、**[次へ]** ボタンをクリックします。

    ![ストレージ アカウント名を入力する](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. このストレージ アカウントのストレージ アカウント キーを入力します。 Azure Portal でストレージ アカウントに移動して、[ストレージ アクセス キーを表示してコピー](../storage/common/storage-create-storage-account.md#manage-your-storage-account)できます。 

     ![ストレージ アカウントを入力する](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. **[サインイン]** ボタンをクリックします。 サインインが成功すると、**[データのインポート中]** 通知が表示されます。

    ![コンテンツ パックのインポート](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    しばらく待ってからインポートが完了すると、**[成功]** 通知が表示されます。 ストレージ アカウントに大量のデータがある場合は、コンテンツ パックのインポートに時間がかかることがあります。
    
    ![コンテンツ パックのインポート成功](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. データが正常にインポートされると、ナビゲーション ウィンドウの **[アプリ]** に **Azure Backup** コンテンツ パックが表示されます。 Azure Backup のダッシュボード、レポート、およびデータセットが、新しくインポートされたことを示す黄色のアスタリスク付きで一覧に表示されます。 

     ![Azure Backup コンテンツ パック](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. [ダッシュボード] の下の **[Azure Backup]** をクリックすると、ピン留めされた主要レポートのセットが表示されます。

      ![Azure Backup のダッシュボード](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. レポートの完全なセットを表示するには、ダッシュボードでいずれかのレポートをクリックします。

      ![Azure Backup のジョブの正常性](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. レポートの各タブをクリックすると、その領域のレポートが表示されます。

      ![Azure Backup レポートのタブ](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>よく寄せられる質問
1. **ストレージ アカウントへのレポート データのフローが開始されているかどうかはどのように確認できますか?**
    
    構成したストレージ アカウントに移動して、コンテナーを選択します。 コンテナーに insights-logs-azurebackupreport のエントリーがある場合は、レポート データのフローが既に開始されています。

2. **ストレージ アカウントと Power BI のコンテンツ パックへのデータ プッシュはどのような頻度で行われますか?**

   初めて利用するユーザーの場合は、ストレージ アカウントにデータがプッシュされるまでに約 24 時間かかります。 この初めてのプッシュが完了した後は、下の図に示す頻度でデータが更新されます。 
      * **ジョブ、アラート、バックアップ項目、コンテナー、保護されているサーバー、およびポリシー**に関するデータは、記録された時点で顧客のストレージ アカウントにプッシュされます。
      * **ストレージ**に関連するデータは、24 時間ごとに顧客のストレージ アカウントにプッシュされます。
   
    ![Azure Backup のレポートのデータ プッシュ頻度](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI では、[1 日 1 回のスケジュールされた更新](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)が行われます。 Power BI でコンテンツ パックのデータを手動で更新することもできます。

3. **レポートはどれだけの期間保持できますか?** 

   ストレージ アカウントを構成する際に、ストレージ アカウントでのレポート データのリテンション期間を選択できます (上で説明したレポート用ストレージ アカウントの構成手順 6 を使用します)。 また、[Excel でレポートを分析](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)し、必要に応じてさらに長い期間レポートを保存することもできます。 

4. **ストレージ アカウントを構成した後は、すべてのデータがレポートに表示されますか?**

   **"ストレージ アカウントの構成"** 後に生成されたデータはすべてストレージ アカウントにプッシュされ、レポートに利用できます。 ただし、レポートには**進行中のジョブはプッシュされません**。 ジョブは完了または失敗した後にレポートに送信されます。

5. **レポートを表示するストレージ アカウントを既に構成している場合、別のストレージ アカウントを使用するように構成を変更できますか?** 

   はい、別のストレージ アカウントをポイントするように構成を変更できます。 Azure Backup コンテンツ パックに接続する際に、新しく構成したストレージ アカウントを使用する必要があります。 別のストレージ アカウントを構成すると、新しいデータはそのストレージ アカウントにフローされます。 ただし、古い (構成を変更する前の) データは以前のストレージ アカウントに残ったままになります。

6. **レポートは複数のコンテナーおよび複数のサブスクリプションにわたって表示できますか?** 

   はい、複数のコンテナーに対して同じストレージ アカウントを構成して、複数のコンテナーにわたるレポートを表示できます。 また、複数のサブスクリプションの複数のコンテナーに対して同じストレージ アカウントを構成することもできます。 その後、Power BI で Azure Backup コンテンツ パックに接続する際にそのストレージ アカウントを使用して、レポートを表示できます。 ただし、選択するストレージ アカウントは Recovery Services コンテナーと同じリージョンにある必要があります。
   
## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
| エラーの詳細 | 解決策 |
| --- | --- |
| バックアップ レポート用のストレージ アカウントの設定後もなお、**[ストレージ アカウント]** に **[構成されていません]** と表示される。 | ストレージ アカウントを正常に構成した場合には、この問題が発生していてもレポート データが挿入されます。 この問題を解決するには、[Azure Portal]、[その他のサービス]、[診断設定]、[RS vault]\(RS コンテナー\)、[設定の編集] の順に移動します。 以前に構成した設定を削除し、同じブレードから新しい設定を作成する必要があります。 今回は、フィールド **[名前]** を **service** と設定します。 これにより、新たに構成されたストレージ アカウントが表示されます。 |
|Power BI で Azure Backup コンテンツ パックをインポートすると、エラー "**404 - コンテナーが見つかりません**" が表示される。 | このドキュメントで既に示したとおり、Recovery Services コンテナーでレポートを構成してから Power BI で正しくレポートが表示されるまでには、24 時間かかります。 24 時間が経過する前にレポートにアクセスしようとすると、有効なレポートを表示するうえで必要なデータが揃っていないため、このエラーが表示されます。 |

## <a name="next-steps"></a>次のステップ
ストレージ アカウントを構成し、Azure Backup コンテンツ パックをインポートしたので、次のステップではこれらのレポートのカスタマイズと、レポート データ モデルを使用したレポートの作成を行います。 詳細については、次の記事をご覧ください。

* [Azure Backup レポート データ モデルの使用](backup-azure-reports-data-model.md)
* [Power BI でレポートをフィルター処理する](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Power BI でレポートを作成する](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)


