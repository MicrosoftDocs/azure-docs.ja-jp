---
title: 構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge の構成。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171671"
---
# <a name="event-grid-configuration"></a>Event Grid の構成

Event Grid には、環境ごとに変更できる多くの構成が用意されています。 次のセクションは、利用可能なすべてのオプションとその既定値のリファレンスです。

## <a name="tls-configuration"></a>TLS の構成

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 使用例については、[こちらの記事](configure-api-protocol.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Event Grid モジュールの TLS ポリシー。 既定値は HTTPS のみです。
|`inbound__serverAuth__serverCert__source`| Event Grid モジュールによって TLS 構成のために使用されるサーバー証明書のソース。 既定値は IoT Edge です。

## <a name="incoming-client-authentication"></a>着信クライアント認証

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-client-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 証明書ベースのクライアント認証をオンまたはオフにします。 既定値は true です。
|`inbound__clientAuth__clientCert__source`| クライアント証明書を検証するためのソース。 既定値は IoT Edge です。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 自己署名されたクライアント証明書を許可するポリシー。 既定値は true です。
|`inbound__clientAuth__sasKeys__enabled`| SAS キー ベースのクライアント認証をオンまたはオフにします。 既定値は off です。
|`inbound__clientAuth__sasKeys__key1`| 着信要求を検証するための値の 1 つ。
|`inbound__clientAuth__sasKeys__key2`| 着信要求を検証するための、オプションの 2 番目の値。

## <a name="outgoing-client-authentication"></a>発信クライアント認証
クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-identity-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 発信要求の ID 証明書の添付をオンまたはオフにします。 既定値は true です。
|`outbound__clientAuth__clientCert__source`| Event Grid モジュールの発信証明書を取得するためのソース。 既定値は IoT Edge です。

## <a name="webhook-event-handlers"></a>Webhook イベント ハンドラー

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-webhook-subscriber-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| HTTPS サブスクライバーのみが許可されるかどうかを制御するポリシー。 既定値は true です (HTTPS のみ)。
|`outbound__webhook__skipServerCertValidation`| サブスクライバーの証明書を検証するかどうかを制御するフラグ。 既定値は true です。
|`outbound__webhook__allowUnknownCA`| サブスクライバーが自己署名証明書を提示できるかどうかを制御するポリシー。 既定値は true です。 

## <a name="delivery-and-retry"></a>配信と再試行

この機能全般については、「[配信と再試行](delivery-retry.md)」を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | イベントを配信する試行の最大数。 既定値は 30 です。
| `broker__defaultEventTimeToLiveInSeconds` | イベントが配信されなかった場合に削除されるまでの Time-to-live (TTL) (秒単位)。 既定値は **7,200** 秒です

## <a name="output-batching"></a>出力のバッチ処理

この機能全般については、[配信と出力のバッチ処理](delivery-output-batching.md)に関するページを参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` ノブに許可される最大値。 既定値は `1_058_576` です。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` ノブに許可される最大値。 既定値は `50` です。
| `broker__defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch` のみが指定されている場合の配信要求の最大サイズ。 既定値は `1_058_576` です。
| `broker__defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes` のみが指定されている場合にバッチに追加するイベントの最大数。 既定値は `10` です。

## <a name="metrics"></a>メトリック

IoT Edge 上の Event Grid でメトリックを使用する方法については、[トピックとサブスクリプションの監視](monitor-topics-subscriptions.md)に関する記事を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `metrics__reporterType` | メトリック エンドポイントのレポーターの種類。 既定値は `none` で、メトリックを無効にします。 `prometheus` に設定すると、Prometheus の公開形式でメトリックが有効になります。