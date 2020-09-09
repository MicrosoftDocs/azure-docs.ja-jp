---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006441"
---
## <a name="setting-up-the-library"></a>ライブラリの設定

セッションにおいて環境データを処理できるようにするには、`Start()` を呼び出します。

セッションによって発生したイベントを処理するには、セッションの `delegate` プロパティをビューのようなオブジェクトに設定します。 このオブジェクトは `SSCCloudSpatialAnchorSessionDelegate` プロトコルを実装する必要があります。
