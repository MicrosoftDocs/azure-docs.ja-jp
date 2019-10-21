---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180059"
---
監視が作成された後、要求されたすべてのアンカーに対して `AnchorLocated` イベントが発生します。 このイベントは、アンカーが探知されたとき、またはアンカーを探知できなかった場合に発生します。 このような状況が発生した場合は、その理由が状態に示されます。 監視のすべてのアンカーが (探知されたかどうかを問わず) 処理された後、`LocateAnchorsCompleted` イベントが発生します。 識別子の上限は、Watcher 1 つあたり 35 個です。 
