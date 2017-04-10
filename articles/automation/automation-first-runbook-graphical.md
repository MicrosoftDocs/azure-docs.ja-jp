---
title: "Azure Automation での初めてのグラフィカルな Runbook | Microsoft Docs"
description: "簡単なグラフィカル Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "Runbook, Runbook テンプレート, Runbook Automation, Azure Runbook"
ms.assetid: dcb88f19-ed2b-4372-9724-6625cd287c8a
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/06/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 0ab72bd4ad531d1162726c6f5548fa253a4f5265
ms.openlocfilehash: 6e5ccae8837491e042b85ba4b7d42227e194cb94


---
# <a name="my-first-graphical-runbook"></a>初めてのグラフィカルな Runbook

> [!div class="op_single_selector"]
> * [グラフィカル](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell ワークフロー](automation-first-runbook-textual.md)
> 
> 

このチュートリアルでは、Azure Automation で [グラフィカルな Runbook](automation-runbook-types.md#graphical-runbooks) を作成する手順を説明します。  簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を説明します。  その後、実際に Azure リソースを管理するように Runbook を変更し、ここでは Azure 仮想マシンを開始します。  そして、Runbook のパラメーターおよび条件付きリンクを追加することによって、Runbook をいっそう堅牢にします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを最後まで行うには、以下のものが必要です。

* として機能します。  まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、<a href="/pricing/free-account/" target="_blank">[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
* [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)。Runbook の保存と Azure リソースの認証に使用します。  このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。  マシンを停止して起動するので、運用環境のものは使用しないでください。

## <a name="step-1---create-new-runbook"></a>手順 1 - 新しい Runbook を作成する
最初に、 *Hello World*というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。  
   Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。  既に資産がいくつかあります。  これらのほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。  [前提条件](#prerequisites)で説明されている資格情報資産も必要です。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。<br> ![Runbook の制御](media/automation-first-runbook-graphical/runbooks-control.png)
3. Create a new runbook by clicking on the **Add a runbook** button and then **Create a new runbook**.
4. Runbook の名前を *MyFirstRunbook-Graphical*に設定します。
5. ここでは[グラフィカルな Runbook](automation-graphical-authoring-intro.md) を作成するため、**[Runbook の種類]** では **[グラフィカル]** を選択します。<br> ![新しい Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **[作成]** をクリックして Runbook を作成し、グラフィカル エディターを開きます。

## <a name="step-2---add-activities-to-the-runbook"></a>手順 2 - Runbook にアクティビティを追加する
エディターの左側にあるライブラリ コントロールでは、Runbook に追加するアクティビティを選択できます。  Runbook のテキストを出力する **Write-Output** コマンドレットを追加します。

1. ライブラリ コントロールの検索ボックスをクリックして、「 **Write-Output**」と入力します。  下に検索結果が表示されます。 <br> ![[[Microsoft.PowerShell.Utility]]](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2. 一覧の一番下までスクロールします。  **Write-Output** を右クリックして **[Add to canvas (キャンバスに追加)]** を選択するか、コマンドレットの横にある省略記号ボタンをクリックして **[Add to canvas (キャンバスに追加)]** を選択します。
3. キャンバスで **[Write-Output]** アクティビティをクリックします。  [構成コントロール] ブレードが開き、アクティビティを構成できます。
4. **[ラベル]** の既定値はコマンドレットの名前ですが、もっとわかりやすい名前に変更できます。 「 *Write Hello World to output*」に変更します。
5. **[パラメーター]** をクリックし、コマンドレットのパラメーターの値を指定します。  
   一部のコマンドレットには複数のパラメーター セットがあり、使用するものを選択する必要があります。 この場合、 **[Write-Output]** のパラメーター セットは 1 つだけなので、選択する必要はありません。 <br> ![Write-Output のプロパティ](media/automation-first-runbook-graphical/write-output-properties-b.png)
6. **[InputObject]** パラメーターを選択します。  このパラメーターでは、出力ストリームに送信するテキストを指定します。
7. **[データ ソース]** ドロップダウンで、**[PowerShell expression (PowerShell 式)]** を選択します。  **[データ ソース]** ドロップダウンでは、パラメーター値の設定に使用するさまざまなソースが提供されます。  
   別のアクティビティ、Automation の資産、PowerShell 式などのソースからの出力を使用できます。  この場合は、 *Hello World*というテキストを出力するだけです。 PowerShell 式を使用して文字列を指定すればそれが可能です。
8. **[式]** ボックスに「*"Hello World"*」と入力し、**[OK]** を 2 回クリックしてキャンバスに戻ります。<br> ![PowerShell Expression](media/automation-first-runbook-graphical/expression-hello-world.png)
9. **[保存]**をクリックして Runbook を保存します。<br> ![Runbook の保存](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする
Runbook を発行して運用環境で使用できるようにする前に、Runbook をテストして正常に動作することを確認します。  Runbook をテストするときは、 **ドラフト** バージョンを実行し、その出力を対話形式で表示します。

1. **[テスト ウィンドウ]** をクリックして、[テスト] ブレードを開きます。<br> ![Test pane](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2. **[開始]** をクリックしてテストを開始します。  有効なオプションはこれだけです。
3. [Runbook ジョブ](automation-runbook-execution.md) が作成され、その状態がペインに表示されます。  
   最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。  その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
4. Runbook ジョブが完了すると、その出力が表示されます。 この例では、*Hello World* と表示されます。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5. [テスト] ブレードを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する
前の手順で作成した Runbook はまだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。  Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。  この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。<br> ![[発行]](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2. **[Runbook]** ブレードで左へスクロールして Runbook を表示すると、**[編集状態]** は **[発行済み]** になっています。
3. 右へスクロールして戻り、 **MyFirstRunbook**のブレードを表示します。  
   上部のオプションを使用すると、Runbook の開始、将来の開始スケジュールの設定、または HTTP 呼び出しで開始できるようにする [Webhook](automation-webhooks.md) の作成を行うことができます。
4. ここでは単純に Runbook を開始するので、**[開始]** をクリックし、確認を求められたら **[はい]** をクリックします。<br> ![Runbook の開始](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5. 作成した Runbook ジョブのジョブ ブレードが開かれます。  このブレードは閉じてもかまいませんが、ここではジョブの進行状況を確認できるように開いたままにします。
6. ジョブの状態が **[ジョブの概要]** に表示され、Runbook をテストしたときに確認した状態と一致しています。<br> ![ジョブの概要](media/automation-first-runbook-graphical/runbook-job-summary.png)
7. Runbook の状態が *[完了]*になったら、 **[出力]**をクリックします。 **[出力]** ブレードが開き、ウィンドウに *Hello World* と表示されます。<br> ![ジョブの概要](media/automation-first-runbook-graphical/runbook-job-output.png)  
8. [出力] ブレードを閉じます。
9. **[すべてのログ]** をクリックして、Runbook ジョブの [ストリーム] ブレードを開きます。  出力ストリームでは *Hello World* だけが表示されますが、Runbook が詳細やエラーに書き込んでいる場合は、これらの Runbook ジョブの他のストリームも表示できます。<br> ![ジョブの概要](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. [すべてのログ] ブレードと [ジョブ] ブレードを閉じて、MyFirstRunbook ブレードに戻ります。
11. **[ジョブ]** をクリックして、この Runbook の [ジョブ] ブレードを開きます。  この Runbook によって作成されたジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。<br> ![ジョブ](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. このジョブをクリックすると、Runbook を開始したときに表示されたものと同じジョブ ウィンドウが開きます。  これにより前に戻って、特定の Runbook に対して作成されたジョブの詳細を見ることができます。

## <a name="step-5---create-variable-assets"></a>手順 5 - 変数アセットを作成する
Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 ここでは、Runbook で Azure リソースを管理します。  Runbook が認証されるよう構成する前に、サブスクリプション ID を格納する変数を作成します。次の手順 6. で、認証のアクティビティを設定した後でこの変数を参照します。  サブスクリプション コンテキストに参照を含めると、複数のサブスクリプション間での作業がしやすくなります。  次に進む前に、ナビゲーション ウィンドウの [サブスクリプション] からサブスクリプション ID をコピーしておきます。  

1. [Automation アカウント] ブレードの **[資産]** タイルをクリックすると、**[資産]** ブレードが開きます。
2. [資産] ブレードの **[変数]** タイルをクリックします。
3. [変数] ブレードで、**[変数の追加]** をクリックします。<br>![Automation の変数](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. [新しい変数] ブレードで、**[名前]** ボックスに「**AzureSubscriptionId**」と入力し、**[値]** ボックスにサブスクリプション ID を入力します。  **[型]** は *[文字列]* のままにして、**[暗号化]** も既定値のままにします。  
5. **[作成]** をクリックして変数を作成します。  

## <a name="step-6---add-authentication-to-manage-azure-resources"></a>手順 6 - Azure リソースを管理するための認証を追加する
これで、サブスクリプション ID を保持する変数を作成できました。次に、「[前提条件](#prerequisites)」で説明した実行アカウントの資格情報を使って認証を受けるように Runbook を構成します。  これを行うには、Azure 実行アカウントの接続**資産**と **Add-AzureRMAccount** コマンドレットをキャンバスに追加します。  

1. MyFirstRunbook ブレードで **[編集]** をクリックして、グラフィカル エディターを開きます。<br> ![Edit runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2. **Write Hello World to output** はもう必要ないので、右クリックして **[削除]** を選択します。
3. ライブラリ コントロールで **[接続]** を展開し、**[Add to canvas (キャンバスに追加)]** を選択して、**AzureRunAsConnection** をキャンバスに追加します。
4. キャンバスで **AzureRunAsConnection** を選択し、[構成コントロール] ウィンドウの **[ラベル]** ボックスに「**Get Run As Connection**」と入力します。  これは接続です。
5. ライブラリ コントロールの検索ボックスに「 **Add-AzureRmAccount** 」と入力します。
6.  **-AzureRmAccount** をキャンバスに追加します。<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7. **Get Run As Connection** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **Add-AzureRmAccount**してください。  作成した矢印は "*リンク*" です。  **Get Run As Connection** で Runbook が開始され、**Add-AzureRmAccount** が実行されます。<br> ![アクティビティ間のリンクの作成](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8. キャンバスで **Add-AzureRmAccount** を選択し、[構成コントロール] ウィンドウの **[ラベル]** ボックスに「**Login to Azure**」と入力します。
9. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ブレードが表示されます。
10. **Add-AzureRmAccount** には複数のパラメーター セットがあるため、パラメーター値を指定する前に、1 つのパラメーター セットを選択する必要があります。  **[パラメーター セット]** をクリックして、**ServicePrincipalCertificate** パラメーター セットを選択します。
11. パラメーター セットを選択すると、[アクティビティ パラメーター構成] ブレードにパラメーターが表示されます。  **[APPLICATIONID]** をクリックします。<br> ![Azure RM アカウントのパラメーターの追加](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12. [パラメーター値] ブレードで、**[データ ソース]** に **[Activity output (アクティビティの出力)]** を選択します。一覧から **Get Run As Connection** を選択し、**[フィールド パス]** ボックスに「**ApplicationId**」と入力して、**[OK]** をクリックします。  フィールド パスにプロパティ名を指定するのは、アクティビティによって複数のプロパティを持つオブジェクトが出力されるためです。
13. **CERTIFICATETHUMBPRINT** をクリックし、[パラメーター値] ブレードで、**[データ ソース]** に **[Activity output (アクティビティの出力)]** を選択します。  一覧から **Get Run As Connection** を選択し、**[フィールド パス]** テキストに「**CertificateThumbprint**」と入力して、**[OK]** をクリックします。
14. **SERVICEPRINCIPAL** をクリックし、[パラメーター値] ブレードで **[データ ソース]** に **ConstantValue** を選択します。オプションの **[True]** をクリックし、**[OK]** をクリックします。
15. **TENANTID** をクリックし、[パラメーター値] ブレードで、**[データ ソース]** に **[Activity output (アクティビティの出力)]** を選択します。  一覧から **Get Run As Connection** を選択し、**[フィールド パス]** ボックスに「**TenantId**」と入力して、**[OK]** を 2 回クリックします。  
16. ライブラリ コントロールの検索ボックスに「 **Set-AzureRmContext** 」と入力します。
17. **Set-AzureRmContext** をキャンバスに追加します。
18. キャンバスで **Set-AzureRmContext** を選択し、[構成コントロール] ウィンドウの **[ラベル]** ボックスに「**Specify Subscription Id**」と入力します。
19. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ブレードが表示されます。
20. **Set-AzureRmContext** には複数のパラメーター セットがあるため、パラメーター値を指定する前に、1 つのパラメーター セットを選択する必要があります。  **[パラメーター セット]** をクリックして、**SubscriptionId** パラメーター セットを選択します。  
21. パラメーター セットを選択すると、[アクティビティ パラメーター構成] ブレードにパラメーターが表示されます。   **SubscriptionID**
22. [パラメーター値] ブレードで、**[データ ソース]** に **[Variable Asset (変数資産)]** を選択し、一覧から **AzureSubscriptionId** を選択して、**[OK]** を 2 回クリックします。   
23. **Login to Azure** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **[Specify Subscription Id]**までドラッグします。

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>手順 7 - 仮想マシンを開始するアクティビティを追加する
次に、仮想マシンを起動するための **Start-AzureRmVM** アクティビティを追加します。  Azure サブスクリプション内の任意の仮想マシンを選択し、ここではその名前をコマンドレットにハードコーディングします。

1. ライブラリ コントロールの検索ボックスに「 **Start-AzureRm** 」と入力します。
2. **Start-AzureRmVM** をキャンバスに追加した後、それをクリックして **Specify Subscription Id** の下にドラッグします。
3. **[Specify Subscription Id]** にポインターを合わせて、図形の下部に円を表示します。  円をクリックし、矢印を **Start-AzureRmVM**までドラッグします。
4. **Start-AzureRmVM**を選択します。  **[パラメーター]**、**[パラメーター セット]** の順にクリックし、**Start-AzureRmVM** のセットを表示します。  **ResourceGroupNameParameterSetName** パラメーター セットを選択します。 **ResourceGroupName** と **Name** の横に感嘆符が付いていることに注意してください。  これは、必須のパラメーターであることを示します。  また、どちらのパラメーターも文字列値を受け取ることにも注意してください。
5. 一覧から **Name**してください。  **[データ ソース]** には **[PowerShell expression (PowerShell 式)]** を選択し、この Runbook で開始する仮想マシンの名前を二重引用符で囲んで入力します。  **[OK]**をクリックします。<br>![Start-AzureRmVM Name パラメーターの値](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6. 一覧から **ResourceGroupName**してください。 **[データ ソース]** には **[PowerShell expression (PowerShell 式)]** を選択し、リソース グループの名前を二重引用符で囲んで入力します。  **[OK]**をクリックします。<br> ![Start-AzureRmVM パラメーター](media/automation-first-runbook-graphical/startazurermvm-params.png)
7. テスト ウィンドウをクリックして、Runbook をテストできるようにします。
8. **[開始]** をクリックしてテストを開始します。  完了したら、仮想マシンが開始されたことを確認します。

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters-to-the-runbook"></a>手順 8 - Runbook に追加の入力パラメーターを追加する
現時点の Runbook は **Start-AzureRmVM** コマンドレットで指定したリソース グループ内の仮想マシンを開始しますが、Runbook を開始するときに両方とも指定できるようにすればもっと便利になります。  そこで次に、入力パラメーターを Runbook に追加して、その機能を提供します。

1. Open the graphical editor by clicking **Edit** on the **MyFirstRunbook** pane.
2. **[入力と出力]**、**[入力の追加]** の順にクリックして、Runbook 入力パラメーター ウィンドウを開きます。<br> ![Runbook の入力と出力](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. *[名前]* を「 **VMName**」と指定します。  Keep *string* for the **Type**, but change **Mandatory** to *Yes*.  **[OK]**をクリックします。
4. *ResourceGroupName* という名前の 2 番目の必須入力パラメーターを作成し、**[OK]** をクリックして **[入力と出力]** ウィンドウを閉じます。<br> ![Runbook の入力パラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. **Start-AzureRmVM** アクティビティを選択し、**[パラメーター]** をクリックします。
6. **Name** の **[データ ソース]** を **[Runbook input (Runbook の入力)]** に変更し、**VMName** を選択します。<br>
7. **ResourceGroupName** の **[データ ソース]** を **[Runbook input (Runbook の入力)]** に変更し、**ResourceGroupName** を選択します。<br> ![Start-AzureVM のパラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Runbook を保存してテスト ウィンドウを開きます。  テストで使用される 2 つの入力変数の値を指定できることに注意してください。
9. テスト ウィンドウを閉じます。
10. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
11. 前の手順で開始した仮想マシンを停止します。
12. **[開始]** をクリックして Runbook を開始します。  起動する仮想マシンの **VMName** と **ResourceGroupName** を入力します。<br> ![Start Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Runbook が完了したら、仮想マシンが開始されたことを確認します。

## <a name="step-9---create-a-conditional-link"></a>手順 9 - 条件付きリンクを作成する
次に、まだ開始されていない場合にのみ仮想マシンを開始するように Runbook を変更します。  そのためには、仮想マシンのインスタンス レベルの状態を取得する **Get-AzureRmVM** コマンドレットを Runbook に追加します。 次に、**Get Status** という名前の PowerShell Workflow コード モジュールを追加します。これには、仮想マシンが実行中か停止中かを調べるための PowerShell コード スニペットが含まれています。  **Get Status** モジュールの条件付きリンクは、現在の実行状態が停止の場合のみ **Start-AzureRmVM** を実行します。  次に、PowerShell Write-Output コマンドレットを使用して、VM が正常に開始されたかどうかを通知するメッセージを出力します。

1. グラフィカル エディターで **MyFirstRunbook** を開きます。
2. **Specify Subscription Id** と **Start-AzureRmVM** の間のリンクをクリックし、*Delete* キーを押して、リンクを削除します。
3. ライブラリ コントロールの検索ボックスに「 **Get-AzureRm** 」と入力します。
4. **Get-AzureRmVM** をキャンバスに追加します。
5. **Get-AzureRmVM**、**[パラメーター セット]** の順にクリックし、**Get-AzureRmVM** のセットを表示します。  **GetVirtualMachineInResourceGroupNameParamSet** パラメーター セットを選択します。  **ResourceGroupName** と **Name** の横に感嘆符が付いていることに注意してください。  これは、必須のパラメーターであることを示します。  また、どちらのパラメーターも文字列値を受け取ることにも注意してください。
6. **Name** の **[データ ソース]** で **[Runbook input (Runbook の入力)]** を選択した後、**VMName** を選択します。  **[OK]**をクリックします。
7. **ResourceGroupName** の **[データ ソース]** で、**[Runbook input (Runbook の入力)]** を選択した後、**ResourceGroupName** を選択します。  **[OK]**をクリックします。
8. **Status** の **[データ ソース]** で、**[定数値]** を選択した後、**[True]** をクリックします。  **[OK]**をクリックします。  
9. **Specify Subscription Id** から **Get-AzureRmVM** へのリンクを作成します。
10. ライブラリ コントロールで **[Runbook の制御]** を展開し、**Code** をキャンバスに追加します。  
11. **Get-AzureRmVM** から **Code** へのリンクを作成します。  
12. 構成ウィンドウで **Code** をクリックし、ラベルを **Get Status** に変更します。
13. **Code** パラメーターを選択すると、**[コード エディター]** ブレードが表示されます。  
14. コード エディターに、次のコード スニペットを貼り付けます。
    
     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```
15. **Get Status** から **Start-AzureRmVM** へのリンクを作成します。<br> ![コード モジュールを含む Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. リンクを選択し、構成ウィンドウの **[Apply condition (条件の適用)]** を **[はい]** に変更します。   条件が満たされた場合にのみ対象のアクティビティが実行されることを示す破線にリンクが変わることに注意してください。  
17. **[条件式]**に「 *$ActivityOutput['Get Status'] -eq "Stopped"*」と入力します。  **Start-AzureRmVM** は、仮想マシンが停止している場合にのみ実行されるようになります。
18. In the Library control, expand **Cmdlets** and then **Microsoft.PowerShell.Utility**.
19. **Write-Output** をキャンバスに 2 回追加します。<br> ![Write-Output での Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. 1 個目の **Write-Output** コントロールで、**[パラメーター]** をクリックし、**[ラベル]** の値を *Notify VM Started* に変更します。
21. **InputObject** の **[データ ソース]** を **[PowerShell expression (PowerShell 式)]** に変更し、式に「*$VMName successfully started.*」と入力します。
22. 2 個目の **Write-Output** コントロールで、**[パラメーター]** をクリックし、**[ラベル]** の値を *Notify VM Start Failed* に変更します
23. **InputObject** の **[データ ソース]** を **[PowerShell expression (PowerShell 式)]** に変更し、式に「*$VMName could not start.*」と入力します。
24. **Start-AzureRmVM** から **Notify VM Started** および **Notify VM Start Failed** へのリンクを作成します。
25. **Notify VM Started** へのリンクを選択し、**[Apply condition (条件の適用)]** を **True** に変更します。
26. **[条件式]**に「 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*」と入力します。  これで、この Write-Output コントロールは仮想マシンが正常に開始された場合にのみ実行されるようになりました。
27. **Notify VM Start Failed** へのリンクを選択し、**[Apply condition (条件の適用)]** を **True** に変更します。
28. **[条件式]**に「 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*」と入力します。  これで、この Write-Output コントロールは仮想マシンが正常に開始されなかった場合にのみ実行されるようになりました。
29. Runbook を保存してテスト ウィンドウを開きます。
30. 仮想マシンが停止している状態で Runbook を開始すると、仮想マシンが開始します。

## <a name="next-steps"></a>次のステップ
* グラフィカル作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* PowerShell Runbook の使用を開始するには、「 [初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)




<!--HONumber=Nov16_HO2-->


