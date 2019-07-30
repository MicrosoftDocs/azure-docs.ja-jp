---
title: Azure Automation Runbook からメールを送信する
description: SendGrid を使用して Runbook 内からメールを送信する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235093"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>チュートリアル:Azure Automation Runbook からメールを送信する

PowerShell を使用して、[SendGrid](https://sendgrid.com/solutions) によって Runbook からメールを送信できます。 このチュートリアルでは、[Azure KeyVault](/azure/key-vault/) に格納されている API キーを使用してメールを送信する、再利用可能な Runbook を作成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Azure KeyVault を作成する
> * SendGrid API キーを KeyVault に格納する
> * API キーを取得してメールを送信する Runbook を作成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure サブスクリプション:まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [SendGrid アカウントを作成します](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)。
* Runbook を格納および実行するための [Automation アカウント](automation-offering-get-started.md)と **Az** モジュール、および [Run As Connection](automation-create-runas-account.md)。

## <a name="create-an-azure-keyvault"></a>Azure KeyVault を作成する

Azure KeyVault は、次の PowerShell スクリプトを使用して作成できます。 変数値を環境に固有の値に置き換えます。 コード ブロックの右上隅にある <kbd>[使ってみる]</kbd> ボタンを通じて、埋め込まれた Azure Cloud Shell を使用します。 ローカル コンピューターに [Azure PowerShell モジュール](/powershell/azure/install-az-ps)がインストールされている場合は、コードをローカルにコピーして実行することもできます。

> [!NOTE]
> API キーを取得するには、[SendGrid API キーの検索](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)に関するページに記載されている手順を使用します。

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Azure KeyVault を作成してシークレットを格納するその他の方法については、[KeyVault のクイックスタート](/azure/key-vault/)に関するページを参照してください。

## <a name="import-required-modules-to-your-automation-account"></a>必要なモジュールを Automation アカウントにインポートする

Runbook 内で Azure KeyVault を使用するには、Automation アカウントに以下のモジュールが必要です。

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)。
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)。

[インストール オプション] の下にある [Azure Automation] タブで <kbd>[Deploy to Azure Automation]\(Azure Automation にデプロイする\)</kbd> をクリックします。 このアクションにより Azure portal が開きます。 [インポート] ページで、Automation アカウントを選択し、<kbd>[OK]</kbd> をクリックします。

必要なモジュールを追加するその他の方法については、「[モジュールをインポートする](/azure/automation/shared-resources/modules#import-modules)」を参照してください。

## <a name="create-the-runbook-to-send-an-email"></a>Runbook を作成してメールを送信する

KeyVault を作成し、SendGrid API キーを格納したら、API キーを取得してメールを送信する Runbook を作成します。

この Runbook では、Azure KeyVault からシークレットを取得するために Azure で認証を行う際に、AzureRunAsConnection [実行アカウント](automation-create-runas-account.md)を使用します。

この例を使用して、**Send-GridMailMessage** と呼ばれる Runbook を作成します。 PowerShell スクリプトは変更して、さまざまなシナリオで再利用できます。

1. Azure Automation アカウントに移動します。
2. **[プロセス オートメーション]** の **[Runbook]** を選択します。
3. Runbook の一覧の上部で、 **[+ Runbook の作成]** を選択します。
4. **[Runbook の追加]** ページで、Runbook 名として「**Send-GridMailMessage**」と入力します。 Runbook の種類として、 **[PowerShell]** を選びます。 そのうえで **[Create]\(作成\)** を選択します。
   ![Runbook を作成する](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook が作成され、 **[PowerShell Runbook の編集]** ページが開きます。
   ![Runbook を編集する](./media/automation-send-email/automation-send-email-edit.png)
6. **[編集]** ページに、次の PowerShell の例をコピーします。 `$VaultName` が、KeyVault を作成したときに指定した名前であることを確認します。

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. **[発行]** を選択して Runbook を保存し、発行します。

Runbook が正常に実行されることを確認するには、「[Runbook をテストする](manage-runbooks.md#test-a-runbook)」または [Runbook の開始](start-runbooks.md)に関するページの手順に従います。
最初にテスト用のメールが表示されない場合は、**迷惑メール**および**スパム**のフォルダーを確認してください。

## <a name="clean-up"></a>クリーンアップする

不要になったら、Runbook を削除します。 削除するには、Runbook 一覧で Runbook を選択し、 **[削除]** をクリックします。

キー コンテナーを削除するには、[Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) コマンドレットを使用します。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>次の手順

* Runbook の作成または開始に関する問題については、「[Runbook に関するエラーのトラブルシューティング](./troubleshoot/runbooks.md)」を参照してください。
* Automation アカウント内のモジュールを更新するには、「[Azure Automation 内の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)]」を参照してください。
* Runbook の実行を監視するには、「[Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。
* アラートを使用して Runbook をトリガーするには、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」を参照してください。
