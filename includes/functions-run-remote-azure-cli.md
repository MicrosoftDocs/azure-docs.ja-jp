---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424929"
---
## <a name="invoke-the-function-on-azure"></a>Azure 上の関数を呼び出す

この関数は HTTP トリガーを使用しているため、呼び出しは、その URL にブラウザーから HTTP 要求を送信するか、または curl などのツールを使用して行います。 

# <a name="browser"></a>[ブラウザー](#tab/browser)

publish コマンドの出力に表示されている完全な **呼び出し URL** にクエリ パラメーター `&name=Functions` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

![Azure 上で実行された関数の出力をブラウザーで表示したところ](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**呼び出し URL** にパラメーター `&name=Functions` を追加して [`curl`](https://curl.haxx.se/) を実行します。 "Hello Functions" というテキストがコマンドの出力として表示されます。

![Azure 上で実行された関数の出力を curl を使用して表示したところ](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
