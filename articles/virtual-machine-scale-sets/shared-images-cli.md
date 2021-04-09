---
title: 共有 VM イメージを使用して Azure CLI のスケール セットを作成する
description: Azure CLI を使用して Azure の仮想マシン スケール セットをデプロイするために使用する共有 VM イメージを作成する方法について説明します。
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 418258c498f768697aefebd5df0cc64b7255076f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676080"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用した仮想マシン スケール セットの共有イメージの作成および使用

スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 [共有イメージ ギャラリー](../virtual-machines/shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、イメージを他のユーザーと共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>次のステップ

[VM](../virtual-machines/image-version-vm-cli.md)、つまり[マネージド イメージ](../virtual-machines/image-version-managed-image-cli.md)からイメージ バージョンを作成します。

共有イメージ ギャラリーの詳細については、[概要](../virtual-machines/shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](../virtual-machines/troubleshooting-shared-images.md)」を参照してください。