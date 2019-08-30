---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949928"
---
## <a name="create-the-local-function-app-project"></a>ローカルの関数アプリ プロジェクトを作成する

コマンド ラインから次のコマンドを実行し、現在のローカル ディレクトリの `MyFunctionProj` フォルダー内に関数アプリ プロジェクトを作成します。 GitHub リポジトリも `MyFunctionProj` 内に作成されます。

```command
func init MyFunctionProj
```

メッセージが表示されたら、次の言語からワーカー ランタイムを選択します。

+ `dotnet`: .NET クラス ライブラリ プロジェクト (.csproj) を作成します。
+ `node`: Node.js ベースのプロジェクトを作成します。 `javascript` または `typescript` を選択します。 
+ `python`: Python プロジェクトの場合は、「[Azure で HTTP によってトリガーされる関数を作成する](../articles/azure-functions/functions-create-first-function-python.md)」の手順を実行してください。
+ `powershell`: PowerShell プロジェクトの場合は、[Azure で初めての PowerShell 関数を作成する](../articles/azure-functions/functions-create-first-function-powershell.md)手順を実行してください。 


プロジェクトが作成されたら、次のコマンドを使用して、新しい `MyFunctionProj` プロジェクト フォルダーに移動します。

```command
cd MyFunctionProj
```
