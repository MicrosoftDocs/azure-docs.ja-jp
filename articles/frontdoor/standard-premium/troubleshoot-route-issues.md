---
title: Azure Front Door Standard または Premium の構成に関する問題のトラブルシューティング
description: このチュートリアルでは、Azure Front Door Standard または Premium のインスタンスで発生する可能性のあるいくつかの一般的な問題をトラブルシューティングする方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098079"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Azure Front Door Standard または Premium でのルーティングに関する一般的な問題のトラブルシューティング

この記事では、Azure Front Door の構成で、よく発生する可能性のあるルーティングの問題をトラブルシューティングする方法について説明します。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>数秒後に Azure Front Door から 503 応答

### <a name="symptom"></a>症状

* Azure Front Door を経由しないでバックエンドに送信される通常の要求は成功します。 Azure Front Door を経由すると、503 エラー応答が返されます。
* Azure Front Door からのエラーは、通常約 30 秒後に表示されます。

### <a name="cause"></a>原因

この問題の原因は、次の 2 つのどちらかである可能性があります。
 
* 配信元で、Azure Front Door から要求を受信するために構成されているタイムアウト (既定値は 30 秒) よりも長い時間がかかっています。
* Azure Front Door からの要求に対して応答を送信するのに、タイムアウト値よりも長い時間がかかっています。 

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

* 要求を (Azure Front Door を経由せずに) バックエンドに直接送信します。 バックエンドからの応答に通常かかる時間を確認します。
* Azure Front Door 経由で要求を送信し、503 応答を受け取るかどうかを確認します。 そうでない場合は、タイムアウトの問題ではない可能性があります。 サポートにお問い合せください。
* Azure Front Door を経由したときに 503 エラー応答コードが生成される場合は、Azure Front Door の `sendReceiveTimeout` フィールドを構成します。 既定のタイムアウトは、最大 4 分 (240 秒) まで延長できます。 この設定は `Endpoint Setting` の下にあり、`Origin response timeout` と呼ばれます。 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>カスタム ドメインに送信された要求で状態コード 400 が返される

### <a name="symptom"></a>症状

* Azure Front Door インスタンスを作成しましたが、ドメインまたはフロントエンド ホストへの要求で HTTP 400 の状態コードが返されます。
* カスタム ドメインに対して、構成したフロントエンド ホストへの DNS マッピングを作成しました。 しかし、カスタム ドメインのホスト名に要求を送信すると、HTTP 400 の状態コードが返されます。 構成したバックエンドにルーティングされているようには見えません。

### <a name="cause"></a>原因

この問題は、フロントエンド ホストとして追加されたカスタム ドメインに対してルーティング規則を構成しなかった場合に発生します。 そのフロントエンド ホストに対して明示的にルーティング規則を追加する必要があります。 Azure Front Door サブドメイン (*.azurefd.net) 下でフロントエンド ホストに対してルーティング規則が既に構成されている場合も、これに該当します。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

カスタム ドメインに対して、選択した配信元グループにトラフィックを送信するルーティング規則を追加します。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door によって HTTP が HTTPS にリダイレクトされない

### <a name="symptom"></a>症状

Azure Front Door には HTTP を HTTPS にリダイレクトするルーティング規則がありますが、ドメインへのアクセスではプロトコルとして HTTP が維持されます。

### <a name="cause"></a>原因

この動作は、Azure Front Door のルーティング規則を正しく構成していない場合に発生する可能性があります。 基本的に、現在の構成は固有ではなく、規則が競合している可能性があります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>フロントエンドのホスト名の要求で状態コード 411 が返される

### <a name="symptom"></a>症状

Azure Front Door Standard または Premium のインスタンスを作成し、フロントエンド ホスト、配信元グループ (1 つ以上の配信元が含まれている)、およびフロントエンド ホストを配信元グループに接続するルーティング規則を作成しました。 構成したフロントエンド ホストに要求を送信しても、HTTP 411 の状態コードが返されるのでコンテンツを利用できないようです。

これらの要求に対する応答には、説明文が記載された HTML エラー ページが応答本文に含まれる場合もあります。 (例: `HTTP Error 411. The request must be chunked or have a content length`)。

### <a name="cause"></a>原因

この現象には、以下のような複数の原因が考えられます。 全体的な理由は、HTTP 要求が RFC に完全には準拠していないことです。 

非準拠の例としては、`Content-Length` または `Transfer-Encoding` ヘッダーを使用せずに送信される `POST` 要求があります (たとえば、`curl -X POST https://example-front-door.domain.com` を使用)。 この要求は [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) で設定されている要件を満たしていません。 それは Azure Front Door により HTTP 411 応答でブロックされます。

この動作は、Azure Front Door の Web アプリケーション ファイアウォール (WAF) 機能とは別のものです。 現時点では、この動作を無効にする方法はありません。 WAF の機能が使用されていない場合でも、すべての HTTP 要求で要件が満たされている必要があります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

- 要求が、必要な RFC に規定されている要件に準拠していることを確認します。

- 要求への応答として返される HTML メッセージ本文をメモします。 多くの場合、メッセージ本文には、要求が "*どのように*" 非準拠であるかが説明されています。

## <a name="next-steps"></a>次のステップ

[Front Door Standard または Premium を作成する](create-front-door-portal.md)方法について学習します。
