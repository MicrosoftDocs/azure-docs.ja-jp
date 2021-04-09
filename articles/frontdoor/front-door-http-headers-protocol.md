---
title: Azure Front Door での HTTP ヘッダー プロトコルのサポート | Microsoft Docs
description: この記事では、Front Door がサポートする HTTP ヘッダー プロトコルについて説明します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 7f40b48473c04238d504288307039948fcacf90a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511146"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front Door での HTTP ヘッダー プロトコルのサポート
この記事では、呼び出しパスの各部で Front Door がサポートするプロトコルの概要を示します (画像を参照)。 以下のセクションでは、Front Door がサポートする HTTP ヘッダーについて詳しく説明します。

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door の HTTP ヘッダー プロトコル":::

>[!IMPORTANT]
>この記事に記載されていない HTTP ヘッダーは、Front Door では認定されていません。

## <a name="client-to-front-door"></a>クライアントから Front Door
Front Door では、受信した要求のほとんどのヘッダーが変更なしで受け入れられます。 X-FD-* プレフィックスの付いたヘッダーを含め、一部の予約済みヘッダーは、送信された場合、受信した要求から削除されます。

## <a name="front-door-to-backend"></a>Front Door からバックエンド

Front Door では、制限により削除されない限り、受信した要求のヘッダーが含められます。 Front Door では、次のヘッダーも追加されます。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door は、クライアントの HTTP バージョンを追加し、その後ろに、Via ヘッダーの値として *Azure* を付けます。 このヘッダーは、クライアントの HTTP バージョンを示すと共に、Front Door がクライアントとバックエンドの間での要求の中間受信者となったことを示します。  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> 処理されている要求に関連付けられたクライアント IP アドレスを表します。 たとえば、プロキシから送信される要求では、最初の呼び出し元の IP アドレスを示すために、X-Forwarded-For ヘッダーが追加されることがあります。 |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> TCP 接続に関連付けられた、ソケットの IP アドレス (現在の要求の送信元) を表します。 要求のクライアント IP アドレスは、ユーザーが任意に上書きできるので、ソケットの IP アドレスと同じではない場合もあります。|
| X-Azure-Ref | *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door によって提供される要求を示す一意の参照文字列です。 これは、アクセス ログの検索に使用されるため、トラブルシューティングにおいて重要です。|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Front Door が要求ループの検出に使用するヘッダーであり、ユーザーはそれに対する依存関係を取得することはできません。 |
| X-Azure-FDID | *X-Azure-FDID:55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> 特定の Front Door リソースからの要求を識別する参照文字列です。 この値は、Azure portal で確認することも、管理 API を使用して取得することもできます。 このヘッダーを IP ACL と組み合わせて使用して、特定の Front Door リソースからの要求のみを受け入れるようにエンドポイントをロック ダウンすることができます。 [詳細](front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)については、FAQ を参照してください。 |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> X-Forwarded-For (XFF) HTTP ヘッダー フィールドは、HTTP プロキシまたはロード バランサーを経由して Web サーバーに接続しているクライアントの発信元 IP アドレスを識別することがよくあります。 既存の XFF ヘッダーがあった場合、Front Door はそのヘッダーにクライアント ソケット IP を追加するか、クライアント ソケット IP を使用した XFF ヘッダーを追加します。 |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> X-Forwarded-Host HTTP ヘッダー フィールドは、Host HTTP 要求ヘッダー内でクライアントによって要求された元のホストを識別するために一般的に使用される方法です。 これは、要求を処理するバックエンド サーバーによって Front Door からのホスト名が異なる場合があるからです。 |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> X-Forwarded-Proto HTTP ヘッダーフィールドは、HTTP 要求の発信元のプロトコルを識別するために使用されることがよくあります。 Front Door は構成に基づいて、HTTPS を使用してバックエンドと通信する場合があります。 これは、リバース プロキシへの要求が HTTP である場合でも当てはまります。 |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP ヘッダー フィールドは、Front Door からの正常性プローブを識別するために使用されます。 このヘッダーが 1 に設定されている場合、要求は正常性プローブです。 X-Forwarded-Host ヘッダー フィールドを使用して、特定の Front Door からの厳密なアクセスが必要な場合に使用できます。 |
| X-Azure-FDID | *X-Azure-FDID ヘッダー: 437c82cd-360a-4a54-94c3-5ff707647783* </br> このフィールドには、受信した要求の送信元の Front Door を特定できる frontdoorID が含まれています。 このフィールドは、Azure Front Door Service によって設定されます。 | 

## <a name="front-door-to-client"></a>Front Door からクライアント

バックエンドから Front Door に送信されるすべてのヘッダーも、クライアントにそのまま渡されます。 Front Door からクライアントに送信されるヘッダーを次に示します。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> これは Front Door によって処理される要求を識別する一意の参照文字列です。アクセス ログの検索に使用されるため、トラブルシューティングには非常に重要です。|
| X-Cache | *X-Cache:TCP_HIT* </br> このヘッダーは、要求のキャッシュの状態を示します。これにより、応答のコンテンツが Front Door のキャッシュから提供されているかどうかを識別できます。 |

"X-Azure-DebugInfo:1" 要求ヘッダーを送信して、次の省略可能な応答ヘッダーを有効にする必要があります。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode:503* </br> このヘッダーには、バックエンドによって返される HTTP 状態コードが含まれています。 このヘッダーを使用すると、バックエンド ログを経由せずにバックエンドで実行中のアプリケーションによって返される HTTP 状態コードを識別できます。 この状態コードは、Front Door によってクライアントに送信される応答の HTTP 状態コードとは異なる場合があります。 このヘッダーを使用すると、バックエンドが誤動作しているかどうか、または Front Door サービスに問題があるかどうかを判断できます。 |
| X-Azure-InternalError | このヘッダーには、要求を処理するときに Front Door に発生するエラー コードが含まれます。 このエラーは、問題が Front Door サービス/インフラストラクチャの内部にあることを示します。 サポートに問題を報告してください。  |
| X-Azure-ExternalError | *X-Azure-ExternalError:0x830c1011, The certificate authority is unfamiliar.* </br> このヘッダーは、要求を処理するためにバックエンド サーバーへの接続を確立している間に Front Door サーバーに発生するエラー コードを示します。 このヘッダーは、Front Door とバックエンド アプリケーションの間の接続に関する問題を特定するのに役立ちます。 このヘッダーには、バックエンドに対する接続の問題を特定するのに役立つ詳細なエラー メッセージが含まれます (たとえば、DNS 解決、無効な証明書など)。 |

## <a name="next-steps"></a>次のステップ

- [Front Door を作成する](quickstart-create-front-door.md)
- [Front Door のしくみ](front-door-routing-architecture.md)
