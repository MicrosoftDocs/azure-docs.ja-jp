---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493517"
---
## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

1. **[Azure: Functions]** 領域に戻り、サイド バーの **[ローカル プロジェクト]**  >  **[Functions]** を展開します。 `HttpExample` 関数を右クリック (Windows) または <kbd>Ctrl</kbd> キーを押しながらクリック (macOS) して、 **[Execute Function Now]\(今すぐ関数を実行\)** を選択します。

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code から今すぐ Azure で関数を実行する":::

1. **[Enter request body]\(要求本文を入力してください\)** に、要求メッセージ本文の値として `{ "name": "Azure" }` が表示されます。 Enter キーを押して、この要求メッセージを関数に送信します。  

1. Azure で関数を実行し、応答が返されると、Visual Studio Code で通知が発生します。
