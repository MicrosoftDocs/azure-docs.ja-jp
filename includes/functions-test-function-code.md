---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180957"
---
## <a name="test"></a>Azure で関数をテストする

cURL を使用して、デプロイした関数をテストします。 前の手順からコピーした URL を使用して、URL にクエリ文字列 `&name=<yourname>` を次の例のように追加します。

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![cURL を使用した Azure の関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

コピーした URL を Web ブラウザーのアドレスに貼り付けることもできます。 繰り返しになりますが、要求を実行する前に、URL にクエリ文字列 `&name=<yourname>` を追加してください。

![Web ブラウザーを使用した関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
