---
title: Azure Event Grid での Key Vault の監視
description: Azure Event Grid を使用して Key Vault のイベントをサブスクライブする
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ee38196f0b1a6e00d385dc0d2c88d45a4291d3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087438"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Azure Event Grid での Key Vault の監視

Key Vault と Event Grid の統合により、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知できます。 状態の変更は、有効期限が切れる直前 (期限切れの 30 日前) のシークレット、有効期限が切れたシークレット、または新しいバージョンが利用可能になったシークレットとして定義されます。 3 つすべての種類のシークレット (キー、証明書、シークレット) の通知がサポートされています。

アプリケーションでは、最新のサーバーレス アーキテクチャを使って、これらのイベントに対応できます。複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 イベントが [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通してイベント ハンドラー ([Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) など) やユーザー独自の Webhook へのイベントにもプッシュされるため、支払いは使用した分だけで済みます。 価格の詳細については、[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)に関する記事をご覧ください。

## <a name="key-vault-events-and-schemas"></a>Key Vault のイベントとスキーマ

Event Grid は、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Key Vault イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Key Vault イベントは、eventType プロパティが "Microsoft.KeyVault" で始まっているので識別できます。

詳細については、 [Key Vault イベント スキーマ](../../event-grid/event-schema-key-vault.md)に関するページを参照してください。

> [!WARNING]
> 通知イベントは、新しいバージョンのシークレット、キー、証明書でのみトリガーされます。それらの通知を受け取るには、まずキー コンテナーのイベントをサブスクライブする必要があります。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Key Vault イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように、複数のサブスクリプションを構成できます。 イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているキー コンテナーからのものであることを確認することが重要です。
* 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
* わからないフィールドは無視します。  この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
* イベントを特定のイベントに制限するには、"subject" プレフィックスとサフィックスの一致を使用します。

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の概要](overview.md)
- [Azure Event Grid の概要](../../event-grid/overview.md)
- 方法:[Automation Runbook への Key Vault イベントのルーティング](event-grid-tutorial.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)
- [Azure Automation の概要](../../automation/index.yml)
