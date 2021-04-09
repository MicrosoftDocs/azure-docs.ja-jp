---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701412"
---
1. 関数を実行するには、Visual Studio で <kbd>F5</kbd> キーを押します。 ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合があります。 承認レベルは、ローカルで関数を実行するときには適用されません。

2. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![Azure ローカル ランタイム](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<YOUR_NAME>` を追加して、要求を実行します。 次の図は、関数によって返されるローカル GET 要求に対するブラウザーでの応答です。 

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. デバッグを停止するには、Visual Studio で <kbd>Shift</kbd>+<kbd>F5</kbd> キーを押します。
