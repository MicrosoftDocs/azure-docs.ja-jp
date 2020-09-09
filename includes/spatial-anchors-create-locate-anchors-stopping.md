---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006470"
---
## <a name="pause-reset-or-stop-the-session"></a>セッションの一時停止、リセット、停止

セッションを一時的に停止するために、`Stop()` を呼び出すことができます。 そうすることで、`ProcessFrame()` を呼び出した場合でも、あらゆるウォッチャーや環境処理を停止できます。 次に `Start()` を呼び出して、処理を再開できます。 再開するとき、既にセッションでキャプチャされた環境データは保持されます。
