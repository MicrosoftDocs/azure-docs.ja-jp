---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631716"
---
アクセス トークンの場合と同様、Azure AD トークンが設定されていない場合は、TokenRequired イベントを処理するか、デリゲート プロトコルに tokenRequired メソッドを実装する必要があります。

イベント引数のプロパティを設定することで、イベントを同期的に処理できます。
