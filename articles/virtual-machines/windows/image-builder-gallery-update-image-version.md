---
title: Azure Image Builder を使用して、既存のイメージ バージョンから新しいイメージ バージョンを作成する (プレビュー)
description: Azure Image Builder を使用して、既存のイメージ バージョンから新しい VM イメージバージョンを作成します。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 160de4521f4035ba3abd01137955cafc27071a05
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976096"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>プレビュー:Azure Image Builder を使用して、既存のイメージ バージョンから新しい VM イメージバージョンを作成

この記事では、[共有イメージ ギャラリー](shared-image-galleries.md)で既存のイメージ バージョンを取得し、それを更新し、新しいイメージ バージョンとしてギャラリーに公開する方法について説明します。

サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json) です。 

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
az provider show -n Microsoft.Compute | grep registrationState
```

登録されていない場合、次のコマンドを実行します。

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>変数とアクセス許可を設定する

[イメージの作成と共有イメージ ギャラリーへの配布](image-builder-gallery.md)に関するページの手順を使用して共有イメージ ギャラリーを作成した場合、必要な変数は既に作成されています。 それ以外の場合は、この例で使用するいくつかの変数を設定してください。

プレビューの Image Builder では、ソース マネージド イメージと同じリソース グループ内にのみ、カスタム イメージを作成できます。 ソース マネージド イメージと同じリソース グループになるように、この例のリソース グループ名を更新します。

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

サブスクリプション ID の変数を作成します。 `az account show | grep id` を使用してこれを取得できます。

```azurecli-interactive
subscriptionID=<Subscription ID>
```

更新するイメージ バージョンを取得します。

```azurecli-interactive
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
.json ファイル [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) と [Image Builder テンプレートのリファレンス](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を開いて、使用しようとしている例を確認できます。 


.json の例をダウンロードし、変数を使用して構成します。 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>イメージの作成

VM Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

イメージのビルドを開始します。

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

イメージがビルドされてレプリケートされるまで待ってから、次の手順に進みます。


## <a name="create-the-vm"></a>VM の作成

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>カスタマイズの確認
VM を作成したときに設定したユーザー名とパスワードを使用して、VM へのリモート デスクトップ接続を作成します。 VM 内で、コマンド プロンプトを開き、次のように入力します。

```console
dir c:\
```

2 つのディレクトリが表示されます。
- `buildActions` は、最初のイメージ バージョンで作成しました。
- `buildActions2` は、2 つ目のイメージ バージョンを作成するための最初のイメージ バージョンの更新の一部として作成されました。


## <a name="next-steps"></a>次の手順

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。