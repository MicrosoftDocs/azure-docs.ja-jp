---
title: Azure Event Grid - トラブルシューティング ガイド
description: この記事では、エラー コード、エラー メッセージ、説明、および推奨されるアクションの一覧を示します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ab52cea6ab43763cf2d9dc2b57b7f369072a399e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119040"
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
| HttpStatusCode.Conflict<br/>409 | Quota limit reached. (クォータの制限値に達しました。) 詳細については、[Azure Event Grid の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)に関するページを参照してください。  | 各 Azure サブスクリプションには、使用できる Azure Event Grid リソースの数に制限があります。 このクォータの一部またはすべてが超過したため、それ以上リソースを作成できませんでした。 |    現在のリソースの使用状況を確認し、不要なリソースを削除します。 そのうえでクォータを増やす必要がある場合は、必要なリソースの正確な値を記載したメールを [aeg@microsoft.com](mailto:aeg@microsoft.com) までお送りください。 |

## <a name="error-code-403"></a>エラー コード:403

| エラー コード | エラー メッセージ | 説明 | 推奨される操作 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Forbidden <br/>403 | Publishing to {Topic/Domain} by client {IpAddress} is rejected due to IpAddress filtering rules. (クライアント {IpAddress} による {Topic/Domain} への発行は、IpAddress フィルター規則により拒否されます。) | トピックまたはドメインには IP ファイアウォール規則が構成されており、アクセスは構成済みの IP アドレスのみに制限されています。 | IP アドレスを IP ファイアウォール規則に追加します。[IP ファイアウォールの構成](configure-firewall.md)を参照 |
| HttpStatusCode.Forbidden <br/> 403 | Publishing to {Topic/Domain} by client is rejected as request came from Private Endpoint and no matching private endpoint connection found for the resource. (クライアントによる {Topic/Domain} への発行は、プライベート エンドポイントからの要求であり、リソースに一致するプライベート エンドポイント接続が見つからなかったため、拒否されます。) | トピックまたはドメインにプライベート エンドポイントが構成されており、発行要求が構成または承認されていないプライベートエンド ポイントから送信されました。 | トピックまたはドメインのプライベート エンドポイントを構成します。 [プライベート エンドポイントの構成](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>イベント サブスクリプション検証のトラブルシューティング

イベント サブスクリプションの作成中に `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` のようなエラー メッセージが表示された場合は、検証ハンドシェイクでエラーが発生したことを示しています。 このエラーを解決するには、次の点を確認します。

- Postman か curl、あるいは同様のツールを使用し、[サンプル SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 要求本文で Webhook URL に HTTP POST を実行します。
- Webhook で同期検証ハンドシェイク機構が実装されている場合、検証コードが応答の一部として返されることを確認します。
- Webhook で非同期検証ハンドシェイク機構が実装されている場合、HTTP POST から 200 OK が返されることを確認します。
- Webhook から応答で 403 (禁止) が返される場合、Webhook が Azure Application Gateway または Web アプリケーション ファイアウォールの背後にあるかどうかを確認します。 うしろにある場合、これらのファイアウォール規則を無効にし、HTTP POST をもう一度実行する必要があります。

  920300 (要求に Accept ヘッダーがありません、これは修正できます)

  942430 (制限された SQL 文字の異常検出 (引数): 特殊文字数を超過しました (12))

  920230 (複数の URL エンコードが検出されました)

  942130 (SQL インジェクション攻撃:SQL トートロジーが検出されました。)

  931130 (可能性のあるリモート ファイル インクルード (RFI) 攻撃 = ドメイン外参照/リンク)


## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
