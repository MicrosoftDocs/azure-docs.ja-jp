---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: feec6a695ad867d26d32904d020648b029f9da35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181265"
---
## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

最初に、Media Services アカウントを作成する必要があります。 このセクションでは、Azure CLI を使用してアカウントを作成するために必要な作業について説明します。

### <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループを作成するには、次のコマンドを使用します。 Azure リソース グループは、Azure Media Services のアカウントや関連するストレージ アカウントなど、各種リソースのデプロイと管理の対象となる論理上のコンテナーです。

`amsResourceGroup` を実際の値に置き換えることができます。

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services で使用されるストレージ アカウントの使用方法について詳しくは、「[ストレージ アカウント](../articles/media-services/latest/storage-account-concept.md)」をご覧ください。

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを**プライマリ**として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../articles/storage/common/storage-account-options.md)」をご覧ください。 

この例では、General Purpose v2、Standard LRS アカウントを作成します。 ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 詳細については、[ストレージ アカウント](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)に関するページを参照してください。
 
Media Services アカウントに関連付けるストレージ アカウントは、以下のコマンドで作成します。 以下のスクリプトの `storageaccountforams` は、実際の値に置き換えてください。 `amsResourceGroup` は、前の手順で、リソース グループに指定した値と一致させる必要があります。 ストレージ アカウント名は 24 文字未満にする必要があります。

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 `amsaccount` と `storageaccountforams` には、実際のストレージ アカウントに割り当てた値を、`amsResourceGroup` には、リソース グループに割り当てた値を指定してください。

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
