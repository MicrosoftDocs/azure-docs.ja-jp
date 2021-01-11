---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 994a7726adec07f2f6533d460d05469a0f3c7bf3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102811"
---
| リソース | 制限 |
| --- | --- |
| クラウド サービスあたりの仮想マシン数 <sup>1</sup> |50 |
| クラウド サービスあたりの入力エンドポイント数 <sup>2</sup> |150 |

<sup>1</sup> Azure Resource Manager ではなく、クラシック デプロイ モデルを使用して作成された仮想マシンは自動的にクラウド サービスに格納されます。 より多くの仮想マシンをそのクラウド サービスに追加することで、負荷分散と可用性を実現できます。 

<sup>2</sup> 入力エンドポイントは、仮想マシンのクラウド サービスの外部から仮想マシンへの通信を可能にします。 同じクラウド サービスまたは仮想ネットワーク内の仮想マシンは自動的に相互に通信できます。  
