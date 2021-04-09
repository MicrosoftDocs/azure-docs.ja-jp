---
title: Azure Event Grid - トラブルシューティング ガイド
description: この記事では、エラー コード、エラー メッセージ、説明、および推奨されるアクションの一覧を示します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592993"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid エラーのトラブルシューティング
このトラブルシューティング ガイドでは、次の情報を提供します。 

- Azure Event Grid のエラー コード
- エラー メッセージ
- エラーの説明
- これらのエラーが発生したときに実行する必要がある推奨される操作。 

## <a name="error-code-400"></a>エラー コード:400
| エラー コード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Topic name must be between 3 and 50 characters in length. (トピック名の長さは 3 文字から 50 文字でなければなりません。) | カスタム トピック名の長さは 3 文字から 50 文字でなければなりません。 トピック名には、英数字、数字、および "-" 文字のみを使用できます。 また、名前を次の予約語で開始することはできません。 <ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li></ul> | トピック名の要件に準拠した別のトピック名を選択します。 |
| HttpStatusCode.BadRequest<br/>400 | Domain name must be between 3 and 50 characters in length. (ドメイン名の長さは 3 文字から 50 文字でなければなりません。) | ドメイン名の長さは 3 文字から 50 文字でなければなりません。 ドメイン名に使用できるのは、英数字、数字、および "-" 文字のみです。 また、名前を次の予約語で開始することはできません。<ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li> | ドメイン名の要件に準拠した別のドメイン名を選択します。 |
| HttpStatusCode.BadRequest<br/>400 | 有効期限が無効です。 | イベント サブスクリプションの有効期限は、イベント サブスクリプションがいつ終了するかを決定します。 この値は、将来の有効な DateTime 値である必要があります。| イベント サブスクリプションの有効期限が有効な DateTime 形式であり、将来の日付に設定されていることを確認します。 |

## <a name="error-code-409"></a>エラー コード:409
| エラー コード | エラー メッセージ | 説明 | 推奨される操作 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | 指定された名前のサービスは既に存在します。 Choose a different topic name. (別のトピック名を選択してください。)   | 適切な発行操作を確実に行うには、カスタム トピック名は 1 つの Azure リージョン内で一意である必要があります。 Azure リージョンが異なれば同じ名前を使用できます。 | トピックに対して別の名前を選択してください。 |
| HttpStatusCode.Conflict <br/> 409 | Domain with the specified already exists. (指定された名前のドメインは既に存在します。) Choose a different domain name. (別のドメイン名を選択してください。) | 適切な発行操作を確実に行うには、ドメイン名は 1 つの Azure リージョン内で一意である必要があります。 Azure リージョンが異なれば同じ名前を使用できます。 | ドメインに対して別の名前を選択してください。 |
| HttpStatusCode.Conflict<br/>409 | Quota limit reached. (クォータの制限値に達しました。) 詳細については、[Azure Event Grid の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)に関するページを参照してください。  | 各 Azure サブスクリプションには、使用できる Azure Event Grid リソースの数に制限があります。 このクォータの一部またはすべてが超過したため、それ以上リソースを作成できませんでした。 |    現在のリソースの使用状況を確認し、不要なリソースを削除します。 そのうえでクォータを増やす必要がある場合は、必要なリソースの正確な値を記載したメールを [aeg@microsoft.com](mailto:aeg@microsoft.com) までお送りください。 |

## <a name="error-code-403"></a>エラー コード:403

| エラー コード | エラー メッセージ | 説明 | 推奨される操作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Forbidden <br/>403 | Publishing to {Topic/Domain} by client {IpAddress} is rejected because of IpAddress filtering rules. (クライアント {IpAddress} による {Topic/Domain} への発行は、IpAddress フィルター規則により拒否されます。) | トピックまたはドメインには IP ファイアウォール規則が構成されており、アクセスは構成済みの IP アドレスのみに制限されています。 | IP アドレスを IP ファイアウォール規則に追加します。[IP ファイアウォールの構成](configure-firewall.md)を参照 |
| HttpStatusCode.Forbidden <br/> 403 | Publishing to {Topic/Domain} by client is rejected as request came from Private Endpoint and no matching private endpoint connection found for the resource. (クライアントによる {Topic/Domain} への発行は、プライベート エンドポイントからの要求であり、リソースに一致するプライベート エンドポイント接続が見つからなかったため、拒否されます。) | トピックまたはドメインにプライベート エンドポイントがあり、発行要求が構成または承認されていないプライベートエンド ポイントから送信されました。 | トピックまたはドメインのプライベート エンドポイントを構成します。 [プライベート エンドポイントの構成](configure-private-endpoints.md) |

また、Webhook が Azure Application Gateway または Web アプリケーション ファイアウォールの背後にあるかどうかを確認します。 そうである場合は、これらのファイアウォール規則を無効にし、もう一度 HTTP POST を実行します。

- 920300 (要求に accept ヘッダーがありません)
- 942430 (制限された SQL 文字の異常検出 (引数): 特殊文字数を超過しました (12))
- 920230 (複数の URL エンコードが検出されました)
- 942130 (SQL インジェクション攻撃:SQL トートロジーが検出されました。)
- 931130 (可能性のあるリモート ファイル インクルード (RFI) 攻撃 = ドメイン外参照/リンク)



## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
