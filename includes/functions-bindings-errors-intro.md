---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 178fa7d5f129a12736ec068fca605ba24cd37839
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955869"
---
Azure Functions で発生するエラーは、次のいずれかが元になっています。

- Azure Functions の組み込みの[トリガーとバインド](..\articles\azure-functions\functions-triggers-bindings.md)の使用
- 基になっている Azure サービスの API の呼び出し
- REST エンドポイントの呼び出し
- クライアント ライブラリ、パッケージ、またはサードパーティ API の呼び出し

データやメッセージが失われないようにするには、次の確実なエラー処理手法が重要です。 推奨されるエラー処理方法には、次のアクションが含まれます。

- [Application Insights を有効にする](../articles/azure-functions/functions-monitoring.md)
- [構造化エラー処理を使用する](#use-structured-error-handling)
- [べき等に設計する](../articles/azure-functions/functions-idempotent.md)
- [再試行ポリシーを実装する](../articles/azure-functions/functions-reliable-event-processing.md) (該当する場合)

### <a name="use-structured-error-handling"></a>構造化エラー処理を使用する

アプリケーションの正常性を監視するには、エラーをキャプチャして発行することが重要です。 関数コードの最上位レベルに、try/catch ブロックを含める必要があります。 catch ブロックでは、エラーをキャプチャして発行できます。

### <a name="retry-support"></a>再試行のサポート

次のトリガーには、組み込みの再試行サポートがあります。

* [Azure BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (キュー/トピック)](../articles/azure-functions/functions-bindings-service-bus.md)

既定では、これらのトリガーにより要求が最大 5 回再試行されます。 5 回目の再試行後に、両方のトリガーによって[有害キュー](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)にメッセージが書き込まれます。

他のトリガーまたはバインドの種類については、再試行ポリシーを手動で実装する必要があります。 手動の実装には、[有害メッセージ キュー](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs)へのエラー情報の書き込みが含まれる場合があります。 有害キューに書き込むことによって、後で操作を再試行する機会ができます。 この方法は、Blob Storage トリガーによって使用されるものと同じです。
