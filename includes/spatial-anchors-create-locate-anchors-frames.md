---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907725"
---
## <a name="provide-frames-to-the-session"></a>セッションにフレームを提供する

空間アンカー セッションは、ユーザーの周りに空白をマップすることによって機能します。 そうすることで、アンカーの場所を決定しやすくなります。 モバイル プラットフォーム (iOS と Android) には、プラットフォームの AR ライブラリからフレームを取得するためにカメラ フィードへのネイティブ呼び出しが必要です。 これに対し、Hololens は環境を継続的にスキャンするため、Mobile の場合と異なり特定の呼び出しは必要ありません。