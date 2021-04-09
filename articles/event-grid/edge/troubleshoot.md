---
title: トラブルシューティング - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge のトラブルシューティング。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171348"
---
# <a name="common-issues"></a>一般的な問題

ご使用の環境において Azure Event Grid on IoT Edge の使用に関して問題が発生した場合は、この記事を参考にしてトラブルシューティングを行い、問題を解決してください。

## <a name="view-event-grid-module-logs"></a>Event Grid モジュールのログを表示する

トラブルシューティングを行うには、Event Grid モジュールのログにアクセスする必要がある場合があります。 そのためには、モジュールがデプロイされている VM で次のコマンドを実行します。

Windows の場合:

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux の場合:

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>HTTPS 要求を送信できない

* 最初に、Event Grid モジュールで **inbound:serverAuth:tlsPolicy** が **strict** または **enabled** に設定されていることを確認します。

* モジュール間通信の場合は、ポート **4438** で呼び出しを行っていること、およびモジュールの名前がデプロイされているものと一致していることを確認します。 

  たとえば、Event Grid モジュールが **eventgridmodule** という名前でデプロイされた場合、URL は **https://eventgridmodule:4438** である必要があります。 大文字と小文字の区別とポート番号が正しいことを確認します。
    
* それが非 IoT モジュールからのものである場合は、デプロイ中に Event Grid ポートがホスト マシンにマップされていることを確認します。たとえば、次のようにします。

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>HTTP 要求を送信できない

* 最初に、Event Grid モジュールで **inbound:serverAuth:tlsPolicy** が **enabled** または **disabled** に設定されていることを確認します。

* モジュール間通信の場合は、ポート **5888** で呼び出しを行っていること、およびモジュールの名前がデプロイされているものと一致していることを確認します。 

  たとえば、Event Grid モジュールが **eventgridmodule** という名前でデプロイされた場合、URL は **http://eventgridmodule:5888** である必要があります。 大文字と小文字の区別とポート番号が正しいことを確認します。
    
* それが非 IoT モジュールからのものである場合は、デプロイ中に Event Grid ポートがホスト マシンにマップされていることを確認します。たとえば、次のようにします。

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>証明書チェーンが信頼されていない機関によって発行された

Event Grid モジュールは、既定で、IoT Edge セキュリティ デーモンによって発行された証明書を使用してクライアントを認証するように構成されます。 クライアントがこのチェーンをルートとする証明書を提示していることを確認します。

[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) の **IoTSecurity** クラスに、IoT Edge セキュリティ デーモンから証明書を取得し、それを使用して発信呼び出しを構成する方法が示されています。

非運用環境の場合は、クライアント認証をオフにするオプションがあります。 これを行う方法の詳細については、「[セキュリティと認証](security-authentication.md)」を参照してください。

## <a name="debug-events-not-received-by-subscriber"></a>サブスクライバーがデバッグ イベントを受信できない

この一般的な理由は、次のとおりです。

* イベントが正常に送信されていない。 Event Grid モジュールにイベントを送信したときに、HTTP StatusCode 200(OK) が返されているはずです。

* イベント サブスクリプションを調べて、次のことを確認します。
    * エンドポイント URL が有効である
    * サブスクリプションのフィルターが原因でイベントが "ドロップ" されていない。

* サブスクライバー モジュールが実行されているかどうかを確認します

* Event Grid モジュールがデプロイされている VM にログオンし、そのログを表示します。

* **broker:logDeliverySuccess=true** を設定し、Event Grid モジュールを再デプロイして要求を再試行することにより、配信ごとのログ記録を有効にします。 配信ごとのログ記録をオンにするとスループットと待ち時間に影響が及ぶ可能性があるため、デバッグが完了したら、これを **broker:logDeliverySuccess=false** に戻し、Event Grid モジュールを再デプロイすることをお勧めします。

* **metrics:reportertype=console** を設定してメトリックを有効にし、Event Grid モジュールを再デプロイします。 それ以降の操作のメトリックが Event Grid モジュールのコンソールに記録されるため、これを使用してさらにデバッグすることができます。 メトリックは、デバッグ時にのみオンにし、完了後は **metrics:reportertype=none** を設定し、Event Grid モジュールを再デプロイすることで、オフにすることをお勧めします。

## <a name="next-steps"></a>次のステップ

Event Grid on IoT Edge の使用に関する問題や提案を [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) でレポートします。