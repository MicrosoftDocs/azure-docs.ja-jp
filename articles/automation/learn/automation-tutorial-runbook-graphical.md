---
title: Azure Automation でグラフィカル Runbook を作成する
description: この記事では、簡単なグラフィカル Runbook を Azure Automation で作成、テスト、発行する方法を説明します。
keywords: Runbook, Runbook テンプレート, Runbook Automation, Azure Runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899803"
---
# <a name="tutorial-create-a-graphical-runbook"></a>チュートリアル:グラフィカル Runbook を作成する

このチュートリアルでは、Azure Automation で [グラフィカルな Runbook](../automation-runbook-types.md#graphical-runbooks) を作成する手順を説明します。 Azure portal のグラフィカル エディターを使用して、グラフィカルおよびグラフィカル PowerShell ワークフロー Runbook を作成および編集できます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 簡単なグラフィカル Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Runbook パラメーターと条件付きリンクで Azure 仮想マシンを起動するように Runbook を更新する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free)にサインアップしてください。
* [Automation アカウント](../index.yml)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。
* 必要に応じて、使用するコマンドレットに基づいて、[Azure モジュールをインストール](../shared-resources/modules.md)するか、[モジュールを更新](../automation-update-azure-modules.md)します。

## <a name="step-1---create-runbook"></a>手順 1 - Runbook を作成する

最初に、`Hello World` というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

    Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 また、サブスクリプションに関連付けられている資格情報資産も持っている必要があります。

2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。

3. **[Runbook の作成]** を選択して、新しい Runbook を作成します。

4. Runbook の名前を **MyFirstRunbook-Graphical** に設定します。

5. ここでは、[グラフィカルな Runbook](../automation-graphical-authoring-intro.md) を作成します。 **[Runbook の種類]** での **[グラフィック]** を選択します。

    ![新しい Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. **[作成]** をクリックして Runbook を作成し、グラフィカル エディターを開きます。

## <a name="step-2---add-activities"></a>手順 2 - アクティビティを追加する

エディターの左側にあるライブラリ コントロールでは、Runbook に追加するアクティビティを選択できます。 Runbook のテキストを出力する `Write-Output` コマンドレットを追加します。

1. ライブラリ コントロールの検索フィールドをクリックして、「`write-output`」と入力します。 検索結果は、次の図のようになります。

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. 一覧の一番下までスクロールします。 **[Write-Output]** を右クリックし、 **[キャンバスに追加]** を選択します。 または、コマンドレット名の横にある省略記号 [...] をクリックし、 **[キャンバスに追加]** を選択してもかまいません。

3. キャンバスで **[Write-Output]** アクティビティをクリックします。 この操作によって [構成コントロール] ページが開き、アクティビティを構成できます。

4. **[ラベル]** フィールドの既定値はコマンドレットの名前ですが、もっとわかりやすい名前に変更できます。 それを `Write Hello World to output` に変更します。

5. **[パラメーター]** をクリックし、コマンドレットのパラメーターの値を指定します。

   一部のコマンドレットには複数のパラメーター セットがあり、どれを使用するかを選択する必要があります。 この場合、`Write-Output` のパラメーター セットは 1 つだけです。

6. `InputObject` パラメーターを選択します。 このパラメーターを使用して、出力ストリームに送信するテキストを指定します。

7. **[データ ソース]** ドロップダウン メニューには、パラメーター値の設定に使用できるソースが含まれます。 このメニューで、 **[PowerShell 式]** を選択します。

   別のアクティビティ、Automation の資産、PowerShell 式などのソースからの出力を使用できます。 この場合、出力は単に `Hello World` です。 PowerShell 式を使用して文字列を指定することができます。

8. **[式]** フィールドに「`"Hello World"`」と入力し、 **[OK]** を 2 回クリックしてキャンバスに戻ります。

9. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト ウィンドウ]** を選択して、[テスト] ペインを開きます。

2. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。

3. [Runbook ジョブ](../automation-runbook-execution.md)が作成され、その状態がペインに表示されることに注意してください。

   ジョブの最初の状態は `Queued` であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示しています。 worker がジョブを要求すると、状態は `Starting` に変わります。 最後に、Runbook の実行が実際に開始されると、状態は `Running` になります。

4. Runbook ジョブが完了すると、[テスト] ウィンドウにその出力が表示されます。 ここでは、`Hello World` が表示されます。

    ![Hello World Runbook の出力](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行する前に、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** を選択して Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

2. [Runbook] ページで左にスクロールして Runbook を表示し、 **[編集状態]** の値が **[発行済み]** に設定されていることを確認します。

3. 右へスクロールして戻り、**MyFirstRunbook-Graphical** ページを表示します。

   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](../automation-webhooks.md) の作成を行うことができます。

4. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。

5. 作成した Runbook ジョブのジョブ ペインが開きます。 **[ジョブの状態]** フィールドに **[完了]** と表示されていることを確認します。

6. **[出力]** をクリックして [出力] ページを開くと、`Hello World` が表示されます。

7. [出力] ページを閉じます。

8. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには `Hello World` のみが表示されます。

    Runbook で詳細ストリームやエラー ストリームに書き込まれている場合は、[ストリーム] ペインで Runbook ジョブの他のストリームも表示できることに注意してください。

9. [ストリーム] ペインと [ジョブ] ペインを閉じて、MyFirstRunbook-Graphical ページに戻ります。

10. Runbook のすべてのジョブを表示するには、 **[リソース]** の **[ジョブ]** を選択します。 [ジョブ] ページには、自分の Runbook で作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

11. ジョブ名をクリックすると、Runbook を開始したときに表示されたのと同じ [ジョブ] ペインが開きます。 Runbook に対して作成されたジョブの詳細を表示するには、このペインを使用します。

## <a name="step-5---create-variable-assets"></a>手順 5 - 変数アセットを作成する

Runbook をテストして発行しましたが、これまでのところ Azure リソースの管理に役立つことは何もしていません。 認証を行うように Runbook を構成する前に、サブスクリプション ID を保持する変数を作成し、認証を行うアクティビティを設定して、この変数を参照する必要があります。 サブスクリプション コンテキストへの参照を含めると、複数のサブスクリプションを簡単に使用できます。

1. ナビゲーション ペインの **[サブスクリプション]** オプションからサブスクリプション ID をコピーします。

2. [Automation アカウント] ページで、 **[共有リソース]** の **[変数]** を選択します。

3. **[変数の追加]** を選択します。

4. [新しい変数] ページで、表示されるフィールドを次のように設定します。

    * **[名前]** -- 「`AzureSubscriptionId`」と入力します。
    * **[値]** -- サブスクリプション ID を入力します。
    * **[種類]** -- 文字列を選択したままにします。
    * **[暗号化]** -- 既定値を使用します。

5. **[作成]** をクリックして変数を作成します。

## <a name="step-6---add-authentication"></a>手順 6 - 認証を追加する

サブスクリプション ID を保持する変数を作成したので、サブスクリプションの実行資格情報を使用して認証を行うように Runbook を構成できます。 これを行うには、Azure 実行接続を資産として追加します。 また、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットと [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) コマンドレットをキャンバスに追加する必要もあります。

>[!NOTE]
>PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらの別名は、グラフィカルな Runbook では使用できないことに注意してください。 グラフィカル Runbook で使用できるのは `Connect-AzAccount` 自体のみです。

1. Runbook に戻り、MyFirstRunbook-Graphical ページの **[編集]** を選択します。

2. `Write Hello World to output` エントリは不要になりました。 省略記号をクリックして、 **[削除]** を選択します。

3. ライブラリ コントロールで、 **[資産]** 、 **[接続]** の順に展開します。 **[キャンバスに追加]** を選択して、キャンバスに `AzureRunAsConnection` を追加します。

4. `AzureRunAsConnection` の名前を `Get Run As Connection` に変更します。

5. ライブラリ コントロールの検索フィールドに「`Connect-AzAccount`」と入力します。

6. キャンバスに `Connect-AzAccount` を追加します。

7. `Get Run As Connection` にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を `Connect-AzAccount` までドラッグしてリンクを作成します。 Runbook は `Get Run As Connection` で開始され、次に `Connect-AzAccount` が実行されます。

    ![アクティビティ間のリンクの作成](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. キャンバスで `Connect-AzAccount` を選択します。 [構成制御] ペインで、 **[ラベル]** フィールドに「**Login to Azure**」と入力します。

9. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。

10. `Connect-AzAccount` コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** をクリックしてから、**ServicePrincipalCertificateWithSubscriptionId** を選択します。

11. このパラメーター セットのパラメーターが、[アクティビティ パラメーター構成] ページに表示されます。 **[APPLICATIONID]** をクリックします。

    ![Azure アカウントのパラメーターを追加する](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. [パラメーター値] ページで、次のように設定して、 **[OK]** をクリックします。

   * **[データ ソース]** -- **[アクティビティの出力]** を選択します。
   * データ ソース リスト -- **Get Automation Connection** を選択します。
   * **[フィールド パス]** -- 「`ApplicationId`」と入力します。 フィールド パスにプロパティ名を指定するのは、アクティビティによって複数のプロパティを持つオブジェクトが出力されるためです。

13. **CERTIFICATETHUMBPRINT** をクリックし、[パラメーター値] ページで次のように設定して、 **[OK]** をクリックします。

    * **[データ ソース]** -- **[アクティビティの出力]** を選択します。
    * データ ソース リスト -- **Get Automation Connection** を選択します。
    * **[フィールド パス]** -- 「`CertificateThumbprint`」と入力します。

14. **[SERVICEPRINCIPAL]** をクリックし、[パラメーター値] ページで **[データ ソース]** フィールドの **[ConstantValue]** を選択します。 **[True]** オプションをクリックし、 **[OK]** をクリックします。

15. **TENANTID** をクリックし、[パラメーター値] ページで次のように設定します。 終わったら、 **[OK]** を 2 回クリックします。

    * **[データ ソース]** -- **[アクティビティの出力]** を選択します。
    * データ ソース リスト -- **Get Automation Connection** を選択します。
    * **[フィールド パス]** -- 「`TenantId`」と入力します。

16. ライブラリ コントロールの検索フィールドに「`Set-AzContext`」と入力します。

17. キャンバスに `Set-AzContext` を追加します。

18. キャンバスで `Set-AzContext` を選択します。 [構成制御] ペインで、 **[ラベル]** フィールドに「`Specify Subscription Id`」と入力します。

19. **[パラメーター]** をクリックすると、[アクティビティ パラメーターの構成] ページが表示されます。

20. `Set-AzContext` コマンドレットには複数のパラメーター セットがあり、パラメーターの値を指定する前に、パラメーター セットを 1 つ選択する必要があります。 **[パラメーター セット]** をクリックしてから **SubscriptionId** を選択します。

21. このパラメーター セットのパラメーターが、[アクティビティ パラメーター構成] ページに表示されます。 **SubscriptionID** をクリックします。

22. [パラメーター値] ページで、 **[データ ソース]** に **[変数資産]** を選択し、ソース リストから **AzureSubscriptionId** を選択します。 終わったら、 **[OK]** を 2 回クリックします。

23. `Login to Azure` にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を `Specify Subscription Id` までドラッグします。 この時点で、Runbook は次のようになります。

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="矢印を [Specify Subscription ID] にドラッグした後の Runbook のスクリーンショット。":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>手順 7 - 仮想マシンを開始するアクティビティを追加する

ここで、仮想マシンを起動する `Start-AzVM` アクティビティを追加する必要があります。 Azure サブスクリプション内の任意の仮想マシンを選択できます。ここでは、その名前を [Start-AzVM](/powershell/module/az.compute/start-azvm) コマンドレットにハードコーディングします。

1. ライブラリ コントロールの検索フィールドに「`Start-Az`」と入力します。

2. キャンバスに `Start-AzVM` を追加してから、それをクリックして `Specify Subscription Id` の下にドラッグします。

3. `Specify Subscription Id` にポインターを合わせて、図形の下部に円を表示します。 円をクリックし、矢印を `Start-AzVM` までドラッグします。

4. [`Start-AzVM`] を選択します。 **[パラメーター]** 、 **[パラメーター セット]** の順にクリックし、アクティビティに対するセットを表示します。

5. パラメーター セットには、**ResourceGroupNameParameterSetName** を選択します。 **[ResourceGroupName]** と **[Name]** フィールドの横に付いている感嘆符は、それらが必須のパラメーターであることを示します。 どちらのフィールドも文字列値が想定されていることに注意してください。

6. 一覧から **Name** してください。 **[データ ソース]** フィールドに対して **[PowerShell 式]** を選択します。 この Runbook を開始するために使用する VM として、二重引用符で囲んだコンピューター名を入力します。 **[OK]** をクリックします。

7. 一覧から **ResourceGroupName** してください。 **[データ ソース]** フィールドの値として **[PowerShell 式]** を使用し、リソース グループの名前を二重引用符で囲んで入力します。 **[OK]** をクリックします。

8. **テスト ウィンドウ** をクリックして、Runbook をテストできるようにします。

9. **[開始]** をクリックしてテストを開始します。 終わったら、VM が開始されたことを確認します。 この時点で、Runbook は次のようになります。

    ![Runbook Start-AzVM の出力](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>手順 8 - 追加の入力パラメーターを追加する

Runbook ではこの時点で、`Start-AzVM` コマンドレットに対して指定したリソース グループ内の VM が起動されます。 Runbook を開始するときに名前とリソース グループの両方を指定できれば、Runbook はいっそう便利になります。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. MyFirstRunbook-Graphical ページで **[編集]** をクリックして、グラフィカル エディターを開きます。

2. **[入力と出力]** 、 **[入力の追加]** の順に選択して、Runbook 入力パラメーター ウィンドウを開きます。

3. 表示されるフィールドを次のように設定して、 **[OK]** をクリックします。
   * **[名前]** -- 「`VMName`」と指定します。
   * **[種類]** -- 文字列の設定のままにします。
   * **[必須]** -- 値を **[はい]** に変更します。

4. `ResourceGroupName` という名前の 2 番目の必須入力パラメーターを作成し、 **[OK]** をクリックして [入力と出力] ペインを閉じます。

    ![Runbook の入力パラメーター](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. `Start-AzVM` アクティビティを選択し、 **[パラメーター]** をクリックします。

6. **Name** の **[データ ソース]** フィールドを **[Runbook の入力]** に変更します。 次に、**VMName** を選択します。

7. **ResourceGroupName** の **[データ ソース]** フィールドを **[Runbook の入力]** に変更し、**ResourceGroupName** を選択します。

    ![Start-AzVM のパラメーター](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Runbook を保存してテスト ウィンドウを開きます。 テストで使用する 2 つの入力変数の値を指定できるようになりました。

9. テスト ウィンドウを閉じます。

10. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。

11. 前に起動した VM を停止します。

12. **[開始]** をクリックして Runbook を開始します。 起動しようとしている VM の `VMName` と `ResourceGroupName` の値を入力します。

13. Runbook が完了したら、VM が起動されていることを確認します。

## <a name="step-9---create-a-conditional-link"></a>手順 9 - 条件付きリンクを作成する

次に、まだ起動されていない場合にのみ VM の起動を試みるように Runbook を変更できます。 これを行うには、VM のインスタンス レベルの状態を取得する [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) コマンドレットを追加します。 次に、`Get Status` という名前の PowerShell ワークフロー コード モジュールを追加できます。これには、VM の状態が実行中か停止しているかを確認するための PowerShell コード スニペットが含まれています。 `Get Status` モジュールの条件付きリンクでは、現在の実行状態が停止の場合にのみ、`Start-AzVM` が実行されます。 この手順の最後では、Runbook で`Write-Output` コマンドレットを使用して、VM が正常に起動されたかどうかを通知するメッセージを出力します。

1. グラフィカル エディターで **MyFirstRunbook-Graphical** を開きます。

2. `Specify Subscription Id` と `Start-AzVM` の間のリンクを削除するには、リンクをクリックしてから **[削除]** を押します。

3. ライブラリ コントロールの検索フィールドに「`Get-Az`」と入力します。

4. キャンバスに `Get-AzVM` を追加します。

5. `Get-AzVM`、 **[パラメーター セット]** の順に選択して、コマンドレットのセットを表示します。

6. **GetVirtualMachineInResourceGroupNameParamSet** パラメーター セットを選択します。 **[ResourceGroupName]** と **[Name]** フィールドの横に付いている感嘆符は、それらが必須のパラメーターであることを示しています。 どちらのフィールドも文字列値が想定されていることに注意してください。

7. **Name** の **[データ ソース]** で **[Runbook の入力]** を選択し、**VMName** を選択します。 **[OK]** をクリックします。

8. **ResourceGroupName** の **[データ ソース]** で、 **[Runbook の入力]** を選択し、**ResourceGroupName** を選択します。 **[OK]** をクリックします。

9. **Status** の **[データ ソース]** で、 **[定数値]** を選択し、 **[True]** を選択します。 **[OK]** をクリックします。

10. `Specify Subscription Id` から `Get-AzVM` へのリンクを作成します。

11. ライブラリ コントロールで **[Runbook の制御]** を展開し、**Code** をキャンバスに追加します。  

12. `Get-AzVM` から `Code` へのリンクを作成します。  

13. `Code` をクリックし、構成ペインでラベルを **Get Status** に変更します。

14. `Code` を選択すると、[コード エディター] ページが表示されます。  

15. 次のコード スニペットをエディター ページに貼り付けます。

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. `Get Status` から `Start-AzVM` へのリンクを作成します。

    ![コード モジュールを含む Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. リンクを選択し、構成ペインで **[条件の適用]** を **[はい]** に変更します。 リンクが、条件が true の場合にのみ対象のアクティビティが実行されることを示す破線になることに注意してください。  

18. **[条件式]** に「`$ActivityOutput['Get Status'] -eq "Stopped"`」と入力します。 `Start-AzVM` は、VM が停止している場合にのみ実行されるようになりました。

19. ライブラリ コントロールで **Cmdlets** 、**Microsoft.PowerShell.Utility** の順に展開します。

20. キャンバスに `Write-Output` を 2 回追加します。

21. 1 つ目の `Write-Output` コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を **Notify VM Started** に変更します。

22. **[InputObject]** の **[データ ソース]** を **[PowerShell 式]** に変更し、式に「`$VMName successfully started.`」と入力します。

23. 2 つ目の `Write-Output` コントロールで、 **[パラメーター]** をクリックし、 **[ラベル]** の値を **Notify VM Start Failed** に変更します。

24. **[InputObject]** の **[データ ソース]** を **[PowerShell 式]** に変更し、式に「`$VMName could not start`」と入力します。

25. `Start-AzVM` から `Notify VM Started` および `Notify VM Start Failed` へのリンクを作成します。

26. `Notify VM Started` へのリンクを選択し、 **[条件の適用]** を true に変更します。

27. **[条件式]** に「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`」と入力します。 これで、この `Write-Output` コントロールは、VM が正常に起動された場合にのみ実行されるようになりました。

28. `Notify VM Start Failed` へのリンクを選択し、 **[条件の適用]** を true に変更します。

29. **[条件式]** フィールドに「`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`」と入力します。 これで、この `Write-Output` コントロールは、VM が正常に起動されなかった場合にのみ実行されるようになりました。 Runbook は次の図のようになります。

    ![Write-Output がある Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Runbook を保存してテスト ウィンドウを開きます。

31. VM が停止している状態で Runbook を開始すると、マシンが起動します。

## <a name="next-steps"></a>次のステップ

* グラフィック作成の詳細については、[Azure Automation でのグラフィカル Runbook の作成](../automation-graphical-authoring-intro.md)に関する記事を参照してください。
* PowerShell Runbook の使用を開始するには、「[PowerShell Runbook を作成する](automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[PowerShell Workflow Runbook を作成する](automation-tutorial-runbook-textual.md)」を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
