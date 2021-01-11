---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88704300"
---
## <a name="run-the-function-locally"></a>Functions をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. Functions を呼び出すには、F5 キーを押して Functions アプリ プロジェクトを起動します。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. Azure Functions Core Tools をまだインストールしていない場合は、プロンプトで **[インストール]** を選択します。 Core Tools がインストールされると、アプリが**ターミナル** パネルで起動します。 HTTP によってトリガーされる Functions の URL エンドポイントがローカルで実行されていることを確認できます。 

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Core Tools が実行されている状態で、次の URL に移動して、`?name=Functions` クエリ文字列を含む GET 要求を実行します。

    `http://localhost:7071/api/HttpExample?name=Functions`

1. 応答が返され、ブラウザーに次のように表示されます。

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 要求に関する情報が **[ターミナル]** パネルに表示されます。

    ![[ターミナル] パネルでの関数の実行](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Ctrl + C キーを押して Core Tools を停止し、デバッガーの接続を解除します。
