---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554062"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| クラウド サービスあたりの[仮想マシン数](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)<sup>1</sup> |50 |50 |
| クラウド サービスあたりの入力エンドポイント数 <sup>2</sup> |150 |150 |

<sup>1</sup>Azure Resource Manager ではなく、クラシック デプロイ モデルを使用して作成された仮想マシンは自動的にクラウド サービスに格納されます。 より多くの仮想マシンをそのクラウド サービスに追加することで、負荷分散と可用性を実現できます。 

<sup>2</sup>入力エンドポイントは、仮想マシンのクラウド サービスの外部から仮想マシンへの通信を可能にします。 同じクラウド サービスまたは仮想ネットワーク内の仮想マシンは自動的に相互に通信できます。 詳細については、[仮想マシンに対してエンドポイントを設定する方法](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。 
