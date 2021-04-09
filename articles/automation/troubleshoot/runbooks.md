---
title: Azure Automation Runbook に関する問題のトラブルシューティング
description: この記事では、Azure Automation Runbook に関する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: 1ff5adf3ec974cc922d73cf5993a78722ca1b591
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723811"
---
# <a name="troubleshoot-runbook-issues"></a>Runbook の問題のトラブルシューティング

 この記事では、発生する可能性がある Runbook 問題と、その解決方法について説明します。 一般的な情報については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」を参照してください。

## <a name="diagnose-runbook-issues"></a>Runbook の問題を診断する

Azure Automation で Runbook を実行しているときにエラーが発生した場合は、次の手順を使用すると、問題の診断に役立ちます。

1. ローカル コンピューターで Runbook スクリプトが正常に実行されることを確認します。

    言語リファレンスと学習モジュールについては、[PowerShell のドキュメント](/powershell/scripting/overview)または [Python のドキュメント](https://docs.python.org/3/)を参照してください。スクリプトをローカルで実行すると、次のような一般的なエラーを検出して解決できます。

      * モジュールの不足
      * 構文エラー
      * 論理エラー

1. Runbook の[エラー ストリーム](../automation-runbook-output-and-messages.md#runbook-output)を調査します。

    これらのストリームで特定のメッセージを確認し、この記事に記載されているエラーと比較します。

1. 必要なモジュールがお使いのノードと Automation ワークスペースにあることを確認します。

    Runbook でモジュールをインポートする場合は、「[モジュールをインポートする](../shared-resources/modules.md#import-modules)」の手順を使用して、それらのモジュールがお使いの Automation アカウントで使用可能であることを確認します。 「[Azure Automation の Azure PowerShell モジュールを更新する](../automation-update-azure-modules.md)」の手順に従って、PowerShell モジュールを最新バージョンに更新します。 トラブルシューティングの詳細については、[モジュールのトラブルシューティング](shared-resources.md#modules)に関する記述を参照してください。

1. Runbook が中断されたか、予期せず失敗した場合:

    * [証明書を更新する](../manage-runas-account.md#cert-renewal) (実行アカウントの有効期限が切れている場合)。
    * [Webhook を更新する](../automation-webhooks.md#renew-a-webhook) (期限切れの Webhook を使用して Runbook を開始しようとしている場合)。
    * [ジョブの状態を確認](../automation-runbook-execution.md#job-statuses)して、現在の Runbook の状態と、この問題について考えられるいくつかの原因を特定する。
    * [新たな出力を Runbook に追加](../automation-runbook-output-and-messages.md#working-with-message-streams)して、Runbook の中断前に何が発生したかを特定する。
    * ジョブによってスローされる[例外を処理する](../automation-runbook-execution.md#exceptions)。

1. Runbook ジョブまたは Hybrid Runbook Worker 上の環境が応答しない場合に、この手順を実行します。

    Azure Automation ではなく、Hybrid Runbook Worker で Runbook を実行している場合は、[Hybrid Worker 自体のトラブルシューティング](hybrid-runbook-worker.md)が必要になることがあります。

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>シナリオ:Runbook が "No permission (アクセス許可なし)" または "Forbidden 403 (禁止 403)" エラーで失敗する

### <a name="issue"></a>問題

Runbook が、"No permission (アクセス許可なし)" または "Forbidden 403 (禁止 403)" エラー、あるいはそれと同等のエラーで失敗します。

### <a name="cause"></a>原因

実行アカウントの Azure リソースに対するアクセス許可が、現在使用している Automation アカウントと同じでない可能性があります。 

### <a name="resolution"></a>解像度

ご自身の実行アカウントに、お使いのスクリプトで使用されている[リソースにアクセスするためのアクセス許可がある](../../role-based-access-control/role-assignments-portal.md)ことを確認してください。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>シナリオ:Azure アカウントにサインインできない

### <a name="issue"></a>問題

`Connect-AzAccount` コマンドレットを使用すると、次のいずれかのエラーが表示されます。

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

これらのエラーは、資格情報資産名が有効でない場合に発生します。 Automation 資格情報資産の設定に使用したユーザー名とパスワードが有効でない場合にも発生する可能性があります。

### <a name="resolution"></a>解像度

問題を特定するには、次の手順に従います。

1. 特殊文字が使用されていないことを確認します。 これらの特殊文字には、Azure に接続するために使用している Automation 資格情報資産名の `\@` 文字が含まれます。
1. ローカルの PowerShell ISE エディターで、Azure Automation 資格情報に格納されているユーザー名とパスワードを使用できるかどうかを確認します。 PowerShell ISE で、次のコマンドレットを実行します。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. ローカルで認証に失敗した場合、Azure Active Directory (Azure AD) 資格情報が適切に設定されていません。 Azure AD アカウントを正しく設定するには、[Azure Active Directory を使用した Azure の認証](../automation-use-azure-ad.md)に関する記事を参照してください。

1. エラーが一時的なものであると思われる場合は、認証をより堅牢にするために認証ルーチンに再試行ロジックを追加してみてください。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>シナリオ:Login-AzureRMAccount を実行してログインする

### <a name="issue"></a>問題

Runbook を実行すると、次のエラーが表示されます。

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

このエラーは、実行アカウントを使用していないとき、または実行アカウントの有効期限が切れたときに発生することがあります。 詳細については、[Azure Automation - 実行アカウントの概要](../automation-security-overview.md#run-as-accounts)に関する記事を参照してください。

このエラーには、次の 2 つの主要な原因があります。

* さまざまなバージョンの AzureRM または Az モジュールがあります。
* 別のサブスクリプションのリソースにアクセスしようとしています。

### <a name="resolution"></a>解像度

1 つの AzureRM または Az モジュールを更新した後でこのエラーが発生する場合は、すべてのモジュールを同じバージョンに更新します。

別のサブスクリプションのリソースにアクセスしようとしている場合は、次の手順に従ってアクセス許可を構成します。

1. Automation 実行アカウントに移動し、**アプリケーション ID** と **サムプリント** をコピーします。

    ![アプリケーション ID とサムプリントをコピーする](../media/troubleshoot-runbooks/collect-app-id.png)

1. Automation アカウントがホスト "*されていない*" サブスクリプションの **[アクセス制御]** に移動し、新しいロールの割り当てを追加します。

    ![アクセス制御](../media/troubleshoot-runbooks/access-control.png)

1. 前に収集した **アプリケーション ID** を追加します。 **[共同作成者]** のアクセス許可を選択します。

    ![ロールの割り当ての追加](../media/troubleshoot-runbooks/add-role-assignment.png)

1. サブスクリプションの名前をコピーします。

1. 次の Runbook コードを使用して、Automation アカウントから他のサブスクリプションへのアクセス許可をテストできるようになります。 `<CertificateThumbprint>` を、手順 1. でコピーした値に置き換えます。 `"<SubscriptionName>"` を、手順 4. でコピーした値に置き換えます。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>シナリオ:Azure サブスクリプションが見つからない

### <a name="issue"></a>問題

`Select-AzureSubscription`、`Select-AzureRMSubscription`、または `Select-AzSubscription` コマンドレットを使用すると、次のエラーが表示されます。

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>エラー

このエラーは、次の場合に発生する可能性があります。

* サブスクリプション名が有効ではない。
* サブスクリプションの詳細を取得しようとしている Azure AD ユーザーが、サブスクリプションの管理者として構成されていない。
* そのコマンドレットが使用できない。

### <a name="resolution"></a>解像度

次の手順に従って、Azure に対して認証されているかと、選択しようとしているサブスクリプションにアクセスできるかを確認します。

1. スクリプトがスタンドアロンで動作することを確認するには、Azure Automation の外部でテストします。
1. スクリプトで、`Select-*` コマンドレットを実行する前に [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットが実行されていることを確認します。
1. Runbook の先頭に `Disable-AzContextAutosave –Scope Process` を追加します。 このコマンドレットにより、すべての資格情報が現在の Runbook の実行にのみ適用されるようになります。
1. それでもエラー メッセージが表示される場合は、`Connect-AzAccount` に `AzContext` パラメーターを追加するようにコードを変更してから、コードを実行します。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>シナリオ:複数のサブスクリプションを処理するときに Runbook が失敗する

### <a name="issue"></a>問題

Runbook を実行したときに、Runbook が Azure リソースを管理できません。

### <a name="cause"></a>原因

Runbook が、実行中に正しいコンテキストを使用していません。 Runbook が誤って間違ったサブスクリプションにアクセスしようとしたことが原因である可能性があります。

次のようなエラーが表示されることがあります。

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>解像度

1 つの Runbook で複数の Runbook を呼び出すと、サブスクリプション コンテキストが失われる可能性があります。 誤って間違ったサブスクリプションにアクセスしようとするのを避けるために、以下のガイダンスに従う必要があります。

* 間違ったサブスクリプションが参照されないようにするには、各 Runbook の開始時に次のコードを使用して、Automation Runbook でコンテキストの保存を無効にします。

   ```azurepowershell-interactive
   Disable-AzContextAutosave –Scope Process
   ```

* Azure PowerShell コマンドレットでは、`-DefaultProfile` パラメーターがサポートされています。 この機能は、すべての Az および AzureRm コマンドレットに追加され、同じプロセスで複数の PowerShell スクリプトを実行できるようになりました。コンテキストと、各コマンドレットに使用するサブスクリプションを指定できます。 Runbook を使用する場合、Runbook の作成時 (つまり、あるアカウントでサインインしたとき) と、変更のたびに、Runbook にコンテキスト オブジェクトを保存してください。また、Az コマンドレットを指定するとき、コンテキストを参照してください。

   > [!NOTE]
   > [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) や [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription) などのコマンドレットを使用してコンテキストを直接操作する場合でも、コンテキスト オブジェクトを渡す必要があります。

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>シナリオ:多要素認証が有効になっているために Azure に対する認証が失敗する

### <a name="issue"></a>問題

Azure のユーザー名とパスワードで Azure に対して認証するときに、次のエラーを受け取ります。

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。 代わりに、証明書またはサービス プリンシパルを使用して認証する必要があります。

### <a name="resolution"></a>解像度

クラシック実行アカウントを Azure クラシック デプロイ モデルのコマンドレットと共に使用するには、[クラシック実行アカウントを作成して Azure サービスを管理する](../automation-create-standalone-account.md#create-a-classic-run-as-account)方法に関する記事を参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する場合は、[Azure portal を使用するサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)および [Azure Resource Manager でのサービス プリンシパルの認証](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)に関するページを参照してください。

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>シナリオ:Runbook が "A task was canceled" (タスクが取り消されました) というエラー メッセージで失敗する

### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

以前のバージョンの Azure モジュールを使用すると、このエラーが発生する可能性があります。

### <a name="resolution"></a>解像度

このエラーは、Azure モジュールを最新バージョンに更新することで解決できます。

1. Automation アカウントで、 **[モジュール]** を選択し、 **[Azure モジュールの更新]** を選択します。
1. 更新には約 15 分かかります。 完了したら、失敗した Runbook を再実行します。

モジュールの更新の詳細については、「[Azure Automation で Azure モジュールを更新する](../automation-update-azure-modules.md)」を参照してください。

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>シナリオ:コマンドレット、関数、またはスクリプトの名前として用語が認識されない

### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

このエラーは、次の理由で発生する可能性があります。

* コマンドレットを含むモジュールが、Automation アカウントにインポートされていない。
* コマンドレットを含むモジュールはインポートされているが、最新ではない。

### <a name="resolution"></a>解像度

このエラーを解決するには、次のいずれかのタスクを実行します。

* Azure モジュールの場合は、Automation アカウントでモジュールを更新する方法について、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」を参照してください。
* Azure 以外のモジュールの場合は、モジュールが Automation アカウントにインポートされていることを確認してください。

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>シナリオ:Azure Automation 上の PnP PowerShell Runbook でコマンドレットが失敗する

### <a name="issue"></a>問題

PnP PowerShell で生成されたオブジェクトが Runbook から Azure Automation の出力に直接書き込まれると、コマンドレットの出力をオートメーションにストリーム バックすることができません。

### <a name="cause"></a>原因

この問題は、戻りオブジェクトをキャッチせずに、`add-pnplistitem` などの PnP PowerShell コマンドレットを呼び出す Runbook を Azure Automation で処理する場合に最もよく発生します。

### <a name="resolution"></a>解像度

スクリプトを編集して戻り値を変数に割り当て、コマンドレットでオブジェクト全部が標準出力に書き出されないようにします。 次に示すように、スクリプトを使って出力ストリームをコマンドレットにリダイレクトできます。

```azurecli
  $null = add-pnplistitem
```

スクリプトを使ってコマンドレットの出力を解析する場合は、単に出力をストリーミングするのではなく、スクリプトで出力を変数に格納し、変数を操作する必要があります。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>シナリオ:Runbook の実行時にコマンドレットが認識されない

### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。 コマンドレットを含むモジュールがアカウントにないか、Runbook 名に名前の競合があるか、あるいはコマンドレットが別のモジュールにも存在していて Automation で名前を解決できない可能性があります。

### <a name="resolution"></a>解像度

問題を解決するには、次のいずれかの解決策を使用します。

* コマンドレット名を正しく入力したことを確認します。
* 確実に、コマンドレットが Automation アカウントに存在し、競合がないようにします。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索するか、`Get-Command <CommandName>` を実行します。 コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、コマンドレットをキャンバスに追加します。 お使いの Runbook に設定されている有効なパラメーターを使用していることを確認してください。
* 名前が競合し、コマンドレットが 2 つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで、問題を解決します。 たとえば、`ModuleName\CmdletName` を使用できます。
* ハイブリッド worker グループでオンプレミスの Runbook を実行する場合は、ハイブリッド worker をホストするマシンにモジュールとコマンドレットが確実にインストールされているようにします。

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>シナリオ:Add-AzAccount の呼び出し時のオブジェクト参照が正しくない

### <a name="issue"></a>問題

`Connect-AzAccount` コマンドレットのエイリアスである `Add-AzAccount` を使用すると、次のエラーが表示されます。

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

このエラーは、`Add-AzAccount` を呼び出して Automation アカウントを追加する前に Runbook で適切な手順が実行されなかった場合に発生する可能性があります。 必要な手順の一例として、実行アカウントでのサインインが挙げられます。 Runbook で使用する正しい操作については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」をご覧ください。

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>シナリオ:オブジェクト参照がオブジェクト インスタンスに設定されていない

### <a name="issue"></a>問題

`Wait` パラメーターを使用して子 Runbook を呼び出したときに、出力ストリームにオブジェクトが含まれていると、次のエラーが表示されます。

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

ストリームにオブジェクトが含まれている場合、`Start-AzAutomationRunbook` で出力ストリームが正しく処理されません。

### <a name="resolution"></a>解像度

ポーリング ロジックを実装し、[Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) コマンドレットを使用して出力を取得します。 このロジックのサンプルは次のように定義されます。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>シナリオ:逆シリアル化されたオブジェクトであるため、Runbook が失敗する

### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>原因

Runbook が PowerShell ワークフローの場合、ワークフローが中断された場合に Runbook の状態を維持できるように、複雑なオブジェクトが逆シリアル化形式で保存されます。

### <a name="resolution"></a>解像度

この問題を解決するには、次のいずれかの解決策を使用します。

* コマンドレット間で複雑なオブジェクトをパイプ処理する場合、これらのコマンドレットを `InlineScript` アクティビティでラップします。
* オブジェクト全体を渡すのではなく、複雑なオブジェクトから、必要な名前または値を渡します。
* PowerShell ワークフロー Runbook ではなく PowerShell Runbook を使用します。

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>シナリオ:Webhook を呼び出すときに 400 Bad Request 状態が発生する

### <a name="issue"></a>問題

Azure Automation Runbook の Webhook を呼び出そうとすると、次のエラーが表示されます。

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

呼び出そうとしている Webhook が、無効または期限切れです。 

### <a name="resolution"></a>解像度

Webhook が無効な場合は、Azure portal から再度有効にすることができます。 Webhook の有効期限が切れている場合は、削除してから再作成する必要があります。 Webhook がまだ期限切れでない場合にのみ、[Webhook を更新](../automation-webhooks.md#renew-a-webhook)できます。 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>シナリオ:429:The request rate is currently too large (現在、要求率が大きすぎます)

### <a name="issue"></a>問題

`Get-AzAutomationJobOutput` コマンドレットを実行すると、次のエラー メッセージを受け取ります。

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

このエラーは、多数の[詳細ストリーム](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)がある Runbook からジョブ出力を取得するときに発生する可能性があります。

### <a name="resolution"></a>解像度

このエラーを解決するには、次のいずれかの操作を行います。

* Runbook を編集し、出力されるジョブ ストリームの数を減らします。
* コマンドレットを実行するときに取得されるストリーム数を減らします。 これを行うために、[Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) コマンドレットの `Stream` パラメーターに値を設定して、出力ストリームのみを取得できます。 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>シナリオ:割り当てられたクォータを超えたために Runbook ジョブが失敗する

### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

ジョブの実行がアカウントの 500 分の無料クォータを超えるとこのエラーが発生します。 このクォータは、すべての種類のジョブ実行タスクに適用されます。 これらのタスクには、ジョブのテスト、ポータルからのジョブの開始、Webhook を使用したジョブの実行、Azure portal またはデータセンターを使用して実行するジョブのスケジュール設定などがあります。 Automation の料金については、「[Automation の料金](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

### <a name="resolution"></a>解像度

毎月 500 分を超える処理を使用する場合は、サブスクリプションを Free レベルから Basic レベルに変更します。

1. Azure サブスクリプションにサインインします。
1. アップグレードする Automation アカウントを選択します。
1. **[設定]** を選択し、 **[価格]** を選択します。
1. ページの下部にある **[有効]** を選択して、アカウントを Basic レベルにアップグレードします。

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>シナリオ:1 MB を超える Runbook の出力ストリーム

### <a name="issue"></a>問題

Azure サンドボックス内で実行されている Runbook が次のエラーで失敗します。

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>原因

このエラーは、Runbook による例外データの出力ストリームへの書き込みが過剰に試行されると発生します。

### <a name="resolution"></a>解決方法

ジョブ出力ストリームでは 1 MB の制限があります。 Runbook で、`try` および `catch` ブロックを使用して、実行可能ファイルまたはサブプロセスへの呼び出しを確実に囲むようにします。 操作で例外がスローされる場合は、コードによってその例外から Automation 変数にメッセージを書き込みます。 この手法により、メッセージがジョブ出力ストリームに書き込まれなくなります。 Hybrid Runbook Worker ジョブを実行すると、出力ストリームが 1 MB に切り捨てられ、エラー メッセージは表示されません。

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>シナリオ:Runbook ジョブの開始を 3 回試行したが、開始できない

### <a name="issue"></a>問題

Runbook が次のエラーで失敗します。

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

このエラーは、次のいずれかの問題が原因で発生します。

* **メモリの制限。** 400 MB を超えるメモリを使用すると、ジョブが失敗する可能性があります。 サンドボックスに割り当てられるメモリの制限については、[Automation サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)に関するセクションを参照してください。 

* **ネットワーク ソケット。** Azure サンドボックスの同時ネットワーク ソケット数は 1,000 に制限されています。 詳細については、 [Automation サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)に関するページを参照してください。

* **モジュールに互換性がない。** モジュールの依存関係が正しくない可能性があります。 この場合、Runbook では通常、`Command not found` または `Cannot bind parameter` メッセージが返されます。

* **サンドボックスで Active Directory による認証が行われない。** Runbook で、Azure サンドボックス内で実行されている実行可能ファイルまたはサブプロセスを呼び出そうとしました。 Azure Active Directory 認証ライブラリ (ADAL) を使用して Azure AD で認証するように Runbook を構成することはサポートされていません。

### <a name="resolution"></a>解決方法

* **メモリの制限、ネットワーク ソケット。** メモリの制限内で問題を解決するために推奨される方法としては、複数の Runbook 間でワークロードを分割する、メモリ内のデータの処理量を減らす、Runbook からの不要な出力を書き込まない、PowerShell Workflow Runbook に書き込むチェックポイントの数を検討する、などがあります。 `$myVar.clear` などの clear メソッドを使用して変数をクリアし、`[GC]::Collect` を使用してガベージ コレクションをすぐに実行します。 これにより、実行時の Runbook のメモリ専有領域が小さくなります。

* **モジュールに互換性がない。** 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」の手順に従って Azure モジュールを更新します。

* **サンドボックスで Active Directory による認証が行われない。** Runbook で Azure AD に対する認証を行う場合は、お使いの Automation アカウントで Azure AD モジュールが確実に使用できるようにします。 Runbook によって自動化されるタスクを実行するために必要なアクセス許可を、必ず実行アカウントに付与してください。

  Azure サンドボックスで実行されている実行可能ファイルまたはサブプロセスを Runbook で呼び出すことができない場合は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を使用します。 ハイブリッド worker には、Azure サンドボックスに存在するようなメモリとネットワークの制限はありません。

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>シナリオ:PowerShell ジョブが "Cannot invoke method" (メソッドを呼び出せません) というエラー メッセージで失敗する

### <a name="issue"></a>問題

Azure で実行されている Runbook で PowerShell ジョブを開始すると、次のエラー メッセージが表示されます。

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

このエラーは、Azure サンドボックスで実行されている Runbook を、[完全言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)で実行できないことを示している可能性があります。

### <a name="resolution"></a>解決方法

このエラーを解決するには、次の 2 つの方法があります。

* [Start-Job](/powershell/module/microsoft.powershell.core/start-job) を使用する代わりに [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) を使用して、Runbook を開始します。
* Hybrid Runbook Worker で Runbook を実行してみます。

この動作や、Azure Automation Runbook の他の動作の詳細については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」をご覧ください。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>シナリオ:実行時間の長い Runbook が完了しない

### <a name="issue"></a>問題

Runbook が 3 時間の実行の後、停止状態になります。 このエラーが表示されることもあります。

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

これは、Azure Automation 内のプロセスの[フェア シェア](../automation-runbook-execution.md#fair-share)監視のための、Azure サンドボックスでの設計による動作です。 プロセスの実行時間が 3 時間を超えた場合、フェア シェアによって自動的に Runbook が停止されます。 フェア シェアの時間の制限を超える Runbook の状態は、Runbook の種類によって異なります。 PowerShell および Python Runbook は、停止状態に設定されます。 PowerShell Workflow Runbook は、失敗に設定されます。

### <a name="cause"></a>原因

Runbook が、Azure サンドボックスのフェア シェアによって許可されている 3 時間の制限を超えて実行されました。

### <a name="resolution"></a>解決方法

推奨される解決策の 1 つは、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。 ハイブリッド worker には、Azure サンドボックスに存在するフェア シェアによる 3 時間の Runbook 制限は適用されません。 予期しないローカル インフラストラクチャの問題が発生した場合の再起動動作をサポートするには、Hybrid Runbook Worker 上で実行される Runbook を開発する必要があります。

もう 1 つの解決策は、[子 Runbook](../automation-child-runbooks.md) を作成して Runbook を最適化することです。 Runbook で、複数のリソースに対して同じ関数をループ処理する場合 (複数のデータベースに対するデータベース処理などで)、関数を子 Runbook に移動することができます。 各子 Runbook は、別のプロセスで並列に実行されます。 この動作によって、親 Runbook の完了までにかかる合計時間は減ります。

子 Runbook のシナリオを実現する PowerShell コマンドレットは次のとおりです。

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)。 このコマンドレットを使用すると、Runbook を起動し、パラメーターを Runbook に渡すことができます。
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob)。 子 Runbook の完了後に実行する必要がある操作がある場合は、このコマンドレットを使用して、各子のジョブの状態を確認できます。

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>シナリオ:ジョブ ストリームで get_SerializationSettings メソッドに関するエラーが発生する

### <a name="issue"></a>問題

Runbook のジョブ ストリームで、次のエラーが表示されます。

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>原因

このエラーは、Runbook 内で AzureRM モジュールから Az モジュールへの不完全な移行を使用した場合に発生する可能性があります。 この状況が原因で、Azure Automation で Runbook ジョブが AzureRM モジュールのみを使用して開始された後、Az モジュールのみを使用して別のジョブが開始され、サンドボックスのクラッシュにつながる可能性があります。

### <a name="resolution"></a>解決方法

Az と AzureRM のコマンドレットを同じ Runbook で使用することはお勧めできません。 これらのモジュールの正しい使用方法について詳しくは、「[Az モジュールへの移行](../shared-resources/modules.md#migrate-to-az-modules)」をご覧ください。

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>シナリオ:Runbook またはアプリケーションに Azure サンドボックスを使用しているときにアクセスが拒否される

### <a name="issue"></a>問題

Runbook またはアプリケーションを Azure サンドボックスで実行しようとすると、環境によってアクセスが拒否されます。

### <a name="cause"></a>原因

この問題は、Azure サンドボックスですべてのアウトプロセス COM サーバーへのアクセスが阻止されることが原因で発生することがあります。 たとえば、サンドボックス アプリケーションまたは Runbook では、Windows Management Instrumentation (WMI) や Windows インストーラー サービス (msiserver.exe) を呼び出すことはできません。

### <a name="resolution"></a>解決方法

Azure サンドボックスの使用について詳しくは、「[Runbook の実行環境](../automation-runbook-execution.md#runbook-execution-environment)」をご覧ください。

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>シナリオ:Runbook 内で Key Vault を使用すると、無効な "Forbidden (禁止)" 状態コードが表示される

### <a name="issue"></a>問題

Azure Automation Runbook を介して Azure Key Vault にアクセスしようとすると、次のエラーが表示されます。

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>原因

この問題の原因としては、次のようなことが考えられます。

* 実行アカウントを使用していない。
* 権限が不足しています。

### <a name="resolution"></a>解像度

#### <a name="not-using-a-run-as-account"></a>実行アカウントを使用していない

「[手順 5 - Azure リソースを管理するための認証を追加する](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources)」に従って、Key Vault へのアクセスに実行アカウントを使用するようにします。

#### <a name="insufficient-permissions"></a>アクセス許可が不十分である

[Key Vault へのアクセス許可を追加](../manage-runas-account.md#add-permissions-to-key-vault)して、Key Vault にアクセスするための十分なアクセス許可が実行アカウントに付与されるようにします。

## <a name="recommended-documents"></a>推奨されるドキュメント

* [Azure Automation での Runbook の実行](../automation-runbook-execution.md)
* [Azure Automation での Runbook を開始する](../start-runbooks.md)

## <a name="next-steps"></a>次のステップ

お客様の問題がここになかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 回答、サポート、エキスパートについて、Azure サポートがお客様を Azure コミュニティにご案内します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** を選択します。
