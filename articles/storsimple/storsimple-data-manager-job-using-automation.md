---
title: Azure Automation を使用して StorSimple Data Manager でジョブを起動する
description: Azure Automation を使用して StorSimple Data Manager ジョブをトリガーする方法について説明します
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961211"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Azure Automation を使用してジョブをトリガーする

この記事では、StorSimple データ マネージャー サービスでデータ変換機能を使用して、StorSimple デバイス データを変換する方法について説明します。 次の 2 つの方法で、データ変換ジョブを起動することができます。 

 - .NET SDK を使用する
 - Azure Automation Runbook を使用する
 
この記事では、Azure Automation Runbook を作成し、それを使ってデータ変換ジョブを開始する方法について説明します。 .NET SDK を使ってデータ変換を開始する方法について詳しくは、「[.Net SDK を使用してデータ変換を開始する](storsimple-data-manager-dotnet-jobs.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

*   Azure PowerShell がクライアント コンピューターにインストールされている。 [Azure PowerShell をダウンロードする](/powershell/azure/azurerm/install-azurerm-ps)。
*   リソース グループ内の StorSimple Data Manager サービスに正しく構成されたジョブ定義がある。
*   GitHub リポジトリから [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) ファイルをダウンロードしてある。 
*   GitHub リポジトリから [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) スクリプトをダウンロードしてある。

## <a name="step-by-step-procedure"></a>詳細な手順

### <a name="set-up-the-automation-account"></a>Automation アカウントを設定する

1. Azure Portal で、Azure 実行アカウントを Automation として作成します。 そのためには、 **[Azure Marketplace] > [すべて]** に移動し、**Automation** を検索します。 **[Automation アカウント]** を選択します。

    ![Automation 実行アカウントを作成する](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. 新しい Automation アカウントを追加するには、 **[+ 追加]** をクリックします。

    ![Automation 実行アカウントを作成する 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. **[Automation アカウントの追加]** で、次のようにします。

   1. Automation アカウントの **[名前]** を指定します。
   2. StorSimple Data Manager サービスにリンクされている **[サブスクリプション]** を選びます。
   3. 新しいリソース グループを作成するか、既存のリソース グループを選択します。
   4. **[場所]** を選択します。
   5. **[Create Run As account]\(実行アカウントの作成\)** オプションは既定値の選択状態のままにします。
   6. ダッシュボードでのクイック アクセス用のリンクを取得するには、 **[ダッシュボードにピン留めする]** をオンにします。 **Create** をクリックしてください。

      ![Automation 実行アカウントを作成する 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Automation アカウントが正常に作成されると、その旨が通知されます。
    
      ![Automation アカウントの展開の通知](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      詳しくは、[実行アカウントの作成](../automation/manage-runas-account.md)に関するページをご覧ください。

3. 新しく作成されたアカウントで、 **[共有リソース] > [モジュール]** に移動し、 **[+ モジュールの追加]** をクリックします。

    ![モジュールのインポート 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. ローカル コンピューターで `DataTransformationApp.zip` ファイルの場所を参照し、モジュールを選んで開きます。 **[OK]** をクリックして、モジュールをインポートします。

    ![モジュールのインポート 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Automation は、モジュールをアカウントにインポートするときに、そのモジュールに関するメタデータを抽出します。 この操作には数分かかる場合があります。

   ![モジュールのインポート 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. モジュールをデプロイ中であることが通知され、プロセスが完了すると完了通知が表示されます。  **[モジュール]** の状態が **[利用可能]** に変わります。

    ![モジュールのインポート 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Automation Runbook をインポート、発行、および実行する

Runbook をインポート、発行、実行してジョブ定義をトリガーするには、次の手順を実行します。

1. Azure ポータルで、Automation アカウントを開きます。 **[プロセス オートメーション] > [Runbook]** に移動し、 **[+ Runbook の追加]** をクリックします。

    ![Runbook の追加 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. **[Runbook の追加]** で、 **[既存の Runbook のインポート]** をクリックします。

3. **[Runbook ファイル]** で、Azure PowerShell スクリプト ファイル `Trigger-DataTransformation-Job.ps1`を参照します。 Runbook の種類が自動的に選択されます。 Runbook の名前と説明 (オプション) を入力します。 **Create** をクリックしてください。

    ![Runbook の追加 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Automation アカウントの Runbook の一覧に新しい Runbook が表示されます。 この Runbook を選択してクリックします。

    ![Runbook の追加 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Runbook を編集し、 **[テスト]** ウィンドウをクリックします。

    ![Runbook の追加 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. StorSimple Data Manager サービスの名前、関連付けられているリソース グループ、ジョブ定義の名前などのパラメーターを指定します。 テストを **開始** します。 実行が完了すると、レポートが生成されます。 詳しくは、[Runbook のテスト](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook)方法に関するページをご覧ください。

    ![Runbook の追加 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. [テスト] ウィンドウで Runbook の出力を確認します。 問題がなければ、ウィンドウを閉じます。 **[発行]** をクリックします。確認を求められたら確定して Runbook を発行します。

    ![Runbook の追加 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. **[Runbook]** に戻り、新しく作成した Runbook を選びます。

    ![Runbook の追加 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. Runbook を **開始** します。 **[Runbook の開始]** で、すべてのパラメーターを入力します。 **[OK]** をクリックして送信し、データ変換ジョブを開始します。

10. Azure Portal でジョブの進行状況を監視するには、StorSimple Data Manager サービスの **[ジョブ]** に移動します。 ジョブを選んでクリックし、ジョブの詳細を表示します。

    ![Runbook の追加 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。