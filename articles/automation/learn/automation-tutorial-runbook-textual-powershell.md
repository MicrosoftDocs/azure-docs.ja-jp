---
title: Azure Automation で PowerShell Runbook を作成する
description: この記事では、シンプルな PowerShell Runbook を作成、テスト、発行する方法を説明します。
keywords: Azure PowerShell, PowerShell スクリプトのチュートリアル, PowerShell Automation
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896615"
---
# <a name="tutorial-create-a-powershell-runbook"></a>チュートリアル:PowerShell Runbook を作成する

このチュートリアルでは、Azure Automation で [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) を作成する手順について説明します。 PowerShell Runbook は、Windows PowerShell に基づきます。 Azure portal のテキスト エディターを使用して、Runbook のコードを直接編集することができます。

> [!div class="checklist"]
> * 簡単な PowerShell Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Runbook パラメーターで Azure 仮想マシンを起動するように Runbook を更新する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](../automation-quickstart-create-account.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。
* 必要に応じて、使用するコマンドレットに基づいて、[Azure モジュールをインストール](../shared-resources/modules.md)するか、[モジュールを更新](../automation-update-azure-modules.md)します。

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell ワークフロー Runbook との違い

PowerShell Runbook のライフサイクル、機能、管理は、PowerShell ワークフロー Runbook と同じです。 ただし、相違点や制限事項がいくつかあります。

| 特徴  | PowerShell Runbook | PowerShell ワークフロー Runbook |
| ------ | ----- | ----- |
| 速度 | コンパイル手順を使用しないため、高速で実行されます。 | もっとゆっくり実行されます。 |
| チェックポイント | チェックポイントはサポートされていません。 PowerShell Runbook では、最初から再開することしかできません。 | チェックポイントを使用してください。任意のポイントからブックの操作を再開できます。 |
| コマンド実行 | 直列実行のみをサポートします。 | 直列実行と並列実行の両方をサポートします。|
| Runspace | 1 つの実行空間で、スクリプト内のすべてが実行されます。 | 別個の実行空間をアクティビティ、コマンド、またはスクリプト ブロックに使用できます。 |

以上の違いに加え、PowerShell Runbook には、PowerShell ワークフロー Runbook と[構文上、異なる点](/previous-versions/technet-magazine/dn151046(v=msdn.10))がいくつかあります。

## <a name="step-1---create-runbook"></a>手順 1 - Runbook を作成する

最初に、`Hello World` というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。

3. **[Runbook の作成]** を選択して、新しい Runbook を作成します。

4. Runbook に **MyFirstRunbook-PowerShell** という名前を付けます。

5. ここでは、[PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks) を作成します。 **[Runbook の種類]** に **[PowerShell]** を選択します。

6. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## <a name="step-2---add-code-to-the-runbook"></a>手順 2 - コードを Runbook に追加する

Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。 このチュートリアルでは、Runbook にコードを直接入力します。

1. Runbook は現在、空です。 スクリプトの本文に「`Write-Output "Hello World"`」を入力します。

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト ウィンドウ]** をクリックして、テスト ウィンドウを開きます。

2. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。

3. [Runbook ジョブ](../automation-runbook-execution.md)が作成され、その状態がペインに表示されることに注意してください。

   ジョブの最初の状態は "キュー済み" であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示します。 worker がジョブを要求すると、状態は "開始中" になります。 最後に、Runbook が実際に実行を開始すると [実行中] になります。

4. Runbook ジョブが完了すると、[テスト] ウィンドウにその出力が表示されます。 この場合、`Hello World` が表示されます。

   ![テスト ウィンドウの出力](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行する前に、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

2. [Runbook] ページで左にスクロールして Runbook を表示し、 **[編集状態]** の値が **[発行済み]** に設定されていることを確認します。

3. 右へスクロールして戻り、**MyFirstRunbook-PowerShell** のページを表示します。
   
   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](../automation-webhooks.md) の作成を行うことができます。

4. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。 

5. 作成した Runbook ジョブのジョブ ペインが開きます。 このウィンドウは閉じてもかまいませんが、ジョブの進捗状況を確認できるように今は開いたままにします。 ジョブの状態が **[ジョブの概要]** に表示されます。Runbook のテストには説明されているとおりの状態があります。

   ![ジョブの概要](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Runbook の状態が [完了] と表示されたら、 **[出力]** をクリックして [出力] ページを開くと、`Hello World` が表示されます。

   ![ジョブの出力](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. [出力] ページを閉じます。

8. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには `Hello World` のみが表示されます。

    Runbook で詳細ストリームやエラー ストリームに書き込まれている場合は、[ストリーム] ペインで Runbook ジョブの他のストリームも表示できることに注意してください。

   ![すべてのログ](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. ストリーム ペインとジョブ ペインを閉じると、MyFirstRunbook-PowerShell ページに戻ります。

10. **[詳細]** の **[ジョブ]** をクリックして、この Runbook のジョブ ページを開きます。 このページには、お使いの Runbook によって作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

   ![ジョブ リスト](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. ジョブ名をクリックすると、Runbook を開始したときに表示されたのと同じ [ジョブ] ペインが開きます。 このウィンドウを使用して、Runbook に対して作成されたジョブの詳細を表示します。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>手順 5 - Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 これを行うには、Automation アカウントの作成時に自動的に作成された実行アカウントを使用して Runbook で認証できなければなりません。

下の例のように、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットで Run As 接続が行われます。 複数のサブスクリプションにわたってリソースを管理しようとしている場合は、`AzContext` パラメーターを [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) と共に使用する必要があります。

> [!NOTE]
> PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](../automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. MyFirstRunbook-PowerShell ページで **[編集]** をクリックして、テキスト エディターを開きます。

2. `Write-Output` 行は不要になります。 削除してかまいません。

3. Automation の実行アカウントを使用して認証を処理する次のコードを入力またはコピーして貼り付けます。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. **テスト ウィンドウ** をクリックして、Runbook をテストできるようにします。

5. **[開始]** をクリックしてテストを開始します。 完了すると、次のような出力が表示され、アカウントの基本情報が表示されます。 この出力は、実行アカウントが有効であることを確認します。

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>手順 6 - 仮想マシンを開始するコードを追加する

これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。 仮想マシンを起動するコマンドを追加してみましょう。 Azure サブスクリプション内の任意の仮想マシンを選択し、ここではその名前を Runbook にハードコーディングできます。

1. Runbook スクリプトに [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) コマンドレットを追加し、仮想マシンを起動します。 下の画像のように、名前が `VMName` でリソース グループ名が `ResourceGroupName` の仮想マシンがコマンドレットによって開始されます。

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Runbook を保存し、**テスト ウィンドウ** をクリックしてテストできるようにします。

3. **[開始]** をクリックしてテストを開始します。 終わったら、仮想マシンが開始されたことを確認します。

## <a name="step-7---add-an-input-parameter"></a>手順 7 - 入力パラメーターを追加する

この Runbook では現在、Runbook にハードコーディングされた仮想マシンを起動しています。 Runbook を開始するときに仮想マシンを指定できれば、Runbook はいっそう便利になります。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. テキスト エディターで、パラメーターの `VMName` と `ResourceGroupName` に変数を使用するように `Start-AzVM` コマンドレットを変更します。 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Runbook を保存してテスト ウィンドウを開きます。 テストで使用する 2 つの入力変数の値を指定できるようになりました。

3. テスト ウィンドウを閉じます。

4. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。

5. 前に開始した仮想マシンを停止します。

6. **[開始]** をクリックして Runbook を開始します。 

7. 起動する仮想マシンの **VMNAME** 値と **RESOURCEGROUPNAME** 値を入力し、 **[OK]** をクリックします。

    ![パラメーターを渡す](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Runbook が完了したら、仮想マシンが確実に起動されているようにします。

## <a name="next-steps"></a>次のステップ

* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](/powershell/scripting/overview)を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
* グラフィカル Runbook の使用を開始するには、「[グラフィカル Runbook を作成する](automation-tutorial-runbook-graphical.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[PowerShell Workflow Runbook を作成する](automation-tutorial-runbook-textual.md)」を参照してください。
* Runbook の種類と利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](../automation-runbook-types.md)」を参照してください。
* PowerShell スクリプトのサポート機能の詳細については、[Azure Automation のネイティブ PowerShell スクリプト サポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関するページを参照してください。
