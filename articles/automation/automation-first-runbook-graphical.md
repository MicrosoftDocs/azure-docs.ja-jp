---
title: Azure Automation での初めてのグラフィカルな Runbook
description: 簡単なグラフィカル Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。
keywords: Runbook, Runbook テンプレート, Runbook Automation, Azure Runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cdd015d9f29c3fb672d626f32a485271e2757c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850314"
---
# <a name="my-first-graphical-runbook"></a>初めてのグラフィカルな Runbook

> [!div class="op_single_selector"]
> * [グラフィカル](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell ワークフロー](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

このチュートリアルでは、Azure Automation で [グラフィカルな Runbook](automation-runbook-types.md#graphical-runbooks) を作成する手順を説明します。 最初に、簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を学習します。 次に、実際に Azure リソースを管理するように Runbook を変更します。ここでは Azure 仮想マシンを開始します。 チュートリアルの最後で、Runbook のパラメーターおよび条件付きリンクを追加することによって、Runbook をいっそう堅牢にします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](automation-offering-get-started.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="create-runbook"></a>Runbook を作成する

最初に、"*Hello World*" というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

   Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 [前提条件](#prerequisites)で説明されている資格情報資産も必要です。

2. **[プロセス管理]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[+ Runbook の追加]** を選択し、 **[新しい Runbook の作成]** をクリックして新しい Runbook を作成します。
4. Runbook の名前を *MyFirstRunbook-Graphical*に設定します。
5. ここでは[グラフィカルな Runbook](automation-graphical-authoring-intro.md) を作成するため、 **[Runbook の種類]** で **[グラフィック]** を選択します。<br> ![新しい Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **[作成]** をクリックして Runbook を作成し、グラフィカル エディターを開きます。

## <a name="add-activities"></a>アクティビティを追加する

エディターの左側にあるライブラリ コントロールでは、Runbook に追加するアクティビティを選択できます。 Runbook のテキストを出力する **Write-Output** コマンドレットを追加します。

1. ライブラリ コントロールの検索ボックスをクリックして、「 **Write-Output**」と入力します。 検索結果は、次の図のとおりです。 <br> ![[[Microsoft.PowerShell.Utility]]](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 一覧の一番下までスクロールします。 **[Write-Output]** を右クリックして **[キャンバスに追加]** を選択するか、コマンドレットの横にある省略記号ボタンをクリックして **[キャンバスに追加]** を選択します。
1. キャンバスで **[Write-Output]** アクティビティをクリックします。 この操作によって [構成コントロール] ページが開き、アクティビティを構成できます。
1. **[ラベル]** の既定値はコマンドレットの名前ですが、もっとわかりやすい名前に変更できます。 「 *Write Hello World to output*」に変更します。
1. **[パラメーター]** をクリックし、コマンドレットのパラメーターの値を指定します。

   一部のコマンドレットには複数のパラメーター セットがあり、どれを使用するかを選択する必要があります。 この場合、 **[Write-Output]** のパラメーター セットは 1 つだけなので、選択する必要はありません。

1. **[InputObject]** パラメーターを選択します。 このパラメーターでは、出力ストリームに送信するテキストを指定します。
1. **[データ ソース]** ドロップダウンで、 **[PowerShell expression (PowerShell 式)]** を選択します。 **[データ ソース]** ドロップダウンでは、パラメーター値の設定に使用するさまざまなソースが提供されます。

   別のアクティビティ、Automation の資産、PowerShell 式などのソースからの出力を使用できます。 この場合、出力は単に "*Hello World*" です。 PowerShell 式を使用して文字列を指定することができます。<br>

1. **[式]** ボックスに「 *"Hello World"* 」と入力し、 **[OK]** を 2 回クリックしてキャンバスに戻ります。
1. **[保存]** をクリックして Runbook を保存します。

## <a name="test-the-runbook"></a>Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、Runbook をテストして正常に動作することを確認します。 Runbook をテストするときは、 **ドラフト** バージョンを実行し、その出力を対話形式で表示します。

1. **[テスト ウィンドウ]** を選択して、[テスト] ページを開きます。
1. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。
1. [Runbook ジョブ](automation-runbook-execution.md) が作成され、その状態がペインに表示されます。

   最初のジョブの状態は "*キューに設定*" であり、クラウドの runbook worker が使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると "*開始中*" になり、Runbook が実際に実行を開始すると "*実行中*" になります。

1. Runbook ジョブが完了すると、その出力が表示されます。 この場合は、"*Hello World*" です。<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. [テスト] ページを閉じてキャンバスに戻ります。

## <a name="publish-and-start-the-runbook"></a>Runbook を発行して開始する

前の手順で作成した Runbook は、まだドラフト モードです。 運用環境で実行する前に、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** を選択して Runbook を発行し、確認を要求されたら **[はい]** をクリックします。
1. **[Runbook]** ページで左へスクロールして Runbook を表示すると、 **[編集状態]** は **[発行済み]** になっています。
1. 右へスクロールして戻り、**MyFirstRunbook-Graphical** ページを表示します。

   上部のオプションを使用すると、Runbook の開始、将来の開始スケジュールの設定、または HTTP 呼び出しで開始できるようにする [Webhook](automation-webhooks.md) の作成を行うことができます。

1. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。
1. 作成した Runbook ジョブのジョブ ページが開かれます。 **[ジョブの状態]** に **[完了]** と表示されていることを確認します。
1. Runbook の状態が *[完了]* になったら、 **[出力]** をクリックします。 **[出力]** ページが開き、ウィンドウに "*Hello World*" と表示されます。
1. [出力] ページを閉じます。
1. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ページを開きます。 出力ストリームでは "*Hello World*" だけが表示されますが、Runbook が詳細やエラーに書き込んでいる場合は、これらの Runbook ジョブの他のストリームも表示できます。
1. [すべてのログ] ページと [ジョブ] ページを閉じて、MyFirstRunbook-Graphical ページに戻ります。
1. Runbook のすべてのジョブを表示するには、 **[ジョブ]** ページを閉じて、 **[リソース]** の **[ジョブ]** を選択します。 この Runbook によって作成されたジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。
1. このジョブをクリックすると、Runbook を開始したときに表示されたのと同じジョブ ウィンドウが開きます。 これにより前に戻って、特定の Runbook に対して作成されたジョブの詳細を見ることができます。

## <a name="create-variable-assets"></a>変数アセットを作成する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 Runbook の認証を構成する前に、サブスクリプション ID を格納する変数を作成します。次の手順 6. で、認証のアクティビティを設定した後でこの変数を参照します。 サブスクリプション コンテキストに参照を含めると、複数のサブスクリプション間での作業がしやすくなります。 次に進む前に、ナビゲーション ウィンドウの [サブスクリプション] からサブスクリプション ID をコピーしておきます。

1. [Automation アカウント] ページで、 **[共有リソース]** の **[変数]** を選択します。
1. **[変数の追加]** を選択します。
1. [新しい変数] ページで、 **[名前]** ボックスに「**AzureSubscriptionId**」と入力し、 **[値]** ボックスにサブスクリプション ID を入力します。 **[型]** は *[文字列]* のままにして、 **[暗号化]** も既定値のままにします。
1. **[作成]** をクリックして変数を作成します。

## <a name="add-authentication"></a>認証を追加する

これで、サブスクリプション ID を保持する変数を作成できました。次に、「[前提条件](#prerequisites)」で説明した実行資格情報を使って認証を受けるように Runbook を構成します。 これを行うには、Azure 実行アカウントの接続**資産**および **Connect-AzureRmAccount** コマンドレットをキャンバスに追加します。

1. Runbook に戻り、MyFirstRunbook-Graphical ページの **[編集]** を選択します。
1. **Write Hello World to output** はもう必要ないので、省略記号ボタン ([...]) をクリックして **[削除]** を選択します。
1. ライブラリ コントロールで **[資産]** 、 **[接続]** の順に展開し、 **[キャンバスに追加]** を選択して、**AzureRunAsConnection** をキャンバスに追加します。
1. ライブラリ コントロールの検索ボックスに「**Connect-AzureRmAccount**」と入力します。

   > [!IMPORTANT]
   > これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントでモジュールを更新できます。

1. **Connect-AzureRmAccount** をキャンバスに追加します。
1. **Get Run As Connection** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **Connect-AzureRmAccount** にドラッグします。 作成した矢印は "*リンク*" です。 Runbook で、まず **Get Run As Connection** が実行され、次に **Connect-AzureRmAccount** が実行されます。<br> ![アクティビティ間のリンクの作成](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. キャンバスで **Connect-AzureRmAccount** を選択し、[構成コントロール] ウィンドウの **[ラベル]** ボックスに「**Login to Azure**」と入力します。
1. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。
1. **Connect-AzureRmAccount** には複数のパラメーター セットがあるため、パラメーター値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** をクリックして、**ServicePrincipalCertificate** パラメーター セットを選択します。
1. パラメーター セットを選択すると、[アクティビティ パラメーター構成] ページにパラメーターが表示されます。 **[APPLICATIONID]** をクリックします。<br> ![Azure RM アカウントのパラメーターの追加](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. [パラメーター値] ページで、 **[データ ソース]** に **[アクティビティの出力]** を選択します。一覧から **Get Run As Connection** を選択し、 **[フィールド パス]** ボックスに「**ApplicationId**」と入力して、 **[OK]** をクリックします。 フィールド パスにプロパティ名を指定するのは、アクティビティによって複数のプロパティを備えたオブジェクトが出力されるためです。
1. **CERTIFICATETHUMBPRINT** をクリックし、[パラメーター値] ページで、 **[データ ソース]** に **[アクティビティの出力]** を選択します。 一覧から **Get Run As Connection** を選択し、 **[フィールド パス]** テキストに「**CertificateThumbprint**」と入力して、 **[OK]** をクリックします。
1. **SERVICEPRINCIPAL** をクリックし、[パラメーター値] ページで **[データ ソース]** に **ConstantValue** を選択します。オプションの **[True]** をクリックし、 **[OK]** をクリックします。
1. **TENANTID** をクリックし、[パラメーター値] ページで、 **[データ ソース]** に **[アクティビティの出力]** を選択します。 一覧から **Get Run As Connection** を選択し、 **[フィールド パス]** ボックスに「**TenantId**」と入力して、 **[OK]** を 2 回クリックします。
1. ライブラリ コントロールの検索ボックスに「 **Set-AzureRmContext** 」と入力します。
1. **Set-AzureRmContext** をキャンバスに追加します。
1. キャンバスで **Set-AzureRmContext** を選択し、[構成コントロール] ウィンドウの **[ラベル]** ボックスに「**Specify Subscription Id**」と入力します。
1. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。
1. **Set-AzureRmContext** には複数のパラメーター セットがあるため、パラメーター値を指定する前に、1 つのパラメーター セットを選択する必要があります。 **[パラメーター セット]** をクリックして、**SubscriptionId** パラメーター セットを選択します。
1. パラメーター セットを選択すると、[アクティビティ パラメーター構成] ページにパラメーターが表示されます。 **SubscriptionID** をクリックします。
1. [パラメーター値] ページで、 **[データ ソース]** に **[変数資産]** を選択し、一覧から **AzureSubscriptionId** を選択して、 **[OK]** を 2 回クリックします。
1. **Login to Azure** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **[Specify Subscription Id]** までドラッグします。

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>VM を開始するアクティビティを追加する

ここで、仮想マシンを起動するための **Start-AzureRmVM** アクティビティを追加します。 Azure サブスクリプション内の任意の仮想マシンを選択し、ここではその名前をコマンドレットにハードコーディングします。

1. ライブラリ コントロールの検索ボックスに「 **Start-AzureRm** 」と入力します。
2. **Start-AzureRmVM** をキャンバスに追加した後、それをクリックして **Specify Subscription Id** の下にドラッグします。
1. **[Specify Subscription Id]** にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を **Start-AzureRmVM**までドラッグします。
1. **Start-AzureRmVM**を選択します。 **[パラメーター]** 、 **[パラメーター セット]** の順にクリックし、**Start-AzureRmVM** のセットを表示します。 **ResourceGroupNameParameterSetName** パラメーター セットを選択します。 **ResourceGroupName** と **Name** の横に感嘆符が付いています。 これは、必須のパラメーターであることを示します。 また、どちらのパラメーターも文字列値を受け取ることにも注意してください。
1. 一覧から **Name**してください。 **[データ ソース]** には **[PowerShell 式]** を選択し、この Runbook で開始する仮想マシンの名前を二重引用符で囲んで入力します。 Click **OK**.
1. 一覧から **ResourceGroupName**してください。 **[データ ソース]** には **[PowerShell expression (PowerShell 式)]** を選択し、リソース グループの名前を二重引用符で囲んで入力します。 Click **OK**.
1. テスト ウィンドウをクリックして、Runbook をテストできるようにします。
1. **[開始]** をクリックしてテストを開始します。 完了したら、仮想マシンが開始されたことを確認します。

この時点の Runbook は次のようになります。 <br>![Runbook 認証の構成](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>追加の入力パラメーターを追加する

Runbook は、現時点では、**Start-AzureRmVM** コマンドレットで指定したリソース グループ内の仮想マシンを開始します。 Runbook を開始するときにも指定できれば、この Runbook はもっと便利になります。 ここで、その機能を備えるために、Runbook に入力パラメーターを追加します。

1. **MyFirstRunbook** ウィンドウで **[編集]** をクリックすると、グラフィカル エディターが開きます。
1. **[入力と出力]** 、 **[入力の追加]** の順に選択して、Runbook 入力パラメーター ウィンドウを開きます。
1. *[名前]* を「 **VMName**」と指定します。 Keep *string* for the **Type**, but change **Mandatory** to *Yes*. Click **OK**.
1. *ResourceGroupName* という名前の 2 番目の必須入力パラメーターを作成し、 **[OK]** をクリックして **[入力と出力]** ウィンドウを閉じます。<br> ![Runbook の入力パラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. **Start-AzureRmVM** アクティビティを選択し、 **[パラメーター]** をクリックします。
1. **Name** の **[データ ソース]** を **[Runbook input (Runbook の入力)]** に変更し、**VMName** を選択します。
1. **ResourceGroupName** の **[データ ソース]** を **[Runbook input (Runbook の入力)]** に変更し、**ResourceGroupName** を選択します。<br> ![Start-AzureVM のパラメーター](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Runbook を保存してテスト ウィンドウを開きます。 テストで使用する 2 つの入力変数の値を指定できるようになりました。
1. テスト ウィンドウを閉じます。
1. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
1. 前の手順で開始した仮想マシンを停止します。
1. **[開始]** をクリックして Runbook を開始します。 起動する仮想マシンの **VMName** と **ResourceGroupName** を入力します。
1. Runbook が完了したら、仮想マシンが開始されたことを確認します。

## <a name="create-a-conditional-link"></a>条件付きリンクを作成する

次に、まだ開始されていない場合にのみ仮想マシンを開始するように Runbook を変更します。 そのためには、仮想マシンのインスタンス レベルの状態を取得する **Get-AzureRmVM** コマンドレットを Runbook に追加します。 次に、**Get Status** という名前の PowerShell Workflow コード モジュールを追加します。これには、仮想マシンが実行中か停止中かを調べるための PowerShell コード スニペットが含まれています。 **Get Status** モジュールの条件付きリンクは、現在の実行状態が停止の場合にのみ、**Start-AzureRmVM** を実行します。 最後に、PowerShell Write-Output コマンドレットを使用して、VM が正常に開始されたかどうかを通知するメッセージを出力します。

1. グラフィカル エディターで **MyFirstRunbook-Graphical** を開きます。
1. **Specify Subscription Id** と **Start-AzureRmVM** の間のリンクをクリックし、*Delete* キーを押して、リンクを削除します。
1. ライブラリ コントロールの検索ボックスに「 **Get-AzureRm** 」と入力します。
1. **Get-AzureRmVM** をキャンバスに追加します。
1. **Get-AzureRmVM**、 **[パラメーター セット]** の順にクリックし、**Get-AzureRmVM** のセットを表示します。 **GetVirtualMachineInResourceGroupNameParamSet** パラメーター セットを選択します。 **ResourceGroupName** と **Name** の横に感嘆符が付いています。 これは、必須のパラメーターであることを示します。 また、どちらのパラメーターも文字列値を受け取ることにも注意してください。
1. **Name** の **[データ ソース]** で **[Runbook input (Runbook の入力)]** を選択した後、**VMName** を選択します。 Click **OK**.
1. **ResourceGroupName** の **[データ ソース]** で、 **[Runbook input (Runbook の入力)]** を選択した後、**ResourceGroupName** を選択します。 Click **OK**.
1. **Status** の **[データ ソース]** で、 **[定数値]** を選択した後、 **[True]** をクリックします。 Click **OK**.
1. **Specify Subscription Id** から **Get-AzureRmVM** へのリンクを作成します。
1. ライブラリ コントロールで **[Runbook の制御]** を展開し、**Code** をキャンバスに追加します。  
1. **Get-AzureRmVM** から **Code** へのリンクを作成します。  
1. 構成ウィンドウで **Code** をクリックし、ラベルを **Get Status** に変更します。
1. **Code** パラメーターを選択すると、 **[コード エディター]** ページが表示されます。  
1. コード エディターに、次のコード スニペットを貼り付けます。

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. **Get Status** から **Start-AzureRmVM** へのリンクを作成します。<br> ![コード モジュールを含む Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. リンクを選択し、構成ウィンドウの **[Apply condition (条件の適用)]** を **[はい]** に変更します。 条件が満たされた場合にのみ対象のアクティビティが実行されることを示す破線にリンクが変わることに注意してください。  
1. **[条件式]** に「 *$ActivityOutput['Get Status'] -eq "Stopped"* 」と入力します。 **Start-AzureRmVM** は、仮想マシンが停止している場合にのみ実行されるようになります。
1. ライブラリ コントロールで **Cmdlets** 、**Microsoft.PowerShell.Utility** の順に展開します。
1. **Write-Output** をキャンバスに 2 回追加します。
1. 1 個目の **Write-Output** コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を *Notify VM Started* に変更します。
1. **InputObject** の **[データ ソース]** を **[PowerShell expression (PowerShell 式)]** に変更し、式に「 *$VMName successfully started.* 」と入力します。
1. 2 個目の **Write-Output** コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を *Notify VM Start Failed* に変更します
1. **InputObject** の **[データ ソース]** を **[PowerShell expression (PowerShell 式)]** に変更し、式に「 *$VMName could not start.* 」と入力します。
1. **Start-AzureRmVM** から **Notify VM Started** および **Notify VM Start Failed** へのリンクを作成します。
1. **Notify VM Started** へのリンクを選択し、 **[Apply condition (条件の適用)]** を **True** に変更します。
1. **[条件式]** に「 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*」と入力します。 これで、この Write-Output コントロールは仮想マシンが正常に開始された場合にのみ実行されるようになりました。
1. **Notify VM Start Failed** へのリンクを選択し、 **[Apply condition (条件の適用)]** を **True** に変更します。
1. **[条件式]** に「 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*」と入力します。 これで、この Write-Output コントロールは仮想マシンが正常に開始されなかった場合にのみ実行されるようになりました。 Runbook は次の図のようになります。 <br> ![Write-Output での Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Runbook を保存してテスト ウィンドウを開きます。
1. 仮想マシンが停止している状態で Runbook を開始すると、仮想マシンが開始します。

## <a name="next-steps"></a>次の手順

* グラフィカル作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* PowerShell Runbook の使用を開始するには、「 [初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。


