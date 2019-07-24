---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631666"
---
## <a name="update-properties"></a>プロパティを更新する

アンカーでプロパティを更新するには、`UpdateAnchorProperties()` メソッドを使用します。 2 つ以上のデバイスが同じアンカーのプロパティを同時に更新する場合は、オプティミスティック同時実行制御モデルを使用します。 これは、最初の書き込みが優先されることを意味します。  その他のすべての書き込みには、「同時実行」エラーが発生します。再試行する前に、プロパティの更新が必要になります。
