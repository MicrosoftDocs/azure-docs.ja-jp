---
title: Azure Front Door の構成に関する問題のトラブルシューティング
description: このチュートリアルでは、Azure Front Door インスタンスで発生する可能性のある、一般的な問題のいくつかを自己解決する方法を説明します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629990"
---
# <a name="troubleshooting-common-routing-problems"></a>ルーティングの一般的な問題のトラブルシューティング

この記事では、Azure Front Door の構成で、よく発生する可能性のあるルーティングの問題をトラブルシューティングする方法について説明します。

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>数秒後に Azure Front Door から 503 応答

### <a name="symptom"></a>症状

* Azure Front Door を経由しないでバックエンドに送信される通常の要求は成功します。 Azure Front Door を経由すると、503 エラー応答が返されます。
* Azure Front Door からのエラーは、通常約 30 秒後に表示されます。

### <a name="cause"></a>原因

この問題の原因は、次の 2 つのどちらかである可能性があります。
 
* バックエンドで、Azure Front Door から要求を受信するために構成したタイムアウト (既定値は 30 秒) よりも長い時間がかかっています。
* Azure Front Door からの要求に対して応答を送信するのに、タイムアウト値よりも長い時間がかかっています。 

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

* 要求を (Azure Front Door を経由せずに) バックエンドに直接送信します。 バックエンドからの応答に通常かかる時間を確認します。
* Azure Front Door 経由で要求を送信し、503 応答を受け取るかどうかを確認します。 そうでない場合は、タイムアウトの問題ではない可能性があります。 サポートにお問い合せください。
* Azure Front Door を経由したときに 503 エラー応答コードが生成される場合は、Azure Front Door の `sendReceiveTimeout` フィールドを構成します。 既定のタイムアウトは、最大 4 分 (240 秒) まで延長できます。 この設定は `backendPoolSettings` の下にあり、`sendRecvTimeoutSeconds` と呼ばれます。 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>カスタム ドメインに送信された要求で状態コード 400 が返される

### <a name="symptom"></a>症状

* Azure Front Door インスタンスを作成しましたが、ドメインまたはフロントエンド ホストへの要求で HTTP 400 の状態コードが返されます。
* カスタム ドメインに対して、構成したフロントエンド ホストへの DNS マッピングを作成しました。 しかし、カスタム ドメインのホスト名に要求を送信すると、HTTP 400 の状態コードが返されます。 構成したバックエンドにルーティングされているようには見えません。

### <a name="cause"></a>原因

この問題は、フロントエンド ホストとして追加されたカスタム ドメインに対してルーティング規則を構成しなかった場合に発生します。 そのフロントエンド ホストに対して明示的にルーティング規則を追加する必要があります。 Azure Front Door サブドメイン (*.azurefd.net) 下でフロントエンド ホストに対してルーティング規則が既に構成されている場合も、これに該当します。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

カスタム ドメインに対して、選択したバックエンド プールにトラフィックを転送するルーティング規則を追加します。

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door によって HTTP が HTTPS にリダイレクトされない

### <a name="symptom"></a>症状

Azure Front Door には HTTP を HTTPS にリダイレクトするルーティング規則がありますが、ドメインへのアクセスではプロトコルとして HTTP が維持されます。

### <a name="cause"></a>原因

この動作は、Azure Front Door のルーティング規則を正しく構成していない場合に発生する可能性があります。 基本的に、現在の構成は固有ではなく、規則が競合している可能性があります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>フロントエンドのホスト名の要求で状態コード 404 が返される

### <a name="symptom"></a>症状

フロントエンド ホスト、少なくとも 1 つ以上のバックエンドがあるバックエンド プール、フロントエンド ホストをバックエンド プールに接続するルーティング規則を構成して、Azure Front Door インスタンスを作成しました。 構成したフロントエンド ホストに要求を送信しても、コンテンツを利用できません。 その結果、要求に HTTP 404 の状態コードが返されます。

### <a name="cause"></a>原因

この現象には、以下のような複数の原因が考えられます。

* バックエンドが一般に公開されているバックエンドではなく、Azure Front Door から参照できません。
* バックエンドが正しく構成されていないため、Azure Front Door から間違った要求が送信されています。 つまり、バックエンドで受け入れられるのは HTTP のみで、かつ HTTPS が使用不可になっていません。 そのため、Azure Front Door から HTTPS 要求の転送が試みられます。
* バックエンドが、バックエンドへの要求で送信されたホスト ヘッダーを拒否しています。
* バックエンドの構成が、まだ完全にはデプロイされていません。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

* デプロイ時間を確認する:
   * 構成がデプロイされるまで少なくとも 10 分間待ったことを確認します。

* バックエンドの設定を確認する:
    * 要求のルーティング先となるバックエンド プールにアクセスします (ルーティング規則がどのように構成されているかによって異なります)。バックエンド ホストの種類とバックエンドのホスト名が正しいことを確認します。 バックエンドがカスタム ホストの場合は、スペルが正しく入力されていることを確認します。 

    * HTTP および HTTPS ポートを確認します。 多くの場合、80 と 443 が (それぞれ) 正しく、変更する必要はありません。 ただし、バックエンドの構成がこのようにはなっておらず、別のポートをリッスンしている場合もあります。

    * フロントエンド ホストのルーティング先のバックエンドに構成されているバックエンド ホスト ヘッダーを確認します。 多くの場合、このヘッダーはバックエンド ホスト名と同じである必要があります。 ただし、バックエンドが別のものを期待している場合、不正な値によってさまざまな HTTP 4xx の状態コードが発生する場合があります。 バックエンドの IP アドレスを入力した場合、バックエンドのホスト ヘッダーをバックエンドのホスト名に設定する必要があります。

* ルーティング規則の設定を確認する:
    * 問題のフロントエンド ホスト名からバックエンド プールにルーティングする、ルーティング規則に移動します。 要求の転送時に、許可されているプロトコルが正しく構成されていることを確認します。 **[受け入れ済みのプロトコル]** フィールドによって、Azure Front Door で受け入れられる要求が決定されます。 転送プロトコルによって、Azure Front Door でバックエンドに要求を転送するために使用されるプロトコルが決定されます。
      
      たとえば、バックエンドが HTTP 要求のみを許可している場合、次の構成が有効になります。
            
      * [accepted protocols] \(許可されているプロトコル\) は、HTTP および HTTPS です。 転送プロトコルは HTTP です。 HTTPS は許可されているプロトコルであるため、一致要求は機能しません。 要求が HTTPS として送信された場合、Azure Front Door から HTTPS を使用した転送が試みられます。

      * 受け入れられるプロトコルは HTTP です。 転送プロトコルは、一致要求または HTTP です。
    - **[Url の書き換え]** は、既定では無効化されています。 このフィールドは、バックエンドでホストされているリソースの利用可能にする範囲を限定したい場合にのみ使用します。 このフィールドを無効にすると、Azure Front Door で受信されたのと同じ要求パスが転送されます。 このフィールドを間違って構成する可能性があります。 その場合、Azure Front Door によって、利用できないバックエンドのリソースが要求されると、HTTP 404 の状態コードが返されます。

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>フロントエンドのホスト名の要求で状態コード 411 が返される

### <a name="symptom"></a>症状

Azure Front Door インスタンスを作成し、フロントエンド ホスト、少なくとも 1 つ以上のバックエンドがあるバックエンド プール、フロントエンド ホストをバックエンド プールに接続するルーティング規則を作成しました。 構成したフロントエンド ホストに要求を送信しても、HTTP 411 の状態コードが返されるのでコンテンツを利用できないようです。

これらの要求に対する応答には、説明文が記載された HTML エラー ページが応答本文に含まれる場合もあります。 (例: `HTTP Error 411. The request must be chunked or have a content length`)。

### <a name="cause"></a>原因

この現象には、以下のような複数の原因が考えられます。 全体的な理由は、HTTP 要求が RFC に完全に準拠していないことです。 

非準拠の例としては、`Content-Length` または `Transfer-Encoding` ヘッダーを使用せずに送信される `POST` 要求があります (たとえば、`curl -X POST https://example-front-door.domain.com` を使用)。 この要求は [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) で設定されている要件を満たしていません。 それは Azure Front Door により HTTP 411 応答でブロックされます。

この動作は、Azure Front Door の Web アプリケーション ファイアウォール (WAF) 機能とは別のものです。 現時点では、この動作を無効にする方法はありません。 WAF の機能が使用されていない場合でも、すべての HTTP 要求では要件が満たされている必要があります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

- 要求が、必要な RFC で規定されている要件に準拠していることを確認します。

- 要求への応答として返される HTML メッセージ本文をメモします。 多くの場合、メッセージ本文には、要求が "*どのように*" 非準拠であるかが説明されています。
