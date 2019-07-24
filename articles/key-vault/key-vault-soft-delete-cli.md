---
title: Azure Key Vault - CLI で論理的な削除を使用する方法
description: CLI コード スニペットを使用した論理的な削除のユース ケース
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mbaldwin
ms.openlocfilehash: aa9b89b9afec069e97236b7652e0f1d37644f5cf
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336076"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>CLI で Key Vault の論理的な削除を使用する方法

Azure Key Vault の論理的な削除機能を使用すると、削除されたコンテナーとコンテナー オブジェクトを復旧できます。 具体的には、論理的な削除は次のシナリオに対処します。

- キー コンテナーの回復可能な削除のサポート
- キー コンテナー オブジェクトの回復可能な削除のサポート (例: キー、シークレット、証明書)

## <a name="prerequisites"></a>前提条件

- Azure CLI - お使いの環境にこのセットアップがない場合は、「[CLI 2.0 を使用した Key Vault の管理](key-vault-manage-with-cli2.md)」をご覧ください。

Key Vault のCLI に関する具体的なリファレンス情報については、[Azure CLI Key Vault のリファレンス](https://docs.microsoft.com/cli/azure/keyvault)に関するページをご覧ください。

## <a name="required-permissions"></a>必要なアクセス許可

Key Vault の操作は、次のようにロールベースのアクセス制御 (RBAC) のアクセス許可で別個に管理されます。

| Operation | 説明 | ユーザーのアクセス許可 |
|:--|:--|:--|
|List|削除されたキー コンテナーの一覧を示します。|Microsoft.KeyVault/deletedVaults/read|
|復旧|削除されたキー コンテナーを復元します。|Microsoft.KeyVault/vaults/write|
|消去|削除されたキー コンテナーとそのコンテンツを永続的に削除します。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

アクセス許可とアクセスの制御について詳しくは、「[キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)」をご覧ください。

## <a name="enabling-soft-delete"></a>論理的な削除を有効にする

"論理的な削除" を有効にすると、削除されたキー コンテナーや、キー コンテナーに格納されているオブジェクトを復旧できるようになります。

> [!IMPORTANT]
> キー コンテナーに対して '論理的な削除' を有効にした場合、それを取り消すことはできません。 論理的な削除のプロパティを "true" に設定すると、それを変更したり削除することはできなくなります。  

### <a name="existing-key-vault"></a>既存のキー コンテナー

ContosoVault という名前の既存のキー コンテナーの場合、次のようにして論理的な削除を有効にします。 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>新しいキー コンテナー

新しいキー コンテナーの論理的な削除は、作成時に create コマンドに論理的な削除を有効にするフラグを追加することで有効にできます。

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>論理的な削除が有効になっていることを確認する

キー コンテナーで論理的な削除が有効になっていることを確認するには、*show* コマンドを実行して 'Soft Delete Enabled?' 属性を探します。

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>論理的な削除で保護されているキー コンテナーを削除する

キー コンテナーを削除するコマンドの動作は、論理的な削除が有効になっているかどうかによって変わります。

> [!IMPORTANT]
>論理的な削除が有効になっていない状態でキー コンテナーで次のコマンドを実行すると、このキー コンテナーとそのコンテンツがすべて永続的に削除され、復旧できません。

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>論理的な削除がキー コンテナーを保護する方法

論理的な削除を有効にした場合:

- 削除されたキー コンテナーはそのリソース グループから削除され、予約された名前空間に配置されます。この名前空間はそれが作成された場所に関連付けられています。 
- 削除されたオブジェクト (キー、シークレット、証明書など) にはアクセスできず、それらオブジェクトが含まれるキー コンテナーが削除されている状態にある間はアクセスできません。 
- 削除されたキー コンテナーの DNS 名は予約されているため、新しいキー コンテナーを同じ名前で作成することはできません。  

サブスクリプションに関連付けられている削除された状態のキー コンテナーは、次のコマンドを使用して表示できます。

```azurecli
az keyvault list-deleted
```
- *ID* は、復旧または消去するときにリソースを識別するために使用できます。 
- *Resource ID* は、そのコンテナーの元のリソース ID です。 このキー コンテナーは削除された状態にあるため、このリソース ID のリソースは存在しません。 
- *Scheduled Purge Date* は、何のアクションも実行しない場合に、そのコンテナーが永続的に削除されるタイミングを示します。 *Scheduled Purge Date* の計算に使用される既定のリテンション期間は 90 日です。

## <a name="recovering-a-key-vault"></a>キー コンテナーの復旧

キー コンテナーを復旧するには、キー コンテナー、リソース グループ、および場所を指定します。 削除されたキー コンテナーの場所とリソース グループは、復旧プロセスに必要となるのでメモしておいてください。

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

キー コンテナーを復旧すると、そのキー コンテナーの元のリソース ID を持つ新しいリソースが作成されます。 元のリソース グループが削除された場合は、復旧を試行する前に、同じ名前のリソース グループを作成する必要があります。

## <a name="deleting-and-purging-key-vault-objects"></a>キー コンテナー オブジェクトを削除して消去する

次のコマンドを実行すると、論理的な削除が有効になっている、'ContosoVault' という名前のキー コンテナーから、'ContosoFirstKey' キーが削除されます。

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

論理的な削除が有効なキー コンテナーでは、削除されたキーは削除されたように見えます。ただし、削除したキーを明示的に一覧表示または取得するとその限りではありません。 削除された状態のキーに対するほとんどの操作は、削除されたキーの一覧表示、復旧、または消去を除いて失敗します。 

たとえば、キー コンテナーで削除されたキーの一覧表示を要求するには、次のコマンドを使用します。

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>切り替え状態 

論理的な削除が有効なキー コンテナーでキーを削除すると、切り替えが完了するまでに数秒かかる場合があります。 この切り替えの間、キーがアクティブな状態でも削除された状態でもなくなる場合があります。 

### <a name="using-soft-delete-with-key-vault-objects"></a>キー コンテナー オブジェクトで論理的な削除を使用する

キー コンテナーと同様に、削除されたキー、シークレット、証明書は、復旧または消去しない限り、最大で 90 日間、削除済みの状態で維持されます。

#### <a name="keys"></a>構成する

論理的に削除されたキーを復旧するには:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

論理的に削除されたキーを完全に削除 (消去) するには:

> [!IMPORTANT]
> キーを消去するとキーは永続的に削除され、復旧できなくなります。 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

**recover** アクションと **purge** アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。 ユーザーまたはサービス プリンシパルが **recover** アクションまたは **purge** アクションを実行するには、そのキーまたはシークレットに対する適切なアクセス許可が必要です。 既定では、'all' ショートカットを使用してすべてのアクセス許可を付与するときに、**purge** はキー コンテナーのアクセス ポリシーには追加されません。 **purge** のアクセス許可は明示的に付与する必要があります。 

#### <a name="set-a-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを設定する

次のコマンドでは、*ContosoVault* のキーに対する複数の操作 (**purge** を含む) を使用するために、user@contoso.com のアクセス許可を付与しています。

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> 論理的な削除を有効にした直後の既存のキー コンテナーの場合、**recover** や **purge** のアクセス許可がない場合があります。

#### <a name="secrets"></a>シークレット

キーと同様、シークレットも独自のコマンドで管理されます。

- SQLPassword という名前のシークレットを削除します。 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- キー コンテナー内の削除されたシークレットをすべて一覧表示します。 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- 削除された状態のシークレットを復旧します。 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- 削除された状態のシークレットを消去します。 

  > [!IMPORTANT]
  > シークレットを消去するとシークレットは永続的に削除され、復旧できなくなります。 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>論理的な削除で保護されているキー コンテナーを消去する

> [!IMPORTANT]
> キー コンテナーまたはそこに含まれているいずれかのオブジェクトを消去するとキー コンテナーは永続的に削除され、復旧できなくなります。

消去機能は、以前論理的に削除されたキー コンテナー オブジェクトまたはキー コンテナー全体を完全に削除するために使用されます。 前のセクションで示したように、論理的な削除機能が有効になったキー・コンテナーに格納されているオブジェクトは、次の複数の状態を経る可能性があります。

- **アクティブ**: 削除前。
- **論理的に削除済み**: 削除後。一覧表示およびアクティブ状態への復旧が可能。
- **完全に削除済み**: 消去後。復旧不可。

キー・コンテナーの場合も同様です。 論理的に削除されたキー コンテナーとその内容を完全に削除するには、キー・コンテナー自体を削除する必要があります。

### <a name="purging-a-key-vault"></a>キー・コンテナーを消去する

キー コンテナーを消去すると、そのすべてのコンテンツ (キー、シークレット、証明書など) が完全に削除されます。 論理的に削除されたキー コンテナーを消去するには、`az keyvault purge` コマンドを使用します。 サブスクリプションから削除されたキー コンテナーの場所を見つけるには、`az keyvault list-deleted` コマンドを使用します。

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>必要な消去のアクセス許可
- 削除されたキー コンテナーを消去するには、*Microsoft.KeyVault/locations/deletedVaults/purge/action* 操作に対する RBAC アクセス許可が必要になります。 
- 削除されたキー コンテナーを一覧表示するには、*Microsoft.KeyVault/deletedVaults/read* 操作に対する RBAC アクセス許可が必要になります。 
- 既定では、このアクセス許可を持つのはサブスクリプションの管理者のみです。 

### <a name="scheduled-purge"></a>スケジュールされた消去

削除されたキー コンテナー オブジェクトの一覧には、Key Vault による消去の実行スケジュールも表示されます。 *Scheduled Purge Date* は、何のアクションも実行しない場合に、そのキー コンテナー オブジェクトが永続的に削除されるタイミングを示します。 既定では、削除されたキー コンテナー オブジェクトのリテンション期間は 90 日です。

>[!IMPORTANT]
>*Scheduled Purge Date* フィールドでトリガーされた消去済みのコンテナー オブジェクトは永続的に削除されます。 これは復旧できません。

## <a name="enabling-purge-protection"></a>消去保護を有効にする

消去保護をオンにすると、削除状態のコンテナーまたはオブジェクトは、90 日間の保持期間が経過するまで消去できません。 このようなコンテナーまたはオブジェクトは回復することもできます。 この機能は、保持期間が経過するまでコンテナーまたはオブジェクトを完全には削除できないことの追加保証を与えます。

論理的な削除も有効にする場合にのみ、消去保護を有効にできます。 

コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) コマンドを使用します。

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) コマンドを使用します。

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>その他のリソース

- Key Vault の論理的な削除機能の概要については、「[Azure Key Vault の論理的な削除機能の概要](key-vault-ovw-soft-delete.md)」をご覧ください。
- Azure Key Vault の使用方法の概要については、「[Azure Key Vault とは](key-vault-overview.md)」をご覧ください。

