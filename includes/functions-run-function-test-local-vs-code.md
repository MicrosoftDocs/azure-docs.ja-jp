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
ms.openlocfilehash: d47caaf9dbca14364771e0d8ba1e7f6ce16ef2d6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174634"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Azure Functions Core Tools を使用すると、ローカルの開発用コンピューター上で Azure Functions プロジェクトを実行できます。

1. 関数をテストするには、関数コードにブレークポイントを設定し、F5 キーを押して関数アプリ プロジェクトを開始します。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. **ターミナル** パネルで、HTTP によってトリガーされる関数の URL エンドポイントをコピーします。 

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 ブレークポイントに到達すると、実行が一時停止されます。

1. 実行を続行したときの、GET 要求に対するブラウザーの応答を次に示します。

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. デバッグを停止するには、Shift キーを押しながら F5 キーを押します。