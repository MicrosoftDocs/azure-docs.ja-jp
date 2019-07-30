---
title: ローカルでの Azure Functions の開発と実行 | Microsoft Docs
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターでコーディングしてテストする方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: abb807262d976419a0c7700046bd8ad58322fc90
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537089"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions をローカルでコーディングしてテストする

Azure Functions の開発やテストは、[Azure Portal] で行うことができますが、多くの開発者は、ローカル開発エクスペリエンスを好みます。 Functions では、ローカル コンピューター上でお気に入りのコード エディターや開発ツールを使用して、関数を作成し、テストすることができます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。

## <a name="local-development-environments"></a>ローカル開発環境

ローカル コンピューター上で関数を開発する方法は、[言語](supported-languages.md)とツールの設定によって異なります。 ローカル開発をサポートする環境を次の表に示します。

|環境                              |Languages         |説明|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (クラス ライブラリ)](functions-dotnet-class-library.md)、[C# スクリプト (.csx)](functions-reference-csharp.md)、[JavaScript](functions-reference-node.md)、[PowerShell](functions-create-first-function-powershell.md)、[Python](functions-reference-python.md) | [VS Code 用の Azure Functions 拡張](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)は、VS Code に対して Functions サポートを追加します。 Core Tools が必要です。 Core Tools のバージョン 2.x を使用した場合は、Linux、MacOS、および Windows 上での開発がサポートされます。 詳細については、「[Create your first function using Visual Studio Code](functions-create-first-function-vs-code.md)」 (Visual Studio Code を使用して最初の関数を作成する) を参照してください。 |
| [コマンド プロンプトまたはターミナル](functions-run-local.md) | [C# (クラス ライブラリ)](functions-dotnet-class-library.md)、[C# スクリプト (.csx)](functions-reference-csharp.md)、[JavaScript](functions-reference-node.md)、[PowerShell](functions-reference-powershell.md)、[Python](functions-reference-python.md) | [Azure Functions Core Tools] は、関数を作成するためのコア ランタイムとテンプレートを提供しており、これにより、ローカル開発が可能です。 バージョン 2.x では、Linux、MacOS、および Windows 上での開発がサポートされています。 すべての環境は、ローカル Functions ランタイムとして、Core Tools を利用します。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (クラス ライブラリ)](functions-dotnet-class-library.md) | Azure Functions ツールは、[Visual Studio 2019](https://www.visualstudio.com/vs/) 以降のバージョンの **Azure 開発**ワークロードに含まれています。 クラス ライブラリの関数をコンパイルして .dll を Azure に発行できます。 ローカル テスト用の Core Tools が含まれています。 詳細については、「[Develop Azure Functions using Visual Studio](functions-develop-vs.md)」(Visual Studio を使用して Azure Functions を開発する) を参照してください。 |
| [Maven](functions-create-first-java-maven.md) (各種) | [Java](functions-reference-java.md) | Core Tools と統合して、Java 関数を開発できます。 バージョン 2.x では、Linux、MacOS、および Windows 上での開発がサポートされています。 詳細については、「[Create your first function with Java and Maven](functions-create-first-java-maven.md)」(Java および Maven を使用して、最初の関数を作成する) を参照してください。 [Eclipse](functions-create-maven-eclipse.md) や [IntelliJ IDEA](functions-create-maven-intellij.md) を使った開発もサポートされます。 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

これらの各ローカル開発環境では、関数アプリ プロジェクトを作成し、事前定義の Functions テンプレートを使用して新しい関数を作成できます。 各環境では、Core Tools が使用されています。そのため、マシン上の実際の Functions ランタイムに対して、その他のアプリの場合と同様に関数をテストしたり、デバッグしたりできます。 また、これらのどの環境からでも、関数アプリ プロジェクトを Azure に発行できます。  

## <a name="next-steps"></a>次の手順

+ Visual Studio 2019 を使用して、ローカルでコンパイル済み C# 関数を開発する方法の詳細については、「[Visual Studio を使用する Azure Functions の開発](functions-develop-vs.md)」を参照してください。
+ Mac、Linux、または Windows コンピューター上で VS Code を使用して、ローカルで関数を開発する方法の詳細については、「[Azure Functions の VS Code ドキュメントに関する記事](https://code.visualstudio.com/tutorials/functions-extension/getting-started)」を参照してください。
+ コマンド プロンプトまたはターミナルから関数を開発する方法の詳細については、「[Work with Azure Functions Core Tools](functions-run-local.md)」(Azure Functions Core Tools を使用して作業する) を参照してください。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
