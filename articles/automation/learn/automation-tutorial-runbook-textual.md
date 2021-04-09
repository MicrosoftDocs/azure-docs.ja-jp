---
title: Azure Automation で PowerShell Workflow Runbook を作成する
description: この記事では、シンプルな PowerShell ワークフロー Runbook を作成、テスト、発行する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890817"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>チュートリアル:PowerShell Workflow Runbook を作成する

このチュートリアルでは、Azure Automation で [PowerShell Workflow Runbook](../automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 PowerShell ワークフロー Runbook は、Windows PowerShell ワークフローに基づくテキスト Runbook です。 Azure portal のテキスト エディターを使用して、Runbook のコードを作成したり編集したりすることができます。 

> [!div class="checklist"]
> * 簡単な PowerShell ワークフロー Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Runbook パラメーターで Azure 仮想マシンを起動するように Runbook を更新する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](../index.yml)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="step-1---create-new-runbook"></a>手順 1 - 新しい Runbook を作成する

最初に、`Hello World` というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

   Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 また、サブスクリプションに関連付けられている資格情報資産も持っている必要があります。
 
2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。

3. **[Runbook の作成]** を選択して、新しい Runbook を作成します。

4. Runbook に **MyFirstRunbook-Workflow** という名前を付けます。

5. ここでは、[PowerShell ワークフロー Runbook](../automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 **[Runbook の種類]** で、 **[PowerShell ワークフロー]** を選びます。

6. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## <a name="step-2---add-code-to-the-runbook"></a>手順 2 - コードを Runbook に追加する

Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。 このチュートリアルでは、Runbook に直接コードを入力します。

1. Runbook はこの時点で空です (必要な `Workflow` キーワード、Runbook の名前、ワークフロー全体を囲む中かっこ以外)。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. かっこの間に「`Write-Output "Hello World"`」と入力します。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト] ウィンドウ** を選択して、[テスト] ウィンドウを開きます。

2. **[開始]** をクリックしてテストを開始し、有効になっているオプションのみをテストします。

3. [Runbook ジョブ](../automation-runbook-execution.md)が作成され、その状態がペインに表示されることに注意してください。

   ジョブの最初の状態は "キュー済み" であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示します。 worker がジョブを要求すると、状態は "開始中" になります。 最後に、Runbook が実際に実行を開始すると [実行中] になります。

4. Runbook ジョブが完了すると、[テスト] ウィンドウにその出力が表示されます。 ここでは、`Hello World` が表示されます。

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

2. **Runbook** ページで左にスクロールして Runbook を表示し、 **[編集状態]** の値が **発行済み** に設定されていることを確認します。

3. 右にスクロールして戻り、 **MyFirstRunbook-Workflow** のウィンドウを表示します。

   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](../automation-webhooks.md) の作成を行うことができます。

4. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。

   ![Runbook の開始](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. 作成した Runbook ジョブのジョブ ウィンドウが開きます。 この場合、ジョブの進行状況を確認できるように、ウィンドウを開いたままにしておきます。

6. ジョブの状態は **[ジョブの概要]** に表示されることに注意してください。 この状態は、Runbook のテスト時に確認した状態と一致します。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="[ジョブの概要] セクションが強調表示されている Runbook ジョブ ペインのスクリーンショット。":::

7. Runbook の状態が [完了]になったら、 **[出力]** をクリックします。 [出力] ページが開き、`Hello World` メッセージが表示されます。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="[出力] ボタンが強調表示されている Runbook ジョブ ペインのスクリーンショット。":::

8. [出力] ページを閉じます。

9. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには `Hello World` のみが表示されます。 Runbook で詳細ストリームやエラー ストリームに書き込まれている場合は、[ストリーム] ペインで Runbook ジョブの他のストリームも表示できることに注意してください。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="[すべてのログ] ボタンが強調表示されている Runbook ジョブ ペインのスクリーンショット。":::

10. [ストリーム] ウィンドウと [ジョブ] ウィンドウを閉じ、MyFirstRunbook ページに戻ります。

11. **[リソース]** の下の **[ジョブ]** をクリックして、この Runbook の [ジョブ] ページを開きます。 このページには、お使いの Runbook によって作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

   ![ジョブ](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. ジョブ名をクリックすると、Runbook を開始したときに表示されたのと同じジョブ ウィンドウが開きます。 このウィンドウを使用して、Runbook に対して作成されたジョブの詳細を表示します。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>手順 5 - Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 サブスクリプションの資格情報を使用して認証を行わないかぎり、これを行うことはできません。 認証では、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットが使用されます。

>[!NOTE]
>PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](../automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

1. [MyFirstRunbook-Workflow] ページに移動し、 **[編集]** をクリックして、テキスト エディターを開きます。

2. `Write-Output` 行を削除します。

3. 中かっこ内の空白行にカーソルを置きます。

4. Automation の実行アカウントを使用して認証を処理する次のコードを入力またはコピーして貼り付けます。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. **テスト ウィンドウ** をクリックして、Runbook をテストできるようにします。

6. **[開始]** をクリックしてテストを開始します。 完了すると、次のような出力が表示され、アカウントの基本情報が表示されます。 このアクションにより、資格情報が有効であることを確認できます。

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>手順 6 - 仮想マシンを開始するコードを追加する

これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。 仮想マシンを起動するコマンドを追加してみましょう。 Azure サブスクリプション内の任意の VM を選択し、ここではその名前を Runbook にハードコーディングします。 複数のサブスクリプションにわたってリソースを管理しようとしている場合は、`AzContext` パラメーターを [Get-AzContext](/powershell/module/az.accounts/get-azcontext) コマンドレットと共に使用する必要があります。

1. 次に示すように、[Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) コマンドレットの呼び出しを入力することにより、起動する VM の名前とリソース グループ名を指定します。 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Runbook を保存し、**テスト ウィンドウ** をクリックしてテストできるようにします。

3. **[開始]** をクリックしてテストを開始します。 完了したら、VM が開始されたことを確認します。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>手順 7 - Runbook に入力パラメーターを追加する

Runbook では現在、Runbook にハードコードされている VM を起動しています。 Runbook の開始時に VM を指定できるとより便利です。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. `VMName` と `ResourceGroupName` パラメーターの変数を Runbook に追加し、次に示す変数を `Start-AzVM` コマンドレットと共に使用します。

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook を保存してテスト ウィンドウを開きます。 テストで使用される 2 つの入力変数の値を指定できるようになりました。

3. テスト ウィンドウを閉じます。

4. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。

5. 開始した VM を停止します。

6. **[開始]** をクリックして Runbook を開始します。 

7. 起動する VM の **VMNAME** と **RESOURCEGROUPNAME** の値を入力します。

   ![Runbook の開始](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Runbook が完了したら、VM が開始されていることを確認します。

## <a name="next-steps"></a>次のステップ

* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](/powershell/scripting/overview)に関するページを参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
* グラフィカル Runbook の使用を開始するには、「[グラフィカル Runbook を作成する](automation-tutorial-runbook-graphical.md)」を参照してください。
* PowerShell Runbook の使用を開始するには、「[PowerShell Runbook を作成する](automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](../automation-runbook-types.md)」を参照してください。
* PowerShell スクリプトのサポート機能の詳細については、[Azure Automation での PowerShell スクリプトのネイティブ サポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関する記事を参照してください。
