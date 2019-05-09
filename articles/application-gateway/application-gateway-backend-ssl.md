---
title: Azure Application Gateway でのエンド ツー エンド SSL の有効化 | Microsoft Docs
description: このページでは、Application Gateway によるエンドツーエンド SSL のサポートの概要を示します。
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: amsriva
ms.openlocfilehash: 856f23de8a8772255f570a923ecf1708dc819bb5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778350"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Application Gateway でのエンド ツー エンド SSL の概要

アプリケーション ゲートウェイは、ゲートウェイの SSL ターミネーションをサポートします。通常、トラフィックは、その後、暗号化されないままバックエンド サーバーに渡されます。 この機能により、Web サーバーは、負荷の大きい暗号化と復号化のオーバーヘッドから開放されます。 ただし、暗号化されていない通信をバックエンド サーバーで受け入れていない顧客もいます。 このように暗号化されていない通信が発生する原因としては、セキュリティの要件とコンプライアンスの要件が考えられます。また、アプリケーションでセキュリティで保護された接続以外は受け入れられないこともあります。 このようなアプリケーションのために、アプリケーション ゲートウェイでは、エンド ツー エンド SSL 暗号化がサポートされています。

## <a name="overview"></a>概要

エンド ツー エンド SSL により、暗号化されたバックエンドに機密データを安全に送信しながら、アプリケーション ゲートウェイによって提供されるレイヤー 7 の負荷分散機能の利点を引き続き利用できます  (レイヤー 7 の負荷分散機能には、Cookie ベースのセッション アフィニティ、URL ベースのルーティング、サイトに基づくルーティングのサポート、X-Forwarded-* ヘッダーを挿入する機能などがあります)。

エンド ツー エンド SSL 通信モードが構成されている場合、アプリケーション ゲートウェイによって SSL セッションがゲートウェイで終了され、ユーザー トラフィックの暗号化が解除されます。 次に、構成済みのルールが適用され、トラフィックのルーティング先になる適切なバックエンド プール インスタンスが選択されます。 バックエンドに要求を送信する前に、Application Gateway によってバックエンド サーバーへの新しい SSL 接続が開始され、バックエンド サーバーの公開キー証明書を使用してデータが再暗号化されます。 エンド ツー エンド SSL は BackendHTTPSetting のプロトコル設定を HTTPS に設定することによって有効になります。この設定は次にバックエンド プールに適用されます。 エンド ツー エンド SSL が有効になったバックエンド プール内の各バックエンド サーバーは、セキュリティで保護された通信を許可するように証明書で構成する必要があります。

![エンド ツー エンド SSL のシナリオ][1]

この例では、TLS1.2 を使用する要求が、エンド ツー エンド SSL を使用して Pool1 のバックエンド サーバーにルーティングされます。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>エンド ツー エンド SSL と証明書のホワイト リスト

Application Gateway は、既知のバックエンド インスタンスのみと通信します。これらのインスタンスでは、アプリケーション ゲートウェイに関する証明書がホワイトリスト登録されています。 証明書のホワイトリスト登録を有効にするには、(ルート証明書ではなく) バックエンド サーバーの証明書の公開キーをアプリケーション ゲートウェイにアップロードする必要があります。 ホワイトリスト登録された既知のバックエンドへの接続のみが許可され、 それ以外ではゲートウェイ エラーが発生します。 自己署名証明書はテストのみを目的とするため、運用環境のワークロードで使用することはお勧めできません。 このような証明書は、使用する前に、上記の手順で説明したとおりに、アプリケーション ゲートウェイでホワイトリスト登録する必要があります。

## <a name="next-steps"></a>次の手順

エンド ツー エンド SSL について学習したので、[Application Gateway でのエンド ツー エンド SSL の有効化](application-gateway-end-to-end-ssl-powershell.md)に進んで、エンド ツー エンド SSL を使用する Application Gateway を作成します。

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png
