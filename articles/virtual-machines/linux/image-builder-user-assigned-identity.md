---
title: 仮想マシン イメージを作成し、ユーザー割り当てマネージド ID を使用して Azure Storage 内のファイルにアクセスする (プレビュー)
description: ユーザー割り当てマネージド ID を使って Azure Storage に格納されているファイルにアクセスできる仮想マシン イメージを、Azure Image Builder を使って作成します。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: b6347765f8d2e21c352834dc8d28b65c28f99758
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671446"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>イメージを作成し、ユーザー割り当てマネージド ID を使用して Azure Storage 内のファイルにアクセスする 

Azure Image Builder では、スクリプトの使用、または GitHub や Azure Storage などの複数の場所からのファイルのコピーがサポートされています。これらを使うには、それらが Azure Image Builder に外部からアクセスできる必要がありますが、SAS トークンを使って Azure Storage BLOB を保護できます。

この記事で説明する、Azure VM Image Builder を使ってカスタマイズされたイメージを作成する方法では、サービスは[ユーザー割り当てマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) を使ってイメージのカスタマイズのために Azure Storage 内のファイルにアクセスするので、ファイルをパブリック アクセス可能にしたり、SAS トークンを設定したりする必要はありません。

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

az provider show -n Microsoft.Storage | grep registrationState
```

登録されていない場合、次のコマンドを実行します。

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>リソース グループの作成

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。


```azurecli-interactive
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

サブスクリプション ID の変数を作成します。 `az account show | grep id` を使ってこれを取得できます。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

イメージとスクリプト ストレージの両方に対してリソース グループを作成します。

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
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
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



イメージ リソース グループにリソースを作成するためのアクセス許可を Image Builder に付与します。 `--assignee` の値は、Image Builder サービスのアプリ登録 ID です。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ID を作成し、スクリプト ストレージ アカウントに対するアクセス許可を割り当てます。 詳しくは、「[ユーザー割り当てマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)」をご覧ください。

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>例を変更する

.json ファイルの例をダウンロードし、作成した変数を使用して構成します。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
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

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM が作成された後、VM で SSH セッションを開始します。

```azurecli-interactive
ssh aibuser@<publicIp>
```

SSH 接続が確立されるとすぐに、イメージが当日のメッセージでカスタマイズされたことがわかります。

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>クリーンアップ

終わったら、必要のないリソースを削除できます。

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>次の手順

Azure Image Builder の使用に関して問題がある場合は、「[Troubleshooting (トラブルシューティング)](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)」をご覧ください。