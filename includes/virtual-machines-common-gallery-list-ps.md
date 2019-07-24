---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a55653c0f23be594fe65e7a322c11edc37ee1ce6
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984994"
---
## <a name="shared-image-management"></a>共有イメージの管理 

ここでは、一般的な管理タスクの例を示すと共に、PowerShell を使用してそれらを遂行する方法について説明します。

すべてのギャラリーを名前別に一覧表示します。

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

すべてのイメージ定義を名前別に一覧表示します。

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

すべてのイメージ バージョンを名前別に一覧表示します。

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

イメージ バージョンを削除します。 この例では、*1.0.0* という名前のイメージ バージョンを削除しています。

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





