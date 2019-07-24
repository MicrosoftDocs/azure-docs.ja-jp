---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751932"
---
## <a name="setting-up-the-library"></a>ライブラリの設定

セッションで環境データを処理できるようにするには、Start() を呼び出します。

セッションによって発生したイベントを処理するには、セッションの `delegate` プロパティをビューのようなオブジェクトに設定します。 このオブジェクトでは SSCCloudSpatialAnchorSessionDelegate プロトコルを実装する必要があります。
