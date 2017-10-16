---
ms.assetid: 
title: "Azure Key Vault - PowerShell で論理的な削除を使用する方法"
description: "PowerShell コード スニペットを使用した論理的な削除のユース ケースの例"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: bruceper
ms.openlocfilehash: 8cf0674f7eb139e50da4a3c22a8d8376a86b0dcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>PowerShell で Key Vault の論理的な削除を使用する方法

Azure Key Vault の論理的な削除機能を使用すると、削除されたコンテナーとコンテナー オブジェクトを復旧できます。 具体的には、論理的な削除は次のシナリオに対処します。

- キー コンテナーの回復可能な削除のサポート
- キー コンテナー オブジェクトの回復可能な削除のサポート (例: キー、シークレット、証明書)

## <a name="prerequisites"></a>前提条件

- Azure PowerShell 4.0.0 以上: まだセットアップしていない場合は、Azure PowerShell をインストールしてお使いの Azure サブスクリプションに関連付けます。詳しくは、「[Azure PowerShell のインストールおよび構成方法](https://docs.microsoft.com/powershell/azure/overview)」をご覧ください。 

>[!NOTE]
> Key Vault PowerShell 出力書式設定ファイルには古いバージョンがあり、正しいバージョンではなく、これが環境に読み込まれる**可能性があります**。 PowerShell の更新バージョンには出力書式設定に必要な修正が含まれることを想定しており、このトピックはその時点で更新されます。 この出力書式設定に関する問題が発生した場合の現在の対処法は次のとおりです。
> - このトピックで説明されている論理的な削除が有効なプロパティが表示されない場合は、クエリ `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete` を使用します。


PowerShell における Key Vault の具体的な参照情報については、[Azure Key Vault の PowerShell のリファレンス](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)に関するページをご覧ください。

## <a name="required-permissions"></a>必要なアクセス許可

Key Vault の操作は、次のようにロールベースのアクセス制御 (RBAC) のアクセス許可で別個に管理されます。

| 操作 | Description | ユーザーのアクセス許可 |
|:--|:--|:--|
|一覧表示|削除されたキー コンテナーの一覧を示します。|Microsoft.KeyVault/deletedVaults/read|
|復旧|削除されたキー コンテナーを復元します。|Microsoft.KeyVault/vaults/write|
|消去|削除されたキー コンテナーとそのコンテンツを永続的に削除します。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

アクセス許可とアクセスの制御について詳しくは、「[キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)」をご覧ください。

## <a name="enabling-soft-delete"></a>論理的な削除を有効にする

削除されたキー コンテナーやキー コンテナーに保存されたオブジェクトを復旧するには、まずそのキー コンテナーの論理的な削除を有効にする必要があります。

### <a name="existing-key-vault"></a>既存のキー コンテナー

ContosoVault という名前の既存のキー コンテナーの場合、次のようにして論理的な削除を有効にします。 

>[!NOTE]
>現時点では、Azure Resource Manager リソース操作を使用して、Key Vault リソースに *enableSoftDelete* プロパティを直接書き込む必要があります。

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>新しいキー コンテナー

新しいキー コンテナーの論理的な削除は、作成時に create コマンドに論理的な削除を有効にするフラグを追加することで有効にできます。

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>論理的な削除が有効になっていることを確認する

キー コンテナーで論理的な削除が有効になっていることを確認するには、*get* コマンドを実行して 'Soft Delete Enabled?' 属性を探し、その設定が true になっているか false になっているかを確認します。

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>論理的な削除で保護されているキー コンテナーを削除する

キー コンテナーを削除するコマンドは同じですが、論理的な削除が有効になっているかどうかでその動作が変わります。

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>論理的な削除が有効になっていない状態でキー コンテナーで前のコマンドを実行すると、このキー コンテナーとそのコンテンツがすべて永続的に削除され、復旧できません。

### <a name="how-soft-delete-protects-your-key-vaults"></a>論理的な削除がキー コンテナーを保護する方法

論理的な削除を有効にした場合:

- キー コンテナーが削除されると、そのリソース グループから削除され、予約された名前空間に配置されます。この名前空間はそれが作成された場所にのみ関連付けられています。 
- 削除されたキー コンテナーにあるオブジェクト (キー、シークレット、証明書など) にはアクセスできず、それらオブジェクトが含まれるキー コンテナーが削除されている状態にある間はアクセスできません。 
- 削除された状態にあるキー コンテナーの DNS 名は予約されたままであるため、同じ名前の新しいキー コンテナーは作成できません。  

サブスクリプションに関連付けられている削除された状態のキー コンテナーは、次のコマンドを使用して表示できます。

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

出力の *Resource ID* は、このコンテナーの元のリソース ID を指します。 このキー コンテナーは削除された状態にあるため、このリソース ID のリソースは存在しません。 *Id* フィールドは、復旧または消去するときにリソースを識別するために使用できます。 *Scheduled Purge Date* フィールドは、削除されたコンテナーに対して何のアクションも実行しない場合に、そのコンテナーが永続的に削除 (消去) されるタイミングを示します。 *Scheduled Purge Date* の計算に使用される既定のリテンション期間は 90 日です。

## <a name="recovering-a-key-vault"></a>キー コンテナーの復旧

キー コンテナーを復旧するには、キー コンテナー、リソース グループ、および場所を指定する必要があります。 キー コンテナーの復旧プロセスに必要であるため、削除されたキー コンテナーの場所とリソース グループはメモしておいてください。

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

キー コンテナーを復旧すると、結果はそのキー コンテナーの元のリソース ID を持つ新しいリソースになります。 キー コンテナーが存在していたリソース グループが削除された場合、そのキー コンテナーを復旧する前に同じ名前の新しいリソース グループを作成する必要があります。

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault のオブジェクトと論理的な削除

論理的な削除が有効な 'ContosoVault' という名前のキー コンテナーにあるキー 'ContosoFirstKey' を削除する方法は次のとおりです。

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

論理的な削除が有効なキー コンテナーでは、削除されたキーは削除されたように見えます。ただし、削除したキーを明示的に一覧表示または取得するとその限りではありません。 削除された状態のキーに対するほとんどの操作は、削除されたキーの一覧表示、復旧、または消去を除いて失敗します。 

たとえば、キー コンテナーで削除されたキーの一覧表示を要求するには、次のコマンドを使用します。

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>切り替え状態 

論理的な削除が有効なキー コンテナーでキーを削除すると、切り替えが完了するまでに数秒かかる場合があります。 この切り替え状態の間、キーがアクティブな状態でも削除された状態でもなくなる場合があります。 このコマンドは 'ContosoVault' という名前のキー コンテナーにある削除されたすべてのキーの一覧を表示します。

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>キー コンテナー オブジェクトで論理的な削除を使用する

キー コンテナーと同様に、削除されたキー、シークレット、または証明書は復旧または消去しない限り、最大で 90 日間削除された状態にあります。 

#### <a name="keys"></a>構成する

削除されたキーを復旧するには:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

キーを永続的に削除するには:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>キーを消去するとキーは永続的に削除され、復旧できなくなります。

**recover** アクションと **purge** アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。 ユーザーまたはサービス プリンシパルが **recover** アクションまたは **purge** アクションを実行するには、キー コンテナーのアクセス ポリシーにそのオブジェクト (キーまたはシークレット) に対する適切なアクセス許可が必要です。 既定では、'all' ショートカットを使用してユーザーにすべてのアクセス許可を付与するときに、**purge** のアクセス許可はキー コンテナーのアクセス ポリシーには追加されません。 **purge** のアクセス許可は明示的に付与する必要があります。 たとえば、次のコマンドは user@contoso.com のアクセス許可を付与し、**purge** を含む複数の操作を *ContosoVault* のキーに対して実行します。

#### <a name="set-a-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを設定する

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> 論理的な削除を有効にした直後の既存のキー コンテナーの場合、**recover** や **purge** のアクセス許可がない場合があります。

#### <a name="secrets"></a>シークレット

キーと同様に、キー コンテナー内のシークレットは独自のコマンドを使用して操作します。 次のコマンドは、シークレットを削除、一覧表示、復旧、および消去します。

- SQLPassword という名前のシークレットを削除します。 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- キー コンテナー内の削除されたシークレットをすべて一覧表示します。 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- 削除された状態のシークレットを復旧します。 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- 削除された状態のシークレットを消去します。 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>シークレットを消去するとシークレットは永続的に削除され、復旧できなくなります。

## <a name="purging-and-key-vaults"></a>消去とキー コンテナー

### <a name="key-vault-objects"></a>キー コンテナー オブジェクト

キー、シークレット、または証明書は消去すると永続的に削除され、復旧できなくなります。 ただし、削除されたオブジェクトが含まれるキー コンテナーは、そのキー コンテナー内のすべてのオブジェクトと同様にそのまま残ります。 

### <a name="key-vaults-as-containers"></a>コンテナーとしてのキー コンテナー
キー コンテナーを消去すると、キー、シークレット、証明書などのすべてのコンテンツは永続的に削除されます。 キー コンテナーを消去するには、`Remove-AzureRmKeyVault` コマンドをオプション `-InRemovedState` を使用および `-Location location` 引数を使用して削除されたキー コンテナーの場所を指定して実行します。 削除されたコンテナーの場所は、`Get-AzureRmKeyVault -InRemovedState` コマンドを使用して見つけることができます。

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>キー コンテナーを消去するとキー コンテナーは永続的に削除され、復旧できなくなります。

### <a name="purge-permissions-required"></a>必要な消去のアクセス許可
- 削除されたキー コンテナーを消去し、そのコンテナーとそのすべてのコンテンツを永続的に削除するには、ユーザーに *Microsoft.KeyVault/locations/deletedVaults/purge/action* 操作を実行するための RBAC のアクセス許可が必要です。 
- 削除されたキーの一覧を表示するには、コンテナーのユーザーに *Microsoft.KeyVault/deletedVaults/read* のアクセス許可を実行する RBAC のアクセス許可が必要です。 
- 既定では、このアクセス許可を持つのはサブスクリプションの管理者のみです。 

### <a name="scheduled-purge"></a>スケジュールされた消去

削除されたキー コンテナー オブジェクトの一覧には、Key Vault による消去がスケジュールされたタイミングが表示されます。 *Scheduled Purge Date* フィールドは、何のアクションも実行しない場合に、そのキー コンテナー オブジェクトが永続的に削除されるタイミングを示します。 既定では、削除されたキー コンテナー オブジェクトのリテンション期間は 90 日です。

>[!NOTE]
>*Scheduled Purge Date* フィールドでトリガーされた消去済みのコンテナー オブジェクトは永続的に削除されます。 これは復旧できません。

## <a name="other-resources"></a>その他のリソース

- Key Vault の論理的な削除機能の概要については、「[Azure Key Vault の論理的な削除機能の概要](key-vault-ovw-soft-delete.md)」をご覧ください。
- Azure Key Vault の使用方法の概要については、「[Azure Key Vault の概要](key-vault-get-started.md)」をご覧ください。

