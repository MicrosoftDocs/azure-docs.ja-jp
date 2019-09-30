---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203145"
---
## <a name="test"></a>Azure で関数を検証する

cURL を使用して、デプロイした関数を検証します。 前の手順からコピーした URL を関数キーも含めて使用して、URL にクエリ文字列 `&name=<yourname>` を追加します。

![cURL を使用した Azure の関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

コピーした URL を、関数キーも含めて、Web ブラウザーのアドレス バーに貼り付けることもできます。 繰り返しになりますが、要求を実行する前に、URL にクエリ文字列 `&name=<yourname>` を追加してください。

![Web ブラウザーを使用した関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
