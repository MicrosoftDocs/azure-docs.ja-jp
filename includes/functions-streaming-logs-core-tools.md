---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881321"
---
#### <a name="built-in-log-streaming"></a>組み込みのログ ストリーミング

次の例のように、`logstream` オプションを使用して、Azure 内で実行されている特定の関数アプリのストリーミング ログの受信を開始します。

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>ライブ メトリック ストリーム

また、次の例に示すように、`--browser` オプションを含めることによって、関数アプリの [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) を新しいブラウザー ウィンドウに表示することもできます。

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
