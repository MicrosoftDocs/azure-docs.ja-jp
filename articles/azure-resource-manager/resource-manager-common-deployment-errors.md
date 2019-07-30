---
title: Azure へのデプロイで発生する一般的なエラーのトラブルシューティング | Microsoft Docs
description: Azure Resource Manager を使用した Azure へのリソースのデプロイ時に発生する一般的なエラーの解決方法について説明します。
tags: top-support-issue
author: tfitzmac
keywords: デプロイのエラー、Azure へのデプロイ、Azure へのデプロイ
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: fea7f77b1f4bcace23ad9164354c4f42e868869f
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206335"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング

この記事では、Azure へのデプロイに関する一般的なエラーについて説明し、そのエラーを解決するための情報を提供します。 デプロイ エラーのエラー コードを見つけることができない場合は、「[エラー コードを見つける](#find-error-code)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>エラー コード

| エラー コード | 対応策 | 詳細情報 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | ストレージ アカウントの名前付けの制限に従ってください。 | [ストレージ アカウント名の解決](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | 使用可能なストレージ アカウント プロパティを確認してください。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできません。 後で要求を再試行するか、別の VM サイズを要求します。 | [Linux のプロビジョニングと割り当ての問題](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)、[Windows のプロビジョニングと割り当ての問題](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)、[割り当てエラーのトラブルシューティング](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | 同時実行操作の完了を待ちます。 | |
| AuthorizationFailed | お客様のアカウントまたはサービス プリンシパルには、デプロイを完了するために十分なアクセス権がありません。 自分のアカウントが属するロールと、デプロイの範囲に対するアクセス権を確認してください。<br><br>必要なリソース プロバイダーが登録されていないと、このエラーを受け取ることがあります。 | [Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)<br><br>[登録を解決する](resource-manager-register-provider-errors.md) |
| BadRequest | Resource Manager で予期される値と一致しないデプロイ値を送信しました。 トラブルシューティングの方法については、内部ステータス メッセージを確認してください。 | [テンプレート リファレンス](/azure/templates/)と[サポートされている場所](resource-group-authoring-templates.md#resource-location) |
| 競合 | リソースの現在の状態では許可されていない操作を要求しています。 たとえば、ディスクのサイズ変更が許可されているのは、VM の作成時と VM の割り当て解除時のみです。 | |
| DeploymentActive | このリソース グループへの同時実行デプロイが完了するまで待ちます。 | |
| DeploymentFailed | DeploymentFailed エラーは、そのエラーを解決するために必要な詳細が示されない一般的なエラーです。 エラー コードのエラー詳細で情報を確認してください。 | [エラー コードを見つける](#find-error-code) |
| DeploymentQuotaExceeded | リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 Azure CLI の場合は [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) を、PowerShell では [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) を使用して、履歴からエントリを削除できます。 デプロイ履歴からエントリを削除しても、デプロイ リソースには影響しません。 | |
| DnsRecordInUse | DNS レコード名は、一意の名前にする必要があります。 別の名前を指定するか、既存のレコードを変更してください。 | |
| ImageNotFound | VM イメージの設定を確認してください。 |  |
| InUseSubnetCannotBeDeleted | リソースを更新しようとするときにこのエラーが発生することがありますが、リソースを削除して作成すると、要求が処理されます。 変更されていないすべての値を指定してください。 | [リソースを更新する](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 該当するテナントのアクセス トークンを取得します。 トークンは、自分のアカウントが属するテナントからのみ取得できます。 | |
| InvalidContentLink | これは、入れ子になった無効なテンプレートにリンクしようとしたことが原因と考えられます。 入れ子になったテンプレートに指定した URI を十分に確認してください。 ストレージ アカウントにテンプレートがある場合は、URI がアクセス可能であることを確認してください。 場合によっては、SAS トークンを渡す必要があります。 | [リンク済みテンプレート](resource-group-linked-templates.md) |
| InvalidParameter | リソースに対して指定したいずれかの値が、予期される値に一致しません。 このエラーはさまざまな状況が原因となって発生する可能性があります。 たとえば、パスワードが十分でない場合や、BLOB 名が正しくない場合があります。 エラー メッセージを確認して、どの値を修正する必要があるかを判断してください。 | |
| InvalidRequestContent | デプロイの値に予期されない値が含まれているか、必要な値が不足しています。 リソースの種類の値を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidRequestFormat | デプロイを実行するときにデバッグ ログを有効にし、要求の内容を確認してください。 | [デバッグ ログ](#enable-debug-logging) |
| InvalidResourceNamespace | **type** プロパティに指定したリソース名前空間を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidResourceReference | リソースがまだ存在しないか、正しく参照されていません。 依存関係を追加する必要があるかどうかを確認してください。 シナリオに必要なパラメーターを含めて **reference** 関数を使用しているかどうかを確認してください。 | [依存関係を解決する](resource-manager-not-found-errors.md) |
| InvalidResourceType | **type** プロパティに指定したリソースの種類を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidSubscriptionRegistrationState | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](resource-manager-register-provider-errors.md) |
| InvalidTemplate | テンプレートの構文にエラーがないか確認してください。 | [無効なテンプレートを解決する](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | デプロイ先のリソース グループと同じテナントに自分のアカウントが属しているかどうかを確認してください。 | |
| LinkedInvalidPropertyId | リソースのリソース ID が正しく解決されていません。 リソース ID に必要なすべての値 (サブスクリプション ID、リソース グループ名、リソースの種類、親リソース名 (必要な場合)、リソース名など) を指定しているかどうかを確認してください。 | |
| LocationRequired | リソースの場所を指定します。 | [場所を設定する](resource-group-authoring-templates.md#resource-location) |
| MismatchingResourceSegments | 入れ子になったリソースの名前と種類でセグメント数が正しいことを確認します。 | [リソース セグメントを解決する](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | リソース プロバイダーの登録状態、およびサポートされている場所を確認してください。 | [登録を解決する](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | リソース プロバイダーの登録状態を確認してください。 | [登録を解決する](resource-manager-register-provider-errors.md) |
| NotFound | 依存するリソースを、親リソースと並列にデプロイしようとしています。 依存関係を追加する必要があるかどうかを確認してください。 | [依存関係を解決する](resource-manager-not-found-errors.md) |
| OperationNotAllowed | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過する操作が試みられています。 可能であれば、クォータ内に収まるようにデプロイを修正してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](resource-manager-quota-errors.md) |
| ParentResourceNotFound | 子リソースを作成する前に親リソースが存在することを確認してください。 | [親リソースを解決する](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | 文字数の多すぎるパスワードを選択したか、パスワードの値をパラメーターとして渡す前にセキュリティで保護された文字列に変換した可能性があります。 テンプレートに **secure string** パラメーターが含まれている場合、値をセキュリティで保護された文字列に変換する必要はありません。 パスワードの値はテキストとして指定してください。 |  |
| PrivateIPAddressInReservedRange | 指定した IP アドレスは、Azure で必要なアドレス範囲に含まれます。 予約済みの範囲を避けるように IP アドレスを変更してください。 | [IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | 指定した IP アドレスがサブネットの範囲外です。 サブネットの範囲に収まるように IP アドレスを変更してください。 | [IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | デプロイ済みのリソースで、一部のプロパティを変更できません。 リソースを更新する際は、許可されているプロパティに変更を制限してください。 | [リソースを更新する](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | デプロイ時に実行しようとしているアクションを禁止するリソース ポリシーがサブスクリプションに含まれます。 アクションをブロックしているポリシーを見つけてください。 可能であれば、ポリシーの制限を満たすようにデプロイを変更してください。 | [ポリシーを解決する](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | 予約された名前が含まれていないリソース名を指定します。 | [予約されたリソース名](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 削除が完了するまで待ちます。 | |
| ResourceGroupNotFound | デプロイのターゲット リソース グループの名前を確認してください。 サブスクリプションにそのリソース グループが既に存在している必要があります。 サブスクリプションのコンテキストを確認してください。 | [Azure CLI](/cli/azure/account?#az-account-set)、[PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 解決できないリソースをデプロイで参照しています。 **reference** 関数に、シナリオに必要なパラメーターを含まれていることを確認してください。 | [参照を解決する](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過するリソースの作成が試みられています。 可能であれば、クォータ内に収まるようにインフラストラクチャを変更してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](resource-manager-quota-errors.md) |
| SkuNotAvailable | 選択した場所で利用可能な SKU (VM サイズなど) を選択します。 | [SKU を解決する](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | サブスクリプション、リソース グループ、および使用するストレージ アカウントの名前を確認してください。 | |
| SubnetsNotInSameVnet | 仮想マシンでは、仮想ネットワークを 1 つのみを持つことができます。 複数の NIC をデプロイするときは、それらが同じ仮想ネットワークに属していることを確認してください。 | [複数の NIC](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | 1 回のデプロイのリソース グループ数を減らします。 | [リソース グループ間のデプロイ](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>エラー コードを見つける

発生する可能性のあるエラーには、次の 2 種類があります。

* 検証エラー
* デプロイ エラー

検証エラーは、デプロイ前に判断できるシナリオで発生します。 これには、テンプレートに構文エラーがある場合や、サブスクリプション クォータを超えるリソースをデプロイしようとしている場合などがあります。 デプロイ エラーは、デプロイ プロセスで発生する状況に起因します。 これには、並列でデプロイされているリソースにアクセスしようとしている場合などがあります。

この 2 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーも[アクティビティ ログ](resource-group-audit.md)に表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。

### <a name="validation-errors"></a>検証エラー

ポータルでのデプロイ時に、値を送信した後に検証エラーを確認します。

![ポータルの検証エラーの表示](./media/resource-manager-common-deployment-errors/validation-error.png)

詳細については、メッセージを選択します。 次の図では、**InvalidTemplateDeployment** エラーとポリシーがデプロイをブロックしていることを示すメッセージが示されます。

![検証の詳細の表示](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>デプロイ エラー

操作が検証に合格しても、デプロイ中に失敗した場合、デプロイ エラーを受け取ります。

PowerShell でデプロイ エラー コードとメッセージを表示するには、以下を使用します。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Azure CLI でデプロイ エラー コードとメッセージを表示するには、以下を使用します。

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

ポータルで通知を選択します。

![通知エラー](./media/resource-manager-common-deployment-errors/notification.png)

デプロイに関する詳細を表示します。 オプションを選択して、エラーの詳細を確認します。

![デプロイの失敗](./media/resource-manager-common-deployment-errors/deployment-failed.png)

エラー メッセージとエラー コードを表示します。 2 つのエラー コードがあることを確認します。 最初のエラー コード (**DeploymentFailed**) は、解決する必要があるエラーの詳細が示されない一般的なエラーです。 2 つ目のエラー コード (**StorageAccountNotFound**) は、必要な詳細が示されます。 

![エラーの詳細](./media/resource-manager-common-deployment-errors/error-details.png)

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
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

次のコマンドを使用して、要求の内容を確認します。

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

次のコマンドを使用して、応答の内容を確認します。

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>入れ子になったテンプレート

入れ子になったテンプレートのデバッグ情報をログに記録するには、**debugSetting** 要素を使用します。

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
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
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

または、設定の誤った依存関係に関連すると思われるデプロイ エラーが発生していると仮定します。 テンプレートをテストするには、これを簡略化されたテンプレートに分解します。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 そのリソースを正しく定義したことを確認したら、それに依存するリソース (SQL Database など) を追加します。 これら 2 つのリソースを正しく定義したことを確認したら、その他の依存リソース (監査ポリシーなど) を追加します。 各テスト デプロイの合間に、リソース グループを削除して、依存関係を適切にテストしていることを確認します。


## <a name="next-steps"></a>次の手順

* トラブルシューティング チュートリアルについては、「[Tutorial:Troubleshoot Resource Manager template deployments (チュートリアル: Resource Manager テンプレートのデプロイのトラブルシューティング)](./resource-manager-tutorial-troubleshoot.md)」を参照してください。
* 監査アクションについては、「 [リソース マネージャーの監査操作](resource-group-audit.md)」をご覧ください。
* デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](resource-manager-deployment-operations.md)に関するページを参照してください。
