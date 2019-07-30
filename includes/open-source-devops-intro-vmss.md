---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: 3b639e0dcd852ddf34c4ce29292256b1285b2574
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181864"
---
[Azure Virtual Machine Scale Sets](../articles/virtual-machine-scale-sets/overview.md) は、負荷分散が行われる同一の VM のグループを構成するための Azure 機能です。 スケール セットに追加コストはなく、仮想マシンから構築されます。 ユーザーは、VM インスタンス、ロード バランサー、マネージド ディスク ストレージなど、基本的なコンピューティング リソースに対してのみ支払います。 スケール セットには、アプリケーションの実行とスケーリングを行うための管理レイヤーと自動化レイヤーがあります。 代わりに手動で個々 の VM を作成し管理できます。 ただし、スケール セットの使用には、2 つの主な利点があります。 それらは Azure に組み込まれ、アプリケーションのニーズを満たすように自動的に仮想マシンを拡大縮小します。