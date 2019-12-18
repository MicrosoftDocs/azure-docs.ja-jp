---
title: Azure Image Builder を使用して既存のイメージ バージョンから新しい VM イメージ バージョンを作成する (プレビュー)
description: Azure Image Builder を使用して、既存のイメージ バージョンから新しい VM イメージ バージョンを作成します。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: d226a7b31dc9f8cf219c6d0d0f886fb5b21741a6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976334"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>プレビュー:Azure Image Builder を使用して既存のイメージ バージョンから新しい VM イメージ バージョンを作成する

この記事では、[共有イメージ ギャラリー](shared-image-galleries.md)で既存のイメージ バージョンを取得し、それを更新し、新しいイメージ バージョンとしてギャラリーに公開する方法について説明します。

サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) です。 


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


## <a name="set-variables-and-permissions"></a>変数とアクセス許可の設定

[イメージの作成と共有イメージ ギャラリーへの配布](image-builder-gallery.md)に関するページを使用して共有イメージ ギャラリーを作成した場合は、必要とする変数のいくつかが既に作成されています。 それ以外の場合は、この例で使用するいくつかの変数を設定してください。

プレビューの Image Builder では、ソース マネージド イメージと同じリソース グループ内にのみ、カスタム イメージを作成できます。 ソース マネージド イメージと同じリソース グループになるように、この例のリソース グループ名を更新します。


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

サブスクリプション ID の変数を作成します。 `az account show | grep id` を使用してこれを取得できます。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

更新するイメージ バージョンを取得します。

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


既に自身の共有イメージ ギャラリーがあり、以前の例に従わなかった場合、Image Builder がリソース グループにアクセスできるようにアクセス許可を割り当てる必要があります。これによりギャラリーにアクセスできます。


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>helloImage の例の変更
.json ファイル [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) と [Image Builder テンプレートのリファレンス](image-builder-json.md)を開いて、使用しようとしている例を確認できます。 


.json の例をダウンロードし、変数を使用して構成します。 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>イメージの作成

VM Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

イメージがビルドされてレプリケートされるまで待ってから、次の手順に進みます。


## <a name="create-the-vm"></a>VM の作成

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM のパブリック IP アドレスを使用して VM への SSH 接続を作成します。

```azurecli-interactive
ssh azureuser@<pubIp>
```

SSH 接続が確立されるとすぐに、イメージが「当日のメッセージ」でカスタマイズされたことがわかります。

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit` と入力して SSH 接続を閉じます。

ギャラリーで使用できるようになったイメージ バージョンを一覧表示することもできます。

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>次の手順

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。