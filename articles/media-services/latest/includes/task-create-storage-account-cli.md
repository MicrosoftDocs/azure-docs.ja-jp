---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a6349188a2c6b4da68009df93fbea5fa6eabacf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245025"
---
<!-- ### Create a storage account -->

Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services で使用されるストレージ アカウントの使用方法について詳しくは、「[ストレージ アカウント](../storage-account-concept.md)」をご覧ください。

1 つの **プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは **汎用 v1** (GPv1) アカウントをサポートします。 BLOB のみのアカウントを **プライマリ** として使用することはできません。 ストレージ アカウントについて詳しくは、「[Azure Storage アカウントの種類](../../../storage/common/storage-account-overview.md)」をご覧ください。 

この例では、General Purpose v2、Standard LRS アカウントを作成します。 ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 詳細については、[ストレージ アカウント](/cli/azure/storage/account)に関するページを参照してください。

Media Services アカウントに関連付けるストレージ アカウントは、以下のコマンドで作成します。 以下のスクリプトの `storageaccountforams` は、実際の値に置き換えてください。 `amsResourceGroup` は、前の手順で、リソース グループに指定した値と一致させる必要があります。 ストレージ アカウント名は 24 文字未満にする必要があります。

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
