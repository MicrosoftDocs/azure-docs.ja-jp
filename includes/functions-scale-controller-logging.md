---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169912"
---
| | |
|--|--|
|**`<DESTINATION>`**| ログの送信先。 有効な値は `AppInsights` と `Blob` です。<br/>`AppInsights` を使用する場合は、[関数アプリで Application Insights が有効になっている](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration)ことを確認してください。<br/>宛先を `Blob` に設定すると、`AzureWebJobsStorage` アプリケーション設定で設定されている既定のストレージ アカウントの `azure-functions-scale-controller` という名前の BLOB コンテナーにログが作成されます。 |
|**`<VERBOSITY>`** | ログ記録のレベルを指定します。 サポートされている値は、`None`、`Warning`、および `Verbose` です。<br/>`Verbose` に設定すると、スケール コントローラーは、すべてのワーカー数の変更の理由と、それらの決定の要因となるトリガーに関する情報をログに記録します。 詳細ログには、トリガー警告と、スケール コントローラーの実行前と実行後にトリガーによって使用されたハッシュが含まれます。 |

> [!CAUTION]
> スケール コントローラーのログ記録を有効にしたままにしないでください。 スケール コントローラーの動作を理解するのに十分なデータを収集するまでログ記録を有効にし、その後は無効にします。