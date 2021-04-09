---
title: 共有 VM イメージを使用して Azure PowerShell のスケール セットを作成する
description: Azure PowerShell を使用して Azure の仮想マシン スケール セットをデプロイするために使用する共有 VM イメージを作成する方法について説明します。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3ba826de47255143c8adefe4424448d0b80ba105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678340"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Azure PowerShell を使用した仮想マシン スケール セットの共有イメージの作成および使用

スケール セットを作成するときは、VM インスタンスのデプロイ時に使用するイメージを指定します。 共有イメージ ギャラリー サービスにより、組織全体でのカスタム イメージの共有が大幅に簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成などの構成のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、共有イメージを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全な Azure ロールベースのアクセス制御 (Azure RBAC) が可能です。 イメージのバージョン管理もできるため、Azure リージョンの別のセットに各イメージのバージョンをレプリケートできます。 ギャラリーは、マネージド イメージでのみ機能します。 

共有イメージ ギャラリー機能には、リソースの種類が複数あります。 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しい VM インスタンスの作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

この記事の例を実際に行うには、既存のマネージド イメージが必要です。 お持ちでない場合は、「[チュートリアル: Azure PowerShell を使用した仮想マシン スケール セットのカスタム イメージの作成および使用](tutorial-use-custom-image-powershell.md)」に従って作成してください。 マネージド イメージにデータ ディスクが含まれている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>次のステップ

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [イメージ バージョンから VM を作成する](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

共有イメージ ギャラリーの詳細については、[概要](../virtual-machines/shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](../virtual-machines/troubleshooting-shared-images.md)」を参照してください。