---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444617"
---
## <a name="create-an-azure-functions-project"></a>関数を使用して Functions プロジェクトを作成する 

Visual Studio Code の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Create new project...` を検索して選択します。

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 ここでは、ワークスペースに含まれるプロジェクト フォルダーは選択しないでください。

1. プロンプトに従って、次の情報を入力します。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | Select a language for your function app project (関数アプリ プロジェクトの言語を選択してください) | C# または JavaScript | この記事は C# と JavaScript をサポートしています。 Python については[この Python の記事](https://code.visualstudio.com/docs/python/tutorial-azure-functions)を、PowerShell については[この PowerShell の記事](../articles/azure-functions/functions-create-first-function-powershell.md)を参照してください。  |
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | HTTP トリガー | 新しい関数アプリで HTTP トリガー関数を作成します。 |
    | Provide a function name (関数名を指定してください) | HttpTrigger | Enter キーを押して既定の名前を使用します。 |
    | Provide a namespace (名前空間を指定してください) | My.Functions | (C# のみ) C# クラス ライブラリには名前空間が必要です。  |
    | 承認レベル | Function | 関数の HTTP エンドポイントを呼び出すには[関数キー](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)が必要です。 |
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | Add to workspace (ワークスペースに追加) | 現在のワークスペースに関数アプリを作成します。 |

Visual Studio Code によって、新しいワークスペースに関数アプリ プロジェクトが作成されます。 このプロジェクトには、[host.json](../articles/azure-functions/functions-host-json.md) および [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) 構成ファイルと、言語固有のプロジェクト ファイルが含まれています。 

関数アプリ プロジェクトの HttpTrigger フォルダーにも、新しい HTTP トリガー関数が作成されます。