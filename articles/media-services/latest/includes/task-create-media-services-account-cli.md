---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602447"
---
<!--Create a media services account -->

新しい Media Services アカウントは、以下の Azure CLI コマンドで作成します。 次の値を置き換えることができます: `amsaccount` と `storageaccountforams` (ストレージ アカウントに指定した値に一致している必要があります)、`amsResourceGroup` (リソース グループに指定した値に一致している必要があります)。

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```