---
title: 仮想マシン イメージを作成し、ユーザー割り当てマネージド ID を使用して Azure Storage 内のファイルにアクセスする (プレビュー)
description: ユーザー割り当てマネージド ID を使って Azure Storage に格納されているファイルにアクセスできる仮想マシン イメージを、Azure Image Builder を使って作成します。
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9bcb7a94cdf1d5478db32a22ba6e612a90c53ed9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695381"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>イメージを作成し、ユーザー割り当てマネージド ID を使用して Azure Storage 内のファイルにアクセスする 

Azure Image Builder では、スクリプトの使用、または GitHub や Azure Storage などの複数の場所からのファイルのコピーがサポートされています。これらを使うには、それらが Azure Image Builder に外部からアクセスできる必要がありますが、SAS トークンを使って Azure Storage BLOB を保護できます。

この記事では、Azure VM Image Builder を使ってカスタマイズされたイメージを作成する方法を示します。ここで、サービスにより、イメージをカスタマイズするために[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使って Azure Storage 内のファイルにアクセスされるので、ファイルをパブリック アクセス可能にしたり、SAS トークンを設定したりする必要がありません。

次の例では、2 つのリソース グループを作成します。1 つはカスタム イメージ用に使われ、もう 1 つはスクリプト ファイルを含む Azure ストレージ アカウントをホストします。 これは、ビルド成果物またはイメージ ファイルが Image Builder の外部の別のストレージ アカウントに存在することがある、現実のシナリオをシミュレートするものです。 ユーザー割り当て ID を作成した後、それにスクリプト ファイルに対する読み取りアクセス許可を付与しますが、そのファイルへのパブリック アクセスは設定しません。 その後、シェル カスタマイザーを使ってそのスクリプトをストレージ アカウントからダウンロードし、実行します。


> [!IMPORTANT]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="register-the-features"></a>機能の登録
プレビュー中に Azure Image Builder を使用するには、新しい機能を登録する必要があります。

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

機能の登録の状態を確認します。

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

登録を確認します。


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

登録されていない場合、次のコマンドを実行します。

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="create-a-resource-group"></a>リソース グループを作成する

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

サブスクリプション ID の変数を作成します。 `az account show | grep id` を使用してこれを取得できます。

```console
subscriptionID=<Your subscription ID>
```

イメージとスクリプト ストレージの両方に対してリソース グループを作成します。

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

ユーザー割り当て ID を作成し、リソース グループにアクセス許可を設定します。

Image Builder は、指定された[ユーザー ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) を使用して、リソース グループにイメージを挿入します。 この例では、イメージの配布を実行するためのきめ細かなアクションを含む Azure ロール定義を作成します。 このロール定義はその後、ユーザー ID に割り当てられます。

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

ストレージを作成し、GitHub からサンプル スクリプトをコピーします。

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

イメージ リソース グループにリソースを作成するためのアクセス許可を Image Builder に付与します。 `--assignee` の値はユーザー ID です。

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>例を変更する

.json ファイルの例をダウンロードし、作成した変数を使用して構成します。

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>イメージの作成

Azure Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

ビルドが完了するまで待ちます。 これには約 15 分かかります。

## <a name="create-a-vm"></a>VM の作成

イメージから VM を作成します。 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM が作成された後、VM で SSH セッションを開始します。

```console
ssh aibuser@<publicIp>
```

SSH 接続が確立されるとすぐに、イメージが当日のメッセージでカスタマイズされたことがわかります。

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>クリーンアップ

終わったら、必要のないリソースを削除できます。

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>次のステップ

Azure Image Builder の使用に関して問題がある場合は、「[Troubleshooting (トラブルシューティング)](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)」をご覧ください。
