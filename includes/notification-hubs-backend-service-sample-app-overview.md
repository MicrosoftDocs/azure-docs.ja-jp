---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095466"
---
これにより、作成したバックエンド サービスを使用して、通知ハブから登録と登録解除を行うことができます。 

アクションが指定され、アプリがフォアグラウンドにある場合は、アラートが表示されます。 それ以外の場合は、通知センターに通知が表示されます。

> [!NOTE]
> 通常、明示的なユーザー登録/登録解除の入力を行わずに、アプリケーション ライフサイクルの適切な時点で (または初回起動時に)、登録 (および登録解除) アクションを実行します。 ただし、この例では、この機能をより簡単に調査およびテストできるように、明示的なユーザー入力が必要です。
