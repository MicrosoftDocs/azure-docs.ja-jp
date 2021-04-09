---
title: 一般的なデプロイ エラーのトラブルシューティング
description: Azure Resource Manager を使用した Azure へのリソースのデプロイ時に発生する一般的なエラーの解決方法について説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 6cb2b611e694c6b72e063f3d8ab05b7667a34e29
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888857"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング

この記事では、Azure へのデプロイに関する一般的なエラーについて説明し、そのエラーを解決するための情報を提供します。 デプロイ エラーのエラー コードを見つけることができない場合は、「[エラー コードを見つける](#find-error-code)」を参照してください。

エラー コードに関する情報を探していて、その情報がこの記事に記載されていない場合は、お知らせください。 このページの最後で、フィードバックを残していただくことができます。 このフィードバックは、GitHub のイシューで追跡されます。

## <a name="error-codes"></a>エラー コード

| エラー コード | 対応策 | 詳細情報 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | ストレージ アカウントの名前付けの制限に従ってください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 使用可能なストレージ アカウント プロパティを確認してください。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできません。 後で要求を再試行するか、別の VM サイズを要求します。 | [Linux のプロビジョニングと割り当ての問題](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-linux.md)、[Windows のプロビジョニングと割り当ての問題](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-windows.md)、[割り当てエラーのトラブルシューティング](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | 同時実行操作の完了を待ちます。 | |
| AuthorizationFailed | お客様のアカウントまたはサービス プリンシパルには、デプロイを完了するために十分なアクセス権がありません。 自分のアカウントが属するロールと、デプロイの範囲に対するアクセス権を確認してください。<br><br>必要なリソース プロバイダーが登録されていないと、このエラーを受け取ることがあります。 | [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[登録を解決する](error-register-resource-provider.md) |
| BadRequest | Resource Manager で予期される値と一致しないデプロイ値を送信しました。 トラブルシューティングの方法については、内部ステータス メッセージを確認してください。 | [テンプレート リファレンス](/azure/templates/)と[サポートされている場所](resource-location.md) |
| Conflict | リソースの現在の状態では許可されていない操作を要求しています。 たとえば、ディスクのサイズ変更が許可されているのは、VM の作成時と VM の割り当て解除時のみです。 | |
| DeploymentActiveAndUneditable | このリソース グループへの同時実行デプロイが完了するまで待ちます。 | |
| DeploymentFailedCleanUp | 完全モードでデプロイすると、テンプレートにないすべてのリソースが削除されます。 このエラーは、テンプレートにないすべてのリソースを削除するための適切なアクセス許可がない場合に表示されます。 このエラーを回避するには、デプロイ モードを増分モードに変更します。 | [Azure Resource Manager のデプロイ モード](deployment-modes.md) |
| DeploymentNameInvalidCharacters | デプロイ名に使用できるのは、文字、数字、'-'、'.' または '_'. のみです。 | |
| DeploymentNameLengthLimitExceeded | デプロイ名は、64 文字までに制限されています。  | |
| DeploymentFailed | DeploymentFailed エラーは、そのエラーを解決するために必要な詳細が示されない一般的なエラーです。 エラー コードのエラー詳細で情報を確認してください。 | [エラー コードを見つける](#find-error-code) |
| DeploymentQuotaExceeded | リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 | [デプロイ数が 800 を超えたときのエラーを解決する](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS レコード名は、一意の名前にする必要があります。 別の番号を入力します。 | |
| ImageNotFound | VM イメージの設定を確認してください。 |  |
| InUseSubnetCannotBeDeleted | リソースを更新しようとするときにこのエラーが発生することがありますが、リソースを削除して作成すると、要求が処理されます。 変更されていないすべての値を指定してください。 | [リソースを更新する](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 該当するテナントのアクセス トークンを取得します。 トークンは、自分のアカウントが属するテナントからのみ取得できます。 | |
| InvalidContentLink | これは、入れ子になった無効なテンプレートにリンクしようとしたことが原因と考えられます。 入れ子になったテンプレートに指定した URI を十分に確認してください。 ストレージ アカウントにテンプレートがある場合は、URI がアクセス可能であることを確認してください。 場合によっては、SAS トークンを渡す必要があります。 現時点では、[Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)の背後にあるストレージ アカウントのテンプレートにリンクすることはできません。 GitHub などの別のリポジトリにテンプレートを移動することを検討してください。 | [リンク済みテンプレート](linked-templates.md) |
| InvalidDeploymentLocation | サブスクリプション レベルでデプロイする場合は、以前に使用したデプロイ名に別の場所を指定しています。 | [サブスクリプション レベルのデプロイ](deploy-to-subscription.md) |
| InvalidParameter | リソースに対して指定したいずれかの値が、予期される値に一致しません。 このエラーはさまざまな状況が原因となって発生する可能性があります。 たとえば、パスワードが十分でない場合や、BLOB 名が正しくない場合があります。 エラー メッセージから、どの値を修正する必要があるかがわかるはずです。 | |
| InvalidRequestContent | デプロイの値に認識されない値が含まれているか、必要な値が不足しています。 リソースの種類の値を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidRequestFormat | デプロイを実行するときにデバッグ ログを有効にし、要求の内容を確認してください。 | [デバッグ ログ](#enable-debug-logging) |
| InvalidResourceNamespace | **type** プロパティに指定したリソース名前空間を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidResourceReference | リソースがまだ存在しないか、正しく参照されていません。 依存関係を追加する必要があるかどうかを確認してください。 シナリオに必要なパラメーターを含めて **reference** 関数を使用しているかどうかを確認してください。 | [依存関係を解決する](error-not-found.md) |
| InvalidResourceType | **type** プロパティに指定したリソースの種類を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidSubscriptionRegistrationState | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](error-register-resource-provider.md) |
| InvalidTemplate | テンプレートの構文にエラーがないか確認してください。 | [無効なテンプレートを解決する](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | デプロイ先のリソース グループと同じテナントに自分のアカウントが属しているかどうかを確認してください。 | |
| LinkedInvalidPropertyId | リソースのリソース ID が正しく解決されていません。 リソース ID に必要なすべての値 (サブスクリプション ID、リソース グループ名、リソースの種類、親リソース名 (必要な場合)、リソース名など) を指定しているかどうかを確認してください。 | |
| LocationRequired | リソースの場所を指定します。 | [場所を設定する](resource-location.md) |
| MismatchingResourceSegments | 入れ子になったリソースの名前と種類でセグメント数が正しいことを確認します。 | [リソース セグメントを解決する](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | リソース プロバイダーの登録状態、およびサポートされている場所を確認してください。 | [登録を解決する](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](error-register-resource-provider.md) |
| NoRegisteredProviderFound | リソース プロバイダーの登録状態を確認してください。 | [登録を解決する](error-register-resource-provider.md) |
| NotFound | 依存するリソースを、親リソースと並列にデプロイしようとしている可能性があります。 依存関係を追加する必要があるかどうかを確認してください。 | [依存関係を解決する](error-not-found.md) |
| OperationNotAllowed | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過する操作が試みられています。 可能であれば、クォータ内に収まるようにデプロイを修正してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](error-resource-quota.md) |
| ParentResourceNotFound | 子リソースを作成する前に親リソースが存在することを確認してください。 | [親リソースを解決する](error-parent-resource.md) |
| PasswordTooLong | 文字数の多すぎるパスワードを選択したか、パスワードの値をパラメーターとして渡す前にセキュリティで保護された文字列に変換した可能性があります。 テンプレートに **secure string** パラメーターが含まれている場合、値をセキュリティで保護された文字列に変換する必要はありません。 パスワードの値はテキストとして指定してください。 |  |
| PrivateIPAddressInReservedRange | 指定した IP アドレスは、Azure で必要なアドレス範囲に含まれます。 予約済みの範囲を避けるように IP アドレスを変更してください。 | [IP アドレス](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | 指定した IP アドレスがサブネットの範囲外です。 サブネットの範囲に収まるように IP アドレスを変更してください。 | [IP アドレス](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | デプロイ済みのリソースで、一部のプロパティを変更できません。 リソースを更新する際は、許可されているプロパティに変更を制限してください。 | [リソースを更新する](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | デプロイ時に実行しようとしているアクションを禁止するリソース ポリシーがサブスクリプションに含まれます。 アクションをブロックしているポリシーを見つけてください。 可能であれば、ポリシーの制限を満たすようにデプロイを変更してください。 | [ポリシーを解決する](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | 予約された名前が含まれていないリソース名を指定します。 | [予約されたリソース名](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 削除が完了するまで待ちます。 | |
| ResourceGroupNotFound | デプロイのターゲット リソース グループの名前を確認してください。 サブスクリプションにターゲット リソース グループが既に存在している必要があります。 サブスクリプションのコンテキストを確認してください。 | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 解決できないリソースをデプロイで参照しています。 **reference** 関数に、シナリオに必要なパラメーターを含まれていることを確認してください。 | [参照を解決する](error-not-found.md) |
| ResourceQuotaExceeded | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過するリソースの作成が試みられています。 可能であれば、クォータ内に収まるようにインフラストラクチャを変更してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](error-resource-quota.md) |
| SkuNotAvailable | 選択した場所で利用可能な SKU (VM サイズなど) を選択します。 | [SKU を解決する](error-sku-not-available.md) |
| StorageAccountAlreadyExists | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| StorageAccountNotFound | サブスクリプション、リソース グループ、および使用するストレージ アカウントの名前を確認してください。 | |
| SubnetsNotInSameVnet | 仮想マシンでは、仮想ネットワークを 1 つのみを持つことができます。 複数の NIC をデプロイするときは、それらが同じ仮想ネットワークに属していることを確認してください。 | [複数の NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | デプロイ用に指定されたサブスクリプションにアクセスできません。 サブスクリプション ID が間違っている、テンプレートをデプロイしているユーザーにサブスクリプションにデプロイするための適切なアクセス許可がない、またはサブスクリプション ID の形式が間違っている可能性があります。 入れ子になったデプロイを使用して[スコープにまたがってデプロイ](./deploy-to-resource-group.md)する場合は、サブスクリプションの GUID を指定してください。 | |
| SubscriptionNotRegistered | リソースをデプロイするときは、リソース プロバイダーをサブスクリプションに登録する必要があります。 デプロイに Azure Resource Manager テンプレートを使用すると、リソース プロバイダーがサブスクリプションに自動的に登録されます。 自動登録が時間内に完了しないことがあります。 この断続的なエラーを回避するには、デプロイの前にリソース プロバイダーを登録します。 | [登録を解決する](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | 1 回のデプロイのリソース グループ数を減らします。 | [スコープをまたいだデプロイ](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>エラー コードを見つける

発生する可能性のあるエラーには、次の 2 種類があります。

* 検証エラー
* デプロイ エラー

検証エラーは、デプロイ前に判断できるシナリオで発生します。 これには、テンプレートに構文エラーがある場合や、サブスクリプション クォータを超えるリソースをデプロイしようとしている場合などがあります。 デプロイ エラーは、デプロイ プロセスで発生する状況に起因します。 これには、並列でデプロイされているリソースにアクセスしようとしている場合などがあります。

この 2 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーも[アクティビティ ログ](../management/view-activity-logs.md)に表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。

### <a name="validation-errors"></a>検証エラー

ポータルでのデプロイ時に、値を送信した後に検証エラーを確認します。

![ポータルの検証エラーの表示](./media/common-deployment-errors/validation-error.png)

詳細については、メッセージを選択します。 次の図では、**InvalidTemplateDeployment** エラーとポリシーがデプロイをブロックしていることを示すメッセージが示されます。

![検証の詳細の表示](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>デプロイ エラー

操作が検証に合格しても、デプロイ中に失敗した場合、デプロイ エラーを受け取ります。

PowerShell でデプロイ エラー コードとメッセージを表示するには、以下を使用します。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Azure CLI でデプロイ エラー コードとメッセージを表示するには、以下を使用します。

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

ポータルで通知を選択します。

![通知エラー](./media/common-deployment-errors/notification.png)

デプロイに関する詳細を表示します。 オプションを選択して、エラーの詳細を確認します。

![デプロイの失敗](./media/common-deployment-errors/deployment-failed.png)

エラー メッセージとエラー コードを表示します。 2 つのエラー コードがあることを確認します。 最初のエラー コード (**DeploymentFailed**) は、解決する必要があるエラーの詳細が示されない一般的なエラーです。 2 つ目のエラー コード (**StorageAccountNotFound**) は、必要な詳細が示されます。

![エラーの詳細](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>デバッグ ログの有効化

問題が発生した原因を調べるには、要求と応答の詳細が必要な場合があります。 デプロイ中に、追加情報をログに記録することを要求できます。

### <a name="powershell"></a>PowerShell

PowerShell では、**DeploymentDebugLogLevel** パラメーターを All、ResponseContent、または RequestContent に設定します。

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

次のコマンドレットを使用して、要求の内容を確認します。

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

次のコマンドレットを使用して、応答の内容を確認します。

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

この情報は、テンプレートの値が正しく設定されているかどうかを確認するのに役立ちます。

### <a name="azure-cli"></a>Azure CLI

現在、Azure CLI はデバッグ ログの有効化をサポートしていませんが、デバッグ ログを取得できます。

次のコマンドでデプロイ操作を確認します。

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

次のコマンドを使用して、要求の内容を確認します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

次のコマンドを使用して、応答の内容を確認します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>入れ子になったテンプレート

入れ子になったテンプレートのデバッグ情報をログに記録するには、**debugSetting** 要素を使用します。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>トラブルシューティング用テンプレートの作成

場合によっては、テンプレートのトラブルシューティングの最も簡単な方法は、テンプレートの一部をテストすることです。 エラーの原因と考えられる部分に注目できる簡略化されたテンプレートを作成できます。 たとえば、リソースの参照時にエラーが発生しているとします。 この場合、テンプレート全体を処理するのではなく、問題の原因と考えられる部分を返すテンプレートを作成します。 これにより、適切なパラメーターを渡しているかどうか、テンプレート関数を正しく使用しているかどうか、求めているリソースを取得しているかどうかを確認できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

または、設定が誤っている依存関係に関連すると思われるデプロイ エラーが発生しているとします。 テンプレートをテストするには、これを簡略化されたテンプレートに分解します。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 そのリソースを正しく定義したことを確認したら、それに依存するリソース (SQL Database など) を追加します。 これら 2 つのリソースを正しく定義したことを確認したら、その他の依存リソース (監査ポリシーなど) を追加します。 各テスト デプロイの合間に、リソース グループを削除して、依存関係を適切にテストしていることを確認します。

## <a name="next-steps"></a>次のステップ

* トラブルシューティング チュートリアルについては、「[チュートリアル: Resource Manager テンプレート デプロイのトラブルシューティング](template-tutorial-troubleshoot.md)
* 監査アクションについては、「 [リソース マネージャーの監査操作](../management/view-activity-logs.md)」をご覧ください。
* デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](deployment-history.md)に関するページを参照してください。