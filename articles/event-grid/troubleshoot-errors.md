---
title: Azure Event Grid - トラブルシューティング ガイド
description: この記事では、エラー コード、エラー メッセージ、説明、および推奨されるアクションの一覧を示します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: c4c4e7980247e0ce41c685873a2d50b3e37fb405
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061477"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid エラーのトラブルシューティング
このトラブルシューティング ガイドでは、Azure Event Grid のエラー コード、エラー メッセージ、エラーの説明、およびこれらのエラーを受け取ったときに実行する必要がある推奨されるアクションの一覧を示します。 

## <a name="error-code-400"></a>エラー コード:400
| エラー コード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Topic name must be between 3 and 50 characters in length. (トピック名の長さは 3 文字から 50 文字でなければなりません。) | カスタム トピック名の長さは 3 文字から 50 文字でなければなりません。 トピック名には、英数字、数字、および "-" 文字のみを使用できます。 また、名前を次の予約語で開始することはできません。 <ul><li>Microsoft</li><li>EventGrid</li><li>システム</li></ul> | トピック名の要件に準拠した別のトピック名を選択します。 |
| HttpStatusCode.BadRequest<br/>400 | Domain name must be between 3 and 50 characters in length. (ドメイン名の長さは 3 文字から 50 文字でなければなりません。) | ドメイン名の長さは 3 文字から 50 文字でなければなりません。 トピック名には、英数字、数字、および "-" 文字のみを使用できます。 また、名前を次の予約語で開始することはできません。<ul><li>Microsoft</li><li>EventGrid</li><li>システム</li> | ドメイン名の要件に準拠した別のドメイン名を選択します。 |
| HttpStatusCode.BadRequest<br/>400 | 有効期限が無効です。 | イベント サブスクリプションの有効期限は、イベント サブスクリプションがいつ終了するかを決定します。 この値は、将来の有効な DateTime 値である必要があります。| イベント サブスクリプションの有効期限が有効な DateTime 形式であり、将来の日付に設定されていることを確認します。 |

## <a name="error-code-409"></a>エラー コード:409
| エラー コード | エラー メッセージ | 説明 | 推奨される操作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | 指定された名前のサービスは既に存在します。 Choose a different topic name. (別のトピック名を選択してください。)   | 適切な発行操作を確実に行うためには、カスタム トピック名は 1 つの Azure リージョン内で一意である必要があります。 Azure リージョンが異なれば同じ名前を使用できます。 | トピックに対して別の名前を選択してください。 |
| HttpStatusCode.Conflict <br/> 409 | Domain with the specified already exists. (指定された名前のドメインは既に存在します。) Choose a different domain name. (別のドメイン名を選択してください。) | 適切な発行操作を確実に行うためには、ドメイン名は 1 つの Azure リージョン内で一意である必要があります。 Azure リージョンが異なれば同じ名前を使用できます。 | ドメインに対して別の名前を選択してください。 |
| HttpStatusCode.Conflict<br/>409 | Quota limit reached. (クォータの制限値に達しました。) 詳細については、[Azure Event Grid の制限](../azure-subscription-service-limits.md#event-grid-limits)に関するページを参照してください。  | 各 Azure サブスクリプションには、使用できる Azure Event Grid リソースの数に制限があります。 このクォータの一部またはすべてが超過したため、それ以上リソースを作成できませんでした。 |  現在のリソースの使用状況を確認し、不要なリソースを削除します。 そのうえでクォータを増やす必要がある場合は、必要なリソースの正確な値を記載したメールを [aeg@microsoft.com](mailto:aeg@microsoft.com) までお送りください。 |


## <a name="next-steps"></a>次の手順
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
