---
title: Azure Automation Runbook からメールを送信する
description: この記事では、Runbook 内からメールを送信する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.openlocfilehash: 915a0d75622a98b33f647041f3c3b622cb5236b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99053722"
---
# <a name="send-an-email-from-a-runbook"></a>Runbook からメールを送信する

PowerShell を使用して、[SendGrid](https://sendgrid.com/solutions) によって Runbook からメールを送信できます。 

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [SendGrid アカウント](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)。
* [Automation アカウント](./index.yml)と **Az** モジュール。
* Runbook を保存および実行するための[実行アカウント](./automation-security-overview.md#run-as-accounts)。

## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

Azure Key Vault は、次の PowerShell スクリプトを使用して作成できます。 変数値を環境に固有の値に置き換えます。 コード ブロックの右上隅にある **[使ってみる]** ボタンを通じて、埋め込まれた Azure Cloud Shell を使用します。 ローカル コンピューターに [Az モジュール](/powershell/azure/install-az-ps)がインストールされている場合は、コードをローカルにコピーして実行することもできます。

> [!NOTE]
> API キーを取得するには、[SendGrid API キーの検索](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)に関するページに記載されている手順を使用します。

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Azure Key Vault を作成してシークレットを格納するその他の方法については、[Key Vault のクイックスタート](../key-vault/index.yml)を参照してください。

## <a name="import-required-modules-into-your-automation-account"></a>必要なモジュールを Automation アカウントにインポートする

Runbook 内で Azure Key Vault を使用するには、次のモジュールを Automation アカウントにインポートする必要があります。

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

手順については、「[Az モジュールのインポート](shared-resources/modules.md#import-az-modules)」を参照してください。

## <a name="create-the-runbook-to-send-an-email"></a>Runbook を作成してメールを送信する

キー コンテナーを作成し、`SendGrid` API キーを格納したら、API キーを取得してメールを送信する Runbook を作成します。 この Runbook は、[実行アカウント](./automation-security-overview.md#run-as-accounts)として `AzureRunAsConnection` を使用して Azure で認証を行い、Azure Key Vault からシークレットを取得します。 **Send-GridMailMessage** Runbook を呼び出します。 例として使用される PowerShell スクリプトを変更して、さまざまなシナリオで再利用できます。

1. Azure Automation アカウントに移動します。
2. **[プロセス オートメーション]** の **[Runbook]** を選択します。
3. Runbook の一覧の上部で、 **[+ Runbook の作成]** を選択します。
4. [Runbook の追加] ページで、Runbook 名として「**Send-GridMailMessage**」と入力します。 Runbook の種類として、 **[PowerShell]** を選びます。 そのうえで **[Create]\(作成\)** を選択します。
   ![Runbook を作成する](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook が作成され、[PowerShell Runbook の編集] ページが開きます。
   ![Runbook を編集する](./media/automation-send-email/automation-send-email-edit.png)
6. [編集] ページに、次の PowerShell の例をコピーします。 `VaultName` には、Key Vault に対して選択した名前が指定されていることを確認します。

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
    $VaultName = "<Enter your vault name>&quot;
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name &quot;SendGridAPIKey").SecretValue
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

最初にテスト用のメールが表示されない場合は、**迷惑メール** および **スパム** のフォルダーを確認してください。

## <a name="clean-up-resources-after-the-email-operation"></a>メール操作後にリソースをクリーンアップする

1. この Runbook が不要になったら、Runbook の一覧で選択し、 **[削除]** をクリックします。

2. キー コンテナーを削除するには、[Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) コマンドレットを使用します。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

* Runbook ジョブのデータを Log Analytics ワークスペースに送信するには、「[Azure Monitor ログに Azure Automation のジョブ データを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。
* 基本レベルのメトリックとログを監視するには、「[Azure Automation Runbook をトリガーするアラートを使用する](automation-create-alert-triggered-runbook.md)」を参照してください。
* Runbook の操作中に発生した問題を修正するには、「[Runbook の問題のトラブルシューティング](./troubleshoot/runbooks.md)」を参照してください。
