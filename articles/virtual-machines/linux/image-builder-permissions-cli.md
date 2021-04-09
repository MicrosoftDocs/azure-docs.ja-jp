---
title: Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する
description: Azure CLI を使用してアクセス許可と特権を含む Azure VM Image Builder サービスの要件を構成する
author: cynthn
ms.author: danis
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: eb4fe102407bf519c9253ac7da39178ad8cacb0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607536"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する

(AIB) に登録すると、ステージング リソース グループ (IT_*) を作成、管理、削除するためのアクセス許可が AIB サービスに付与され、イメージのビルドに必要なリソースを追加する権限が与えられます。 これは、登録が成功したときに、AIB サービス プリンシパル名 (SPN) がサブスクリプションで使用可能になることによって行われます。

Azure VM Image Builder で、マネージド イメージまたは Shared Image Gallery にイメージを配布できるようにするには、イメージの読み取りと書き込みのアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Azure Storage にアクセスする場合は、プライベートまたはパブリック コンテナーを読み取るためのアクセス許可が必要です。

イメージを構築する前にアクセス許可と特権を設定する必要があります。 以下のセクションでは、Azure CLI を使用して考えられるシナリオを構成する方法について詳しく説明します。

> [!IMPORTANT]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>機能の登録

まず、Azure Image Builder サービスに登録する必要があります。 登録すると、ステージング リソース グループを作成、管理、削除するためのアクセス許可がこのサービスに付与されます。 このサービスには、イメージのビルドに必要なグループにリソースを追加する権限もあります。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure ユーザー割り当てマネージド ID を作成する

Azure Image Builder では、[Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) を作成する必要があります。 Azure Image Builder では、ユーザー割り当てマネージド ID を使用して、イメージの読み取り、イメージの書き込み、および Azure ストレージ アカウントへのアクセスを行います。 サブスクリプションで特定のアクションを実行できるアクセス許可をその ID に付与します。

> [!NOTE]
> 以前に、Azure Image Builder では、Azure Image Builder のサービス プリンシパル名 (SPN) を使用して、イメージ リソース グループへのアクセス許可を付与していました。 SPN の使用は非推奨になる予定です。 代わりにユーザー割り当てマネージド ID を使用してください。

次の例は、Azure ユーザー割り当てマネージド ID を作成する方法を示しています。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Resource group\> | ユーザー割り当てマネージド ID を作成するリソース グループ。 |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Azure ユーザー割り当て ID の詳細については、ID の作成方法に関する [Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) に関するドキュメントを参照してください。

## <a name="allow-image-builder-to-distribute-images"></a>Image Builder にイメージの配布を許可する

Azure Image Builder を使用してイメージ (マネージド イメージまたは Shared Image Gallery) を配布するには、これらのリソース グループにイメージを挿入することを Azure Image Builder サービスに許可する必要があります。 必要なアクセス許可を付与するには、ユーザー割り当てマネージド ID を作成し、イメージが構築されているリソース グループに対するアクセス許可を付与する必要があります。 Azure Image Builder には、サブスクリプション内の他のリソース グループのリソースにアクセスするアクセス許可が **ありません**。 ビルドが失敗しないようにするには、アクセスを許可する明示的なアクションを実行する必要があります。

イメージを配布するために、リソース グループに対する共同作成者アクセス許可をユーザー割り当てマネージド ID に付与する必要はありません。 ただし、ユーザー割り当てマネージド ID には、配布リソース グループに次の Azure `Actions` アクセス許可が必要です。

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Shared Image Gallery に配布する場合は、以下も必要です。

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>既存のイメージをカスタマイズするアクセス許可

Azure Image Builder を使用してソース カスタム イメージ (マネージド イメージまたは Shared Image Gallery) からイメージをビルドするには、これらのリソース グループへのイメージの読み取りを Azure Image Builder サービスに許可する必要があります。 必要なアクセス許可を付与するには、ユーザー割り当てマネージド ID を作成し、イメージが配置されているリソース グループに対するアクセス許可を付与する必要があります。

既存のカスタム イメージから構築する:

```Actions
Microsoft.Compute/galleries/read
```

既存の Shared Image Gallery バージョンから構築する:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>VNET 上のイメージをカスタマイズするためのアクセス許可

Azure Image Builder には、サブスクリプションで既存の VNET をデプロイして使用する機能があるため、接続されているリソースへのカスタマイズ アクセスが可能になります。

VM を既存の VNET にデプロイするために、リソース グループに対する共同作成者アクセス許可をユーザー割り当てマネージド ID に付与する必要はありません。 ただし、ユーザー割り当てマネージド ID には、VNET リソース グループに次の Azure `Actions` アクセス許可が必要です。

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure ロールの定義を作成する

次の例では、前のセクションで説明したアクションから Azure ロール定義を作成します。 これらの例は、リソース グループ レベルで適用されます。 例が実際の要件に合った細かさかどうかを評価し、テストしてください。 シナリオによっては、特定の Shared Image Gallery に合わせて調整する必要がある場合があります。

イメージのアクションでは、読み取りと書き込みが許可されています。 環境に適したものを判断します。 たとえば、リソース グループ *example-rg-1* からイメージを読み取り、リソース グループ *example-rg-2* にイメージを書き込むことを Azure Image Builder に許可するロールを作成します。

### <a name="custom-image-azure-role-example"></a>カスタム イメージの Azure ロールの例

次の例では、ソース カスタム イメージを使用および配布する Azure ロールを作成します。 次に、Azure Image Builder のユーザー割り当てマネージド ID にカスタム ロールを付与します。

この例の値の置換を簡単にするために、最初に次の変数を設定します。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure サブスクリプション ID |
| \<Resource group\> | カスタム イメージのリソース グループ |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>既存の VNET Azure ロールの例

次の例では、既存の VNET イメージを使用および配布する Azure ロールを作成します。 次に、Azure Image Builder のユーザー割り当てマネージド ID にカスタム ロールを付与します。

この例の値の置換を簡単にするために、最初に次の変数を設定します。 プレースホルダーの設定を置き換えて、変数を設定してください。

| 設定 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure サブスクリプション ID |
| \<Resource group\> | VNET リソース グループ |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Azure Storage のアクセスにマネージド ID を使用する

Azure Storage でシームレスに認証を行い、プライベート コンテナーを使用する場合、Azure Image Builder と Azure にユーザー割り当てマネージド ID が必要です。 Azure Image Builder では、ID を使用して Azure Storage で認証します。

> [!NOTE]
> Azure Image Builder では、イメージ テンプレートの送信時にのみ ID を使用します。 イメージのビルド中に、ビルド VM からこの ID にアクセスすることはできません。

Azure CLI を使用して、ユーザー割り当てマネージド ID を作成します。

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

Image Builder テンプレートでは、ユーザー割り当てマネージド ID を指定する必要があります。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

次のプレースホルダー設定を置き換えます。

| 設定 | Description |
|---------|-------------|
| \<Region\> | テンプレートのリージョン |
| \<Resource group\> | Resource group |
| \<Storage account container\> | ストレージ アカウント コンテナーの名前 |
| \<Subscription ID\> | Azure サブスクリプション |

ユーザー割り当てマネージド ID の使用方法の詳細については、「[Create a Custom Image that will use an Azure User-Assigned Managed Identity to seemlessly access files Azure Storage](./image-builder-user-assigned-identity.md)」 (Azure ユーザー割り当てマネージド ID を使用してシームレスにファイル Azure Storage にアクセスするカスタム イメージを作成する) を参照してください。 このクイックスタートでは、ユーザー割り当てマネージド ID を作成し、ストレージ アカウントにアクセスするように構成する方法について説明しています。

## <a name="next-steps"></a>次の手順

詳細については、[Azure Image Builder の概要](../image-builder-overview.md)に関する記事を参照してください。