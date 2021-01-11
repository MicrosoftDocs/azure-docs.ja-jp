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
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 6864a854215d899043607b3d01cffbd343ee7751
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399516"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front Door での HTTP ヘッダー プロトコルのサポート
この記事では、呼び出しパスの各部で Front Door がサポートするプロトコルの概要を示します (画像を参照)。 以下のセクションでは、Front Door がサポートする HTTP ヘッダーについて詳しく説明します。

![Azure Front Door の HTTP ヘッダー プロトコル][1]

>[!IMPORTANT]
>この記事に記載されていない HTTP ヘッダーは、Front Door では認定されていません。

## <a name="client-to-front-door"></a>クライアントから Front Door
Front Door では、受信した要求のほとんどのヘッダーが変更なしで受け入れられます。 X-FD-* プレフィックスの付いたヘッダーを含め、一部の予約済みヘッダーは、送信された場合、受信した要求から削除されます。

## <a name="front-door-to-backend"></a>Front Door からバックエンド

Front Door では、制限により削除されない限り、受信した要求に存在するヘッダーが含められます。 Front Door では、次のヘッダーも追加されます。

| ヘッダー  | 例と説明 |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door は、クライアントの HTTP バージョンを追加し、その後ろに、Via ヘッダーの値として *Azure* を付けます。 このヘッダーは、クライアントの HTTP バージョンを示すと共に、Front Door がクライアントとバックエンドの間での要求の中間受信者となったことを示します。  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> 処理されている要求に関連付けられたクライアント IP アドレスを表します。 たとえば、プロキシから送信される要求では、最初の呼び出し元の IP アドレスを示すために、X-Forwarded-For ヘッダーが追加されることがあります。 |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> TCP 接続に関連付けられた、ソケットの IP アドレス (現在の要求の送信元) を表します。 要求のクライアント IP アドレスは、ユーザーが任意に上書きできるので、ソケットの IP アドレスと同じではない場合もあります。|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Front Door によって提供される要求を示す一意の参照文字列です。 これは、アクセス ログの検索に使用されるため、トラブルシューティングにおいて重要です。|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Front Door が要求ループの検出に使用するヘッダーであり、ユーザーはそれに対する依存関係を取得することはできません。 |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> X-Forwarded-For (XFF) HTTP ヘッダー フィールドは、HTTP プロキシまたはロード バランサーを経由して Web サーバーに接続しているクライアントの発信元 IP アドレスを識別することがよくあります。 既存の XFF ヘッダーがあった場合、Front Door はそのヘッダーにクライアント ソケット IP を追加するか、クライアント ソケット IP を使用した XFF ヘッダーを追加します。 |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> X-Forwarded-Host HTTP ヘッダー フィールドは、Host HTTP 要求ヘッダー内でクライアントによって要求された元のホストを識別するために一般的に使用される方法です。 これは、要求を処理するバックエンド サーバーによって Front Door からのホスト名が異なる場合があるからです。 |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> X-Forwarded-Proto HTTP ヘッダー フィールドは、HTTP 要求の発信元のプロトコルを識別するために使用されることがよくあります。Front Door は、構成に基づいて HTTPS を使用してバックエンドと通信することがあるからです。 これは、リバース プロキシへの要求が HTTP である場合でも当てはまります。 |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP ヘッダー フィールドは、Front Door からの正常性プローブを識別するために使用されます。 このヘッダーが 1 に設定されている場合、要求は正常性プローブです。 X-Forwarded-Host ヘッダー フィールドを使用して、特定の Front Door からの厳密なアクセスが必要な場合に使用できます。 |
|X-Azure-FDID | X-Azure-FDID ヘッダー: 437c82cd-360a-4a54-94c3-5ff707647783 </br> このフィールドには、受信した要求の送信元の Front Door を特定できる frontdoorID が含まれています。 このフィールドは、Azure Front Door Service によって設定されます。 | 

## <a name="front-door-to-client"></a>Front Door からクライアント

バックエンドから Front Door に送信されるすべてのヘッダーも、クライアントにそのまま渡されます。 Front Door からクライアントに送信されるヘッダーを次に示します。

| ヘッダー  | 例 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref:0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> これは、Front Door によって提供される要求を示す一意の参照文字列です。 これは、アクセス ログの検索に使用されるため、トラブルシューティングにおいて重要です。|

## <a name="next-steps"></a>次のステップ

- [Front Door を作成する](quickstart-create-front-door.md)
- [Front Door のしくみ](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
