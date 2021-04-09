---
title: Linux VM の Azure Image Builder を使用して既存の Azure VNET へのアクセスを許可する (プレビュー)
description: Azure Image Builder で Linux VM イメージを作成して、既存の Azure VNET へのアクセスを許可します
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565310"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Linux VM の Azure Image Builder を使用して既存の Azure VNET へのアクセスを許可する

この記事では、Azure Image Builder を使用して、VNET 上の既存のリソースにアクセスできる基本的なカスタマイズ Linux イメージを作成する方法について説明します。 作成したビルド VM は、サブスクリプションに指定した新規または既存の VNET にデプロイされます。 既存の Azure VNET を使用する場合、Azure Image Builder サービスでは、パブリック ネットワーク接続は必要ありません。

> [!IMPORTANT]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>機能の登録

まず、Azure Image Builder サービスに登録する必要があります。 登録すると、ステージング リソース グループを作成、管理、削除するためのアクセス許可がこのサービスに付与されます。 このサービスには、イメージのビルドに必要なグループにリソースを追加する権限もあります。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>変数とアクセス許可の設定 

一部の情報は、繰り返し使用します。 そのような情報を格納する変数をいくつか作成します。

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

リソース グループを作成します。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>ネットワークを構成する

既存の VNET\Subnet\NSG がない場合は、次のスクリプトを使用して作成します。

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>ネットワーク セキュリティ グループの規則の追加

この規則によって、Azure Image Builder のロード バランサーからプロキシ VM への接続が許可されます。 ポート 60001 は Linux OS 用、ポート 60000 は Windows OS 用です。 プロキシ VM は、Linux OS の場合はポート 22、Windows OS の場合はポート 5986 を使用して、ビルド VM に接続されます。

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>サブネットでのプライベート サービス ポリシーの無効化

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Image Builder ネットワークの詳細については、「[Azure Image Builder サービス ネットワークのオプション](image-builder-networking.md)」を参照してください。

## <a name="modify-the-example-template-and-create-role"></a>サンプル テンプレートを変更し、ロールを作成する

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>リソース グループのアクセス許可を設定する

Image Builder は、指定された[ユーザー ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) を使用して、Azure Shared Image Gallery (SIG) にイメージを挿入します。 この例では、イメージの SIG への配布を実行するための粒度の細かいアクションを持つ Azure ロール定義を作成します。 このロール定義はその後、ユーザー ID に割り当てられます。

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Image Builder の粒度を下げたり特権を上げたりするのではなく、2 つのロールを作成することができます。 1 つはイメージを作成するアクセス許可をビルダーに付与するもので、もう 1 つはビルド VM とロードバランサーを VNET に接続できるようにするものです。

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

アクセス許可の詳細については、「[Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-cli.md)」または「[PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-powershell.md)」をご覧ください。

## <a name="create-the-image"></a>イメージの作成

Azure Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

イメージを作成し、両方のリージョンにレプリケートするまでにしばらくかかることがあります。 この部分が終了するまで待ってから、VM の作成に進みます。


## <a name="create-the-vm"></a>VM の作成

Azure Image Builder で作成されたイメージ バージョンから VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM に SSH 接続します。

```bash
ssh aibuser@<publicIpAddress>
```

SSH 接続が確立されるとすぐに、イメージが *当日のメッセージ* でカスタマイズされたことがわかります。

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ここで同じイメージの新しいバージョンを作成するように、イメージ バージョンを再カスタマイズしてみる場合は、次の手順をスキップして、[Azure Image Builder を使用した別のイメージ バージョンの作成](image-builder-gallery-update-image-version.md)に関するページに進みます。


次では、作成されたイメージが他のすべてのリソース ファイルと共に削除されます。 リソースを削除する前に、このデプロイを終了していることを確認します。

イメージ ギャラリー リソースを削除する場合、それらの作成に使用したイメージ定義を削除する前に、すべてのイメージ バージョンを削除する必要があります。 ギャラリーを削除するには、最初にギャラリー内のすべてのイメージ定義を削除していることが必要です。

Image Builder テンプレートを削除します。

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

アクセス許可の割り当て、ロール、および ID を削除します。
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

リソース グループを削除します。

```azurecli-interactive
az group delete -n $imageResourceGroup
```

このクイックスタートで VNET を作成していた場合は、その VNET も削除できます (使用されなくなった場合)。

## <a name="next-steps"></a>次のステップ

詳細については、[Azure 共有イメージ ギャラリー](../shared-image-galleries.md)に関するページを参照してください。
