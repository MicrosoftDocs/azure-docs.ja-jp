---
title: Azure Key Vault とマネージド ID を使用して Azure Batch アカウントのカスタマー マネージド キーを構成する
description: キーを使用して Batch のデータを暗号化する方法について説明します。
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: a89d0182f6a659cee65ebc1de7d97d40418b4b20
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654890"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Azure Key Vault とマネージド ID を使用して Azure Batch アカウントのカスタマー マネージド キーを構成する

既定の Azure Batch では、プラットフォーム マネージド キーを使用して、証明書、ジョブとタスクのメタデータなど、Azure Batch サービスに格納されているすべての顧客データが暗号化されます。 必要に応じて、独自のキー (つまり、カスタマー マネージド キー) を使用して、Azure Batch に格納されているデータを暗号化できます。

提供するキーは、[Azure Key Vault](../key-vault/general/basic-concepts.md) で生成する必要があります。また、カスタマー マネージド キーを使用して構成する Batch アカウントは、[Azure マネージ ID](../active-directory/managed-identities-azure-resources/overview.md) で有効にする必要があります。

> [!IMPORTANT]
> 現在、Azure Batch でのカスタマー マネージド キーのサポートは、西ヨーロッパ、北ヨーロッパ、スイス北部、米国中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部 2、US Gov バージニア、US Gov アリゾナの各リージョンでパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用して Batch アカウントを作成する

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com/)で Batch アカウントを作成するときに、 **[詳細]** タブの ID の種類で **[システム割り当て済み]** を選択します。

![システム割り当て済み ID の種類を使用する新しい Batch アカウント](./media/batch-customer-managed-key/create-batch-account.png)

アカウントが作成された後、 **[プロパティ]** セクションの **[ID のプリンシパル ID]** フィールドで一意の GUID を確認できます。 **[ID の種類]** には `SystemAssigned` と表示されます。

![[ID のプリンシパル ID] フィールドの一意の GUID](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

新しい Batch アカウントを作成するときに、`--identity` パラメーターに対して `SystemAssigned` を指定します。

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

アカウントが作成されたら、システム割り当てマネージド ID がこのアカウントで有効になっていることを確認できます。 必ず `PrincipalId` を記録しておきます。この値は、Key Vault へのアクセス権をこの Batch アカウントに付与するときに必要になります。

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Batch アカウントで作成されたシステム割り当てマネージド ID は、キー コンテナーからカスタマー マネージド キーを取得するためにのみ使用されます。 この ID は Batch プールでは使用できません。

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスを構成する

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

Azure Batch のカスタマー マネージド キーを使用して Azure Key Vault のインスタンスを作成するときに、 **[論理削除]** と **[消去保護]** が両方とも有効になっていることを確認します。

![Key Vault の作成画面](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスにアクセス ポリシーを追加する

Azure portal で Key Vault を作成した後、 **[設定]** の **[アクセス ポリシー]** で、マネージド ID を使用して Batch アカウントのアクセスを追加します。 **[キーのアクセス許可]** で、 **[取得]** 、 **[キーを折り返す]** 、 **[キーの折り返しを解除]** を選択します。 

![アクセス ポリシーの追加](./media/batch-customer-managed-key/key-permissions.png)

**[プリンシパル]** の **[選択]** フィールドに、前に取得した `principalId` または Batch アカウントの名前を入力します。

![principalId を入力する](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault でキーを生成する

Azure portal の **[キー]** セクションで Key Vault インスタンスに移動し、 **[生成/インポート]** を選択します。 **[キーの種類]** で `RSA` を選択し、 **[RSA キー サイズ]** を最低でも `2048` にします。 キーの種類 `EC` は現在、Batch アカウントでカスタマー マネージド キーとしてサポートされていません。

![キーの作成](./media/batch-customer-managed-key/create-key.png)

キーが作成されたら、新しく作成されたキーと現在のバージョンをクリックし、 **[プロパティ]** セクションの **[キー識別子]** をコピーします。  **[許可された操作]** で、 **[キーを折り返す]** と **[キーの折り返しを解除]** がどちらもオンになっていることを確認します。

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Azure Batch アカウントでカスタマー マネージド キーを有効にする

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com/) で、Batch アカウントのページに移動します。 **[暗号化]** セクションで、 **[カスタマー マネージド キー]** を有効にします。 キー識別子を直接使用することも、キー コンテナーを選択して **[キー コンテナーとキーを選択します]** をクリックすることもできます。

![[暗号化] でカスタマー マネージド キーを有効にする](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

システム割り当てマネージド ID を使用して Batch アカウントを作成し、Key Vault へのアクセスを許可した後、`keyVaultProperties` パラメーターで `{Key Identifier}` URL を使用して Batch アカウントを更新します。 また、**encryption_key_source** を `Microsoft.KeyVault` に設定します。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>カスタマー マネージド キーのバージョンを更新する

キーの新しいバージョンを作成したら、その新しいバージョンを使用するように Batch アカウントを更新します。 次の手順に従います。

1. Azure portal で Batch アカウントに移動し、[暗号化] の設定を表示します。
2. 新しいキーのバージョンの URI を入力します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。
3. 変更を保存します。

Azure CLI を使用してバージョンを更新することもできます。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Batch の暗号化に別のキーを使用する

Batch の暗号化に使用されるキーを変更するには、次の手順のようにします。

1. Batch アカウントに移動し、[暗号化] の設定を表示します。
2. 新しいキーの URI を入力します。 キーコンテナーを選択して新しいキーを選択することもできます。
3. 変更を保存します。

また、Azure CLI を使用して別のキーを使用することもできます。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>よく寄せられる質問
  * **カスタマー マネージド キーは既存の Batch アカウントでサポートされますか?** いいえ。 カスタマー マネージド キーは、新しい Batch アカウントに対してのみサポートされています。
  * **2048 ビットを超える RSA キーのサイズを選択できますか?** はい。`3072` および `4096` ビットの RSA キーのサイズもサポートされています。
  * **カスタマー マネージド キーが失効した後、どのような操作を使用できますか?** Batch にカスタマー マネージド キーへのアクセス権がなくなった場合、許可される操作はアカウントの削除だけです。
  * **Key Vault のキーを誤って削除した場合、Batch アカウントへのアクセスを復元するにはどうすればよいですか?** 消去保護と論理削除が有効になっているため、既存のキーを復元することができます。 詳細については、[Azure Key Vault の復旧](../key-vault/general/soft-delete-cli.md#recovering-a-key-vault)に関するページを参照してください。
  * **カスタマー マネージド キーを無効にすることはできますか?** いつでも Batch アカウントの暗号化の種類を "Microsoft マネージド キー" に戻すことができます。 その後は、キーを自由に削除または変更できます。
  * **キーをローテーションするにはどうすればよいですか?** カスタマー マネージド キーは自動的にローテーションされません。 キーをローテーションするには、アカウントに関連付けられているキー識別子を更新します。
  * **アクセスを復元した後、Batch アカウントが再び動作するまでどれくらいかかりますか?** アクセスが復元されてから、アカウントに再びアクセスできるようになるまでに、最大で 10 分かかることがあります。
  * **Batch アカウントが使用できない間、リソースはどうなりますか?** カスタマー マネージド キーへの Batch アクセスが失われたときに実行されていたプールは、引き続き実行されます。 ただし、ノードは使用できない状態に遷移し、タスクの実行は停止されます (キューに登録されます)。 アクセスが復元されると、ノードは再び使用可能になり、タスクは再起動されます。
  * **この暗号化メカニズムは、Batch プール内の VM ディスクに適用されますか?** いいえ。 クラウド サービス構成プールでは、OS と一時ディスクに暗号化は適用されません。 仮想マシン構成プールの場合、OS ディスクと指定したすべてのデータ ディスクは、既定では Microsoft プラットフォーム マネージド キーで暗号化されます。 現時点では、これらのディスクに独自のキーを指定することはできません。 Batch プールの VM の一時ディスクを Microsoft プラットフォーム マネージド キーで暗号化するには、[仮想マシン構成](/rest/api/batchservice/pool/add#virtualmachineconfiguration)プールで [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) プロパティを有効にする必要があります。 機密性の高い環境では、一時ディスクの暗号化を有効にし、OS ディスクとデータ ディスクには機密データを格納しないようにすることをお勧めします。
  * **Batch アカウントのシステム割り当てマネージド ID は、コンピューティング ノードで使用できますか?** いいえ。 このマネージド ID は、現在、カスタマー マネージド キーの Azure キー コンテナーにアクセスするためにのみ使用されています。
  
