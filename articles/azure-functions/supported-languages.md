---
title: Azure Functions でサポートされている言語
description: どの言語がサポートされているか (GA) と、どの言語が試験段階またはプレビューの段階であるかを説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 5f55122b3bf4bb7160459d524b20dd1303cc0fd8
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835422"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions でサポートされている言語

この記事では、Azure Functions で使用できる言語のサポートのレベルについて説明します。

## <a name="levels-of-support"></a>サポートのレベル

サポートのレベルは、次の 3 つです。

* **一般公開 (GA)** - 完全にサポートされ、運用環境用に承認されています。
* **プレビュー** - まだサポートされていませんが、今後 GA 状態に達すると想定されています。
* **試験段階** - サポートされておらず、今後廃止される可能性があります。最終的にプレビューまたは GA 状態になる保証はありません。

## <a name="languages-in-runtime-1x-and-2x"></a>ランタイム 1.x および 2.x の言語

[2 つのバージョンの Azure Functions ランタイム](functions-versions.md)を利用できます。 次の表は、各ランタイム バージョンでどの言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>試験段階の言語

バージョン 1.x の試験段階の言語は、スケーリングが適切に行われず、一部のバインドがサポートされていません。 たとえば、1.x での PowerShell の試験的なサポートはバージョン 5.1 に限定されています。これは、関数アプリを実行する VM に既定でインストールされているためです。 PowerShell スクリプトを実行する場合は、[Azure Automation](https://azure.microsoft.com/services/automation/) を検討してください。

信頼性が大切である場合には、試験段階の機能は使用しないでください。それらの言語には公式なサポートがありません。 実験段階の言語で発生した問題については、サポート ケースを開かないでください。 

バージョン 2.x ランタイムでは、試験段階の言語はサポートされていません。 新しい言語のサポートは、運用環境でその言語をサポートできる場合にのみ追加されます。 

### <a name="language-extensibility"></a>言語拡張

2.x ランタイムは、[言語拡張](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)を提供するように設計されています。 2.x ランタイムの JavaScript および Java 言語は、この拡張機能で構築されています。

## <a name="next-steps"></a>次の手順

Azure Functions で GA またはプレビューであるいずれかの言語を使用する方法の詳細については、以下のリソースを参照してください。

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
