---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214738"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>センサーのフィンガープリント プロバイダーを構成する

まず、センサーのフィンガープリント プロバイダーを作成して構成します。 センサーのフィンガープリント プロバイダーは、デバイス上にあるプラットフォーム固有のセンサーの読み取りを処理し、それらの測定値を、クラウド空間アンカー セッションによって使用される共通表現へと変換します。

> [!IMPORTANT]
> 有効にしているセンサーがお使いのプラットフォームで使用可能かどうか、[こちらを確認](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability)してください。