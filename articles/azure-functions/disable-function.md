---
title: Azure Functions で関数を無効にする方法
description: Azure Functions で関数を無効または有効にする方法を学びます。
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: bffb3136c77074ecd50e839fd7c73144ad910967
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970977"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions で関数を無効にする方法

この記事では、Azure Functions で関数を無効にする方法について説明します。 関数を*無効にする*には、その関数用に定義された自動トリガーをランタイムが無視するようにします。 これにより、関数アプリ全体を停止することなく、特定の関数の実行を防ぐことができます。

関数を無効にするに方法としては、`AzureWebJobs.<FUNCTION_NAME>.Disabled` の形式のアプリの設定を使用するようお勧めします。 このアプリケーション設定は、[Azure CLI](/cli/azure/) を使用したり、[Azure portal](https://portal.azure.com) で関数の **[管理]** タブを使用したりするなど、さまざまな方法で作成または編集できます。 

## <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure CLI でアプリの設定を作成および変更するには、[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドを使います。 `AzureWebJobs.QueueTrigger.Disabled` という名前のアプリの設定を作成して `true` に設定することにより `QueueTrigger` という名前の関数を無効にするコマンドを次に示します。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

関数を再度有効にするには、値 `false` を指定して同じコマンドを再実行します。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>ポータルを使用する

関数の **[管理]** タブにある **[関数の状態]** スイッチを使用することもできます。このスイッチを機能させるには、`AzureWebJobs.<FUNCTION_NAME>.Disabled` アプリ設定を作成および削除します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>その他の方法

このアプリケーション設定方法をすべての言語およびすべてのランタイム バージョンに対して使用するようお勧めしますが、関数を無効にする方法は他にもいくつかあります。 これらのメソッドは、言語とランタイム バージョンによって異なり、旧バージョンとの下位互換性が保持されています。 

### <a name="c-class-libraries"></a>C# クラス ライブラリ

クラス ライブラリ関数では、`Disable` 属性を使用して、関数がトリガーされないようにすることもできます。 次の例に示すように、コンストラクターのパラメーターを指定せずに属性を使用できます。

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

コンストラクターのパラメーターを指定せずに属性を使用するには、プロジェクトを再コンパイルおよび再デプロイして関数の無効状態を変更する必要があります。 より柔軟性に優れた方法でこの属性を使用するには、次の例に示すように、ブール値のアプリ設定を参照するコンストラクターのパラメーターを含めます。

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

この方法では、アプリ設定を変更することで、関数を有効および無効にすることができます。再コンパイルや再デプロイは必要ありません。 アプリ設定を変更すると関数アプリが再起動されるため、無効状態の変更がすぐに認識されます。

> [!IMPORTANT]
> `Disabled` 属性は、クラス ライブラリの関数を無効にする唯一の方法です。 クラス ライブラリの関数用に生成される *function.json* ファイルは直接編集することを意図したものではありません。 そのファイルを編集する場合、`disabled` プロパティに対してどのような処理を行っても効果はありません。
>
> **[管理]** タブの **[関数の状態]** スイッチについても同様です。このスイッチを機能させるには、*function.json* ファイルを変更する必要があるためです。
>
> また、無効でない関数が無効であるとポータルに表示される可能性があることに注意してください。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - スクリプト言語

バージョン 1.x では、*function.json* ファイルの `disabled` プロパティを使用して、関数をトリガーしないようにランタイムを指定することもできます。 このメソッドは、C# スクリプトや JavaScript などのスクリプト言語に対してのみ機能します。 この `disabled` プロパティは、`true` またはアプリ設定の名前に設定できます：

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

2 つ目の例では、`true` または 1 に設定されている IS_DISABLED という名前のアプリ設定がある場合に関数が無効になります。

Azure Portal でファイルを編集するか、または関数の **[管理]** タブで **[関数の状態]** スイッチを使用できます。ポータルのスイッチを機能させるには、*function.json* ファイルを変更します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>次の手順

この記事は、自動トリガーの無効化に関する記事です。 トリガーについて詳しくは、[トリガーとバインド](functions-triggers-bindings.md)に関するページをご覧ください。
