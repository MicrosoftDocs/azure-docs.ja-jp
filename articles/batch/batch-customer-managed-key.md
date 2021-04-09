---
title: Azure Key Vault とマネージド ID を使用して Azure Batch アカウントのカスタマー マネージド キーを構成する
description: カスタマー マネージド キーを使用して Batch データを暗号化する方法について説明します。
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385066"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Azure Key Vault とマネージド ID を使用して Azure Batch アカウントのカスタマー マネージド キーを構成する

既定の Azure Batch では、プラットフォーム マネージド キーを使用して、証明書、ジョブとタスクのメタデータなど、Azure Batch サービスに格納されているすべての顧客データが暗号化されます。 必要に応じて、独自のキー (つまり、カスタマー マネージド キー) を使用して、Azure Batch に格納されているデータを暗号化できます。

指定するキーは [Azure Key Vault](../key-vault/general/basic-concepts.md) で生成する必要があります。また、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) でアクセスする必要があります。

マネージド ID には、[*システム割り当て* と *ユーザー割り当て*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)の 2 種類があります。

システム割り当てマネージド ID を使用して Batch アカウントを作成するか、カスタマー マネージド キーへのアクセス権を持つ、ユーザー割り当てマネージド ID を個別に作成できます。 [比較表](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)を確認して違いを理解し、ソリューションに最適なオプションを検討してください。 たとえば、同じマネージド ID を使用して複数の Azure リソースにアクセスする場合は、ユーザー割り当てマネージド ID が必要になります。 それ以外の場合は、Batch アカウントに関連付けられているシステム割り当てマネージド ID で十分である可能性があります。 ユーザー割り当てマネージド ID を使用すると、[次の例](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)に示すように、Batch アカウントの作成時にカスタマー マネージド キーを適用するオプションも提供されます。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用して Batch アカウントを作成する

個別のユーザー割り当てマネージド ID が不要な場合は、Batch アカウントを作成するときに、システム割り当てマネージド ID を有効にすることができます。

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com/)で Batch アカウントを作成するときに、 **[詳細]** タブの ID の種類で **[システム割り当て済み]** を選択します。

![システム割り当て ID の種類を使用する新しい Batch アカウントのスクリーンショット。](./media/batch-customer-managed-key/create-batch-account.png)

アカウントが作成された後、 **[プロパティ]** セクションの **[ID のプリンシパル ID]** フィールドで一意の GUID を確認できます。 **[ID の種類]** には `System assigned` と表示されます。

![[ID のプリンシパル ID] フィールドに一意の GUID が表示されているスクリーンショット。](./media/batch-customer-managed-key/linked-batch-principal.png)

この Batch アカウントに Key Vault へのアクセスを許可するには、この値が必要になります。

### <a name="azure-cli"></a>Azure CLI

新しい Batch アカウントを作成するときに、`--identity` パラメーターに対して `SystemAssigned` を指定します。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

アカウントが作成されたら、システム割り当てマネージド ID がこのアカウントで有効になっていることを確認できます。 必ず `PrincipalId` を記録しておきます。この値は、Key Vault へのアクセス権をこの Batch アカウントに付与するときに必要になります。

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Batch アカウントで作成されたシステム割り当てマネージド ID は、キー コンテナーからカスタマー マネージド キーを取得するためにのみ使用されます。 この ID は Batch プールでは使用できません。 プールでユーザー割り当てマネージド ID を使用するには、「[Batch プールでマネージド ID を構成する](managed-identity-pools.md)」を参照してください。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

必要に応じて、カスタマー マネージド キーにアクセスするために使用できる、[ユーザー割り当てマネージド ID を作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)できます。

Key Vault にアクセスするには、この ID の **クライアント ID** 値が必要です。

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスを構成する

キーが生成される Azure Key Vault は、Batch アカウントと同じテナントに作成する必要があります。 同じリソース グループ内または同じサブスクリプション内に存在する必要はありません。

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

Azure Batch のカスタマー マネージド キーを使用して [Azure Key Vault のインスタンスを作成](../key-vault/general/quick-create-portal.md)するときに、 **[論理削除]** と **[消去保護]** が両方とも有効になっていることを確認します。

![Key Vault 作成画面のスクリーンショット。](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスにアクセス ポリシーを追加する

Azure portal で Key Vault を作成した後、 **[設定]** の **[アクセス ポリシー]** で、マネージド ID を使用して Batch アカウントのアクセスを追加します。 **[キーのアクセス許可]** で、 **[取得]** 、 **[キーを折り返す]** 、 **[キーの折り返しを解除]** を選択します。

![[アクセス ポリシーの追加] 画面が表示されたスクリーンショット。](./media/batch-customer-managed-key/key-permissions.png)

**[プリンシパル]** の下にある **[選択]** フィールドで、次のいずれかを入力します。

- システム割り当てマネージド ID の場合: 以前に取得した `principalId` または Batch アカウントの名前を入力します。
- ユーザー割り当てマネージド ID の場合: 以前に取得した **クライアント ID**、またはユーザー割り当てマネージド ID の名前を入力します。

![[プリンシパル] 画面のスクリーンショット。](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault でキーを生成する

Azure portal の **[キー]** セクションで Key Vault インスタンスに移動し、 **[生成/インポート]** を選択します。 **[キーの種類]** で `RSA` を選択し、 **[RSA キー サイズ]** を最低でも `2048` にします。 キーの種類 `EC` は現在、Batch アカウントでカスタマー マネージド キーとしてサポートされていません。

![キーの作成](./media/batch-customer-managed-key/create-key.png)

キーが作成されたら、新しく作成されたキーと現在のバージョンをクリックし、 **[プロパティ]** セクションの **[キー識別子]** をコピーします。  **[許可された操作]** で、 **[キーを折り返す]** と **[キーの折り返しを解除]** がどちらもオンになっていることを確認します。

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Batch アカウントでカスタマー マネージド キーを有効にする

上記の手順に従って、Batch アカウントでカスタマー マネージド キーを有効にできます。

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com/) で、Batch アカウントのページに移動します。 **[暗号化]** セクションで、 **[カスタマー マネージド キー]** を有効にします。 キー識別子を直接使用することも、キー コンテナーを選択して **[キー コンテナーとキーを選択します]** をクリックすることもできます。

![暗号化セクションと、カスタマー マネージド キーを有効にするオプションを示すスクリーンショット](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

システム割り当てマネージド ID を使用して Batch アカウントを作成し、Key Vault へのアクセスを許可した後、`keyVaultProperties` パラメーターで `{Key Identifier}` URL を使用して Batch アカウントを更新します。 また、**encryption_key_source** を `Microsoft.KeyVault` に設定します。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>ユーザー割り当てマネージド ID とカスタマー マネージド キーを使用して Batch アカウントを作成する

Batch 管理 .NET クライアントを使用して、ユーザー割り当てマネージド ID とカスタマー マネージド キーを持つ Batch アカウントを作成できます。

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>カスタマー マネージド キーのバージョンを更新する

キーの新しいバージョンを作成したら、その新しいバージョンを使用するように Batch アカウントを更新します。 次の手順に従います。

1. Azure portal で Batch アカウントに移動し、[暗号化] の設定を表示します。
2. 新しいキーのバージョンの URI を入力します。 または、Key Vault とキーを再び選択して、バージョンを更新してもかまいません。
3. 変更を保存します。

Azure CLI を使用してバージョンを更新することもできます。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Batch の暗号化に別のキーを使用する

Batch の暗号化に使用されるキーを変更するには、次の手順のようにします。

1. Batch アカウントに移動し、[暗号化] の設定を表示します。
2. 新しいキーの URI を入力します。 Key Vault を選択して新しいキーを選択することもできます。
3. 変更を保存します。

また、Azure CLI を使用して別のキーを使用することもできます。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- **カスタマー マネージド キーは既存の Batch アカウントでサポートされますか?** いいえ。 カスタマー マネージド キーは、新しい Batch アカウントに対してのみサポートされています。
- **2048 ビットを超える RSA キーのサイズを選択できますか?** はい。`3072` および `4096` ビットの RSA キーのサイズもサポートされています。
- **カスタマー マネージド キーが失効した後、どのような操作を使用できますか?** Batch にカスタマー マネージド キーへのアクセス権がなくなった場合、許可される操作はアカウントの削除だけです。
- **Key Vault のキーを誤って削除した場合、Batch アカウントへのアクセスを復元するにはどうすればよいですか?** 消去保護と論理削除が有効になっているため、既存のキーを復元することができます。 詳細については、[Azure Key Vault の復旧](../key-vault/general/key-vault-recovery.md)に関するページを参照してください。
- **カスタマー マネージド キーを無効にすることはできますか?** いつでも Batch アカウントの暗号化の種類を "Microsoft マネージド キー" に戻すことができます。 その後は、キーを自由に削除または変更できます。
- **キーをローテーションするにはどうすればよいですか?** カスタマー マネージド キーは自動的にローテーションされません。 キーをローテーションするには、アカウントに関連付けられているキー識別子を更新します。
- **アクセスを復元した後、Batch アカウントが再び動作するまでどれくらいかかりますか?** アクセスが復元されてから、アカウントに再びアクセスできるようになるまでに、最大で 10 分かかることがあります。
- **Batch アカウントが使用できない間、リソースはどうなりますか?** カスタマー マネージド キーへの Batch アクセスが失われたときに実行されていたプールは、引き続き実行されます。 ただし、ノードは使用できない状態に遷移し、タスクの実行は停止されます (キューに登録されます)。 アクセスが復元されると、ノードは再び使用可能になり、タスクは再起動されます。
- **この暗号化メカニズムは、Batch プール内の VM ディスクに適用されますか?** いいえ。 クラウド サービス構成プールでは、OS と一時ディスクに暗号化は適用されません。 仮想マシン構成プールの場合、OS ディスクと指定したすべてのデータ ディスクは、既定では Microsoft プラットフォーム マネージド キーで暗号化されます。 現時点では、これらのディスクに独自のキーを指定することはできません。 Batch プールの VM の一時ディスクを Microsoft プラットフォーム マネージド キーで暗号化するには、[仮想マシン構成](/rest/api/batchservice/pool/add#virtualmachineconfiguration)プールで [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) プロパティを有効にする必要があります。 機密性の高い環境では、一時ディスクの暗号化を有効にし、OS ディスクとデータ ディスクには機密データを格納しないようにすることをお勧めします。 詳細については、「[ディスク暗号化が有効になっているプールを作成する](./disk-encryption.md)」をご覧ください
- **Batch アカウントのシステム割り当てマネージド ID は、コンピューティング ノードで使用できますか?** いいえ。 システム割り当てマネージド ID は、現在、カスタマー マネージド キーの Azure Key Vault にアクセスするためにのみ使用されています。 計算ノードでユーザー割り当てマネージド ID を使用するには、「[Batch プールでマネージド ID を構成する](managed-identity-pools.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Azure Batch のセキュリティに関するベスト プラクティス](security-best-practices.md)に関する記事を参照してください。
- [Azure Key Vault](../key-vault/general/basic-concepts.md) の詳細を確認してください。
