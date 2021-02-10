---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 1a0521f76a2cf986f7036d1f701a40a156d16ee7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493484"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. 関数を呼び出すには、<kbd>F5</kbd> キーを押して関数アプリ プロジェクトを起動します。 Core Tools からの出力が **ターミナル** パネルに表示されます。 アプリが **ターミナル** パネルで起動します。 HTTP によってトリガーされる関数の URL エンドポイントがローカルで実行されていることを確認できます。

    ![ローカル関数の VS Code の出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Windows での実行に問題がある場合、Visual Studio Code の既定のターミナルが **WSL Bash** に設定されていないことをご確認ください。

1. Core Tools を実行したまま、**Azure: Functions** 領域に移動します。 **[Functions]** の **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code から今すぐ関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  

1. ローカルで関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。 関数の実行に関する情報は、 **[ターミナル]** パネルに表示されます。

1. <kbd>Ctrl + C</kbd> キーを押して Core Tools を停止し、デバッガーの接続を解除します。
