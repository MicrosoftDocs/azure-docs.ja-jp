---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455174"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。

1. 関数をテストするには、関数コードにブレークポイントを設定し、F5 キーを押して関数アプリ プロジェクトを開始します。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。 この URL には、`code` クエリ パラメーターに渡される関数キーが含まれています。

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 ブレークポイントに到達すると、実行が一時停止されます。

1. 実行を続行したときの、GET 要求に対するブラウザーの応答を次に示します。

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. デバッグを停止するには、Shift キーを押しながら F5 キーを押します。