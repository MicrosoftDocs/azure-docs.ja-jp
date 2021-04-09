---
title: 関数を他の Azure サービスに接続する
description: 他の Azure サービスに接続するバインドを、ご利用の Azure Functions プロジェクト内の既存の関数に追加する方法について説明します。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258203"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>バインドを使用して関数を Azure サービスに接続する

関数を作成するときには、一連のトリガー テンプレートから、言語固有のトリガー コードがプロジェクトに追加されます。 入力または出力のバインドを使用して関数を他のサービスに接続する場合は、その関数に特定のバインド定義を追加する必要があります。 バインドの詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

## <a name="local-development"></a>ローカル開発       

関数をローカルで開発する場合は、関数コードを更新してバインドを追加する必要があります。 Visual Studio Code を使用すると、より簡単にバインドを関数に追加できるようになります。  

### <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code を使用して関数を開発し、その関数で function.json ファイルを使用すると、Azure Functions 拡張機能によって、既存の function.json ファイルにバインドが自動的に追加されます。 詳細については、「[入出力バインドを追加する](functions-develop-vs-code.md#add-input-and-output-bindings)」を参照してください。   

### <a name="manually-add-bindings-based-on-examples"></a>例に基づいて手動でバインドを追加する

既存の関数にバインドを追加するときには、関数コードと、言語で使用されている場合は function.json ファイルの両方を更新する必要があります。 .NET クラス ライブラリと Java 関数はどちらも、function.json ではなく属性を使用するため、代わりにそれを更新する必要があります。

次の表を使用して、既存の関数を更新する際の参考に利用できる特定のバインドの種類の例を見つけてください。 まず、実際のプロジェクトに対応する言語のタブを選択します。 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) で関数を開発するときには、特定の関数の **[統合]** タブで、入出力バインドを追加します。 新しいバインドは、言語に応じて、function.json ファイルまたはメソッドの属性のいずれかに追加されます。 以下の記事では、ポータルで既存の関数にバインドを追加する方法の例を示しています。

+ [キュー ストレージの出力バインド](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB の出力バインド](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>次のステップ

+ [Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)
