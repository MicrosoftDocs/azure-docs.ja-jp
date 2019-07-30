---
title: Azure Image Builder で Linux VM を作成する (プレビュー)
description: Azure Image Builder で Linux VM を作成します。
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667521"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>更新:Azure Image Builder で Linux VM を作成する

この記事では、Azure Image Builder と Azure CLI を使用して、カスタマイズされた Linux イメージを作成する方法について説明します。 この記事の例では、イメージのカスタマイズに 3 つの異なる[カスタマイザー](image-builder-json.md#properties-customize)を使用します。

- シェル (ScriptUri) - [シェル スクリプト](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)をダウンロードおよび実行します。
- シェル (インライン) - 特定のコマンドを実行します。 この例では、インライン コマンドには、ディレクトリの作成や OS の更新が含まれます。
- ファイル - [ファイルを GitHub から](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) VM 上のディレクトリにコピーします。


サンプルの .json テンプレートを使用して、イメージを構成します。 使用する .json ファイルは、[helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json) です。 

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

## <a name="setup-example-variables"></a>サンプル変数を設定する

いくつかの情報を繰り返し使用するので、その情報を格納するいくつかの変数を作成します。


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

サブスクリプション ID の変数を作成します。 `az account show | grep id` を使用してこれを取得できます。

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>リソース グループを作成します。
これは、イメージ構成テンプレート成果物およびイメージを保存するために使用されます。

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>リソース グループのアクセス許可を設定する
リソース グループにイメージを作成するための "共同作成者" アクセス許可を Image Builder に付与します。 適切なアクセス許可がない場合、イメージのビルドは失敗します。 

`--assignee` 値は、Image Builder サービスのアプリ登録 ID です。 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>サンプル テンプレートをダウンロードする

パラメーター化されたサンプル イメージ構成テンプレートが作成されており、使用できます。 サンプル .json ファイルをダウンロードし、事前に設定した変数を使用してこれを構成します。

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

必要に応じて、このサンプル .json を変更できます。 たとえば、ビルドの実行時間を長くするために、`buildTimeoutInMinutes` の値を増やすことができます。 Cloud Shell で `vi` を使用してファイルを編集できます。

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> ソース イメージの場合、常に[バージョンを指定](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)する必要があり、`latest` は使用できません。
>
> イメージの配布先となるリソース グループを追加または変更する場合、[リソース グループに対してアクセス許可が設定されていること](#set-permissions-on-the-resource-group)を確認する必要があります。


## <a name="submit-the-image-configuration"></a>イメージ構成を送信する
VM Image Builder サービスにイメージ構成を送信します。

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

正常に完了した場合、成功メッセージが返され、$imageResourceGroup に Image Builder 構成テンプレート成果物が作成されます。 [非表示の型の表示] を有効にした場合、ポータルにリソース グループが表示されます。

またバックグラウンドで、Image Builder により、サブスクリプションにステージング リソース グループが作成されます。 Image Builder は、イメージのビルドにこのステージング リソース グループを使用します。 リソース グループの名前の形式は、`IT_<DestinationResourceGroup>_<TemplateName>` です。

> [!IMPORTANT]
> ステージング リソース グループは直接削除しないでください。 イメージ テンプレート成果物を削除すると、ステージング リソース グループが自動的に削除されます。 詳細については、この記事の最後にある「[クリーンアップ](#clean-up)」セクションをご覧ください。

イメージ構成テンプレートの送信中にサービスによって障害が報告された場合、[トラブルシューティング](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting)の手順をご覧ください。 また、ビルドの送信を再試行する前に、テンプレートを削除する必要があります。 テンプレートを削除するには:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>イメージのビルドを開始する

イメージのビルドを開始します。


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

ビルドが完了するまで待機します。この例では、10 分から 15 分かかることがあります。

エラーが発生した場合は、これらの[トラブルシューティング](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)の手順を確認してください。


## <a name="create-the-vm"></a>VM の作成

ビルドしたイメージを使用して VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM の作成の出力から IP アドレスを取得し、それを使用して VM に SSH 接続します。

```azurecli-interactive
ssh azureuser@<pubIp>
```

SSH 接続が確立されるとすぐに、イメージが当日のメッセージでカスタマイズされたことがわかります。

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

終了したら `exit` と入力して、SSH 接続を閉じます。

## <a name="check-the-source"></a>ソースの確認

Image Builder テンプレートの [プロパティ] には、ソース イメージ、実行しているカスタマイズ スクリプト、および配布されている場所が表示されます。

```azurecli-interactive
cat helloImageTemplateLinux.json
```

この .json ファイルの詳細については、[Image Builder テンプレートのリファレンス](image-builder-json.md)に関するページを参照してください。

## <a name="clean-up"></a>クリーンアップ

作業が終わったら、リソースを削除できます。

Image Builder テンプレートを削除します。

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

イメージ リソース グループを削除します。

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>次の手順

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](image-builder-json.md)に関するページを参照してください。
