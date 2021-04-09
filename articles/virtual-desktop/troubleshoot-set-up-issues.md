---
title: Windows Virtual Desktop 環境のホスト プールの作成 - Azure
description: Windows Virtual Desktop 環境のセットアップ中にテナントとホスト プールの問題をトラブルシューティングおよび解決する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c31a32b32a685087c53198ec52af1188d0071cab
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652447"
---
# <a name="host-pool-creation"></a>ホスト プールの作成

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)を参照してください。

この記事では、Windows Virtual Desktop テナントと、関連するセッション ホスト プール インフラストラクチャの初期セットアップ中の問題について説明します。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise マルチセッションのイメージの入手

Windows 10 Enterprise マルチセッションのイメージを使用するには、Azure Marketplace にアクセスし、 **[開始する]**  >  **[Microsoft Windows 10]** 、[[Windows 10 Enterprise multi-session, Version 1809]\(Windows 10 Enterprise マルチセッション、バージョン 1809\)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) を選択します。

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Azure portal を使用してホスト プールを作成する場合の問題

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>エラー:サービスにアクセスすると、[無料アカウントの作成] が表示される

> [!div class="mx-imgBorder"]
> !["無料アカウントの作成" メッセージが表示された Azure portal を示す画像](media/create-new-account.png)

**原因**:Azure にサインインしたアカウントにアクティブなサブスクリプションがないか、アカウントにサブスクリプションを表示するアクセス許可がありません。

**解決策**:少なくとも共同作成者レベルのアクセス権を持つアカウントを使用して、セッション ホスト仮想マシン (VM) をデプロイするサブスクリプションにサインインします。

### <a name="error-exceeding-quota-limit"></a>エラー:"クォータ制限を超過しています"

操作がクォータ制限を超えた場合は、次のいずれかの方法を実行できます。

- 同じパラメーターで、ただし VM数と VM コア数を減らして、新しいホスト プールを作成します。

- ブラウザーで statusMessage フィールドに表示されているリンクを開き、指定した VM SKU の Azure サブスクリプションのクォータを増やす要求を送信します。

### <a name="error-cant-see-user-assignments-in-app-groups"></a>エラー:アプリ グループでユーザー割り当てが表示されない。

**原因**: このエラーは通常、ある Azure Active Directory (AD) テナントから別のテナントにサブスクリプションを移した後に発生します。 以前の割り当てが古い Azure AD テナントにまだ関連付けられている場合、Azure portal はこれらを見失います。

**解決策**: ユーザーをアプリ グループに再割り当てする必要があります。

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>サービス オブジェクトの場所を設定する際、米国以外が表示されない

**原因**: 現時点では、Azure は Windows Virtual Desktop サービスのリージョンをサポートしていません。 サポートされている地域の詳細については、[データの場所](data-locations.md)に関するページを参照してください。 特定の場所が Windows Virtual Desktop でサポートされているにもかかわらず、場所の選択時に表示されない場合は、リソース プロバイダーがまだ更新されていないことを意味します。

**解決策**: リージョンの最新の一覧を取得するには、リソース プロバイダーを再登録します。

1. **[サブスクリプション]** に移動し、関連するサブスクリプションを選択します。
2. **[リソース プロバイダー]** を選択します。
3. **[Microsoft.DesktopVirtualization]** を見つけて選択し、アクション メニューから **[再登録]** を選択します。

リソース プロバイダーを再登録すると、特定の UI フィードバックや更新状態は表示されません。 再登録プロセスでも、既存の環境に干渉することはありません。

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager テンプレート エラー

Azure Resource Manager テンプレートと PowerShell DSC のデプロイ失敗をトラブルシューティングするには、次の手順に従います。

1. 「[Azure Resource Manager でのデプロイ操作の表示](../azure-resource-manager/templates/deployment-history.md)」を使用して、デプロイのエラーを確認します。
2. デプロイにエラーがない場合、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を使用して、アクティビティ ログのエラーを確認します。
3. エラーが特定されたら、エラー メッセージと、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」のリソースを使用して問題に対処します。
4. 以前のデプロイ中に作成されたすべてのリソースを削除し、テンプレートのデプロイを再試行します。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>エラー:デプロイに失敗しました….\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![デプロイ失敗のスクリーンショット。](media/failure-joindomain.png)

未処理エラーの例:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1:** VM をドメインに参加させるために提供された資格情報が正しくありません。

**解決策 1:** [セッション ホストの VM の構成](troubleshoot-vm-configuration.md)に関するページで、「VMs are not joined to the domain」\(VM がドメインに参加していません\) の「Incorrect credentials」\(資格情報が正しくありません\) エラーを参照してください。

**原因 2:** ドメイン名が解決されません。

**解決策 2:** 「[エラー: ドメイン名が解決されません](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)」 (「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」) を参照してください。

**原因 3:** 仮想ネットワーク (VNET) の DNS 構成が **既定** に設定されています。

これを解決するには、次の操作を実行します。

1. Azure portal を開いて、 **[仮想ネットワーク]** タブに移動します。
2. お使いの VNET を見つけて、 **[DNS サーバー]** を選択します。
3. 画面の右側に DNS サーバーのメニューが表示されます。 そのメニューで、 **[カスタム]** を選択します。
4. [カスタム] の下に表示されている DNS サーバーが、ドメイン コントローラーまたは Active Directory ドメインと一致していることを確認します。 お使いの DNS サーバーが表示されない場合は、 **[DNS サーバーの追加]** フィールドに値を入力して追加できます。

### <a name="error-your-deployment-failedunauthorized"></a>エラー:デプロイに失敗しました...\権限がありません

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因:** 使用しているサブスクリプションは、お客様の展開先のリージョンで必要な機能にアクセスすることができない種類です。 このエラーは、MSDN、無料、または教育向けのサブスクリプションなどで表示されることがあります。

**解決策:** サブスクリプションの種類またはリージョンを必要な機能にアクセスできるものに変更します。

### <a name="error-vmextensionprovisioningerror"></a>エラー:VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![ターミナルのプロビジョニングの状態 (失敗) を示しているデプロイ失敗のスクリーンショット。](media/failure-vmextensionprovisioning.png)

**原因 1:** Windows Virtual Desktop 環境の一時的なエラーです。

**原因 2:** 一時的な接続エラーです。

**解決策:** PowerShell を使用してサインインすることによって、Windows Virtual Desktop 環境が正常であることを確認します。 「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)」で、VM の登録を手動で終了します。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>エラー:指定した管理者のユーザー名は許可されません

> [!div class="mx-imgBorder"]
> ![指定した管理者が許可されないことを示しているデプロイ失敗のスクリーンショット。](media/failure-username.png)

未処理エラーの例:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**原因:** 指定されたパスワードに、禁止された部分文字列 (admin、administrator、root) が含まれています。

**解決策:** ユーザー名を更新するか、別のユーザーを使用します。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>エラー:拡張機能を処理しているときに VM がエラーを報告しました

> [!div class="mx-imgBorder"]
> ![リソース操作の完了とターミナルのプロビジョニングの状態を示している、デプロイ失敗のスクリーンショット。](media/failure-processing.png)

未処理エラーの例:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**原因:** PowerShell DSC 拡張機能が、VM 上で管理者アクセス権を取得できませんでした。

**解決策:** ユーザー名とパスワードが仮想マシン上で管理アクセス権を持っていることを確認して、Azure Resource Manager テンプレートをもう一度実行します。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>エラー:DeploymentFailed – PowerShell DSC 構成 'FirstSessionHost' がエラーで完了しました

> [!div class="mx-imgBorder"]
> ![PowerShell DSC 構成 'FirstSessionHost' がエラーで完了したデプロイ失敗のスクリーンショット。](media/failure-dsc.png)

未処理エラーの例:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**原因:** PowerShell DSC 拡張機能が、VM 上で管理者アクセス権を取得できませんでした。

**解決策:** 指定されたユーザー名とパスワードが仮想マシン上で管理アクセス権を持っていることを確認して、Azure Resource Manager テンプレートをもう一度実行します。

### <a name="error-deploymentfailed--invalidresourcereference"></a>エラー:DeploymentFailed – InvalidResourceReference

未処理エラーの例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因:** リソース グループ名の一部が、テンプレートによって作成されている特定のリソースに使用されます。 既存のリソースと名前が一致するため、テンプレートは別のグループから既存のリソースを選択する場合があります。

**解決策:** Azure Resource Manager テンプレートを実行してセッション ホスト VM をデプロイするときは、サブスクリプションのリソース グループ名の最初の 2 文字を一意にします。

### <a name="error-deploymentfailed--invalidresourcereference"></a>エラー:DeploymentFailed – InvalidResourceReference

未処理エラーの例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**原因:** このエラーの原因は、Azure Resource Manager テンプレートによって作成された NIC が、既に VNET にある別の NIC と同じ名前であることです。

**解決策:** 異なるホスト プレフィックスを使用します。

### <a name="error-deploymentfailed--error-downloading"></a>エラー:DeploymentFailed – ダウンロード中のエラー

未処理エラーの例:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**原因:** このエラーの原因は、Azure Resource Manager テンプレートに関連付けられている zip ファイルのダウンロードが、静的ルート、ファイアウォール規則、または NSG によってブロックされていることです。

**解決策:** ブロックしている静的ルート、ファイアウォール規則、または NSG を削除します。 必要に応じて、Azure Resource Manager テンプレートの json ファイルをテキスト エディターで開き、zip ファイルへのリンクを実行し、許可されている場所にリソースをダウンロードします。

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>エラー:VM を削除した後、ホスト プールからセッション ホストを削除することができません

**原因:** VM を削除する前に、セッション ホストを削除する必要があります。

**解決策:** セッション ホストをドレイン モードにして、セッション ホストからすべてのユーザーをサインアウトしてから、ホストを削除します。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop エージェントまたはセッション接続に関連した問題をトラブルシューティングするには、「[Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする](troubleshoot-agent.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
