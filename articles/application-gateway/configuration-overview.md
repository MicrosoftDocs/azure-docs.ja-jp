---
title: Azure Application Gateway 構成の概要
description: この記事では、Azure Application Gateway のコンポーネントを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652012"
---
# <a name="application-gateway-configuration-overview"></a>アプリケーション ゲートウェイ構成の概要

Azure Application Gateway は、さまざまなシナリオに合わせて多様な方法で構成できるいくつかのコンポーネントで構成されています。 この記事では、各コンポーネントの構成方法について説明します。

![Application Gateway コンポーネントのフロー チャート](./media/configuration-overview/configuration-overview1.png)

この画像は、3 つのリスナーがあるアプリケーションを示しています。 最初の 2 つのリスナーは、それぞれ `http://acme.com/*` と `http://fabrikam.com/*` のマルチサイト リスナーです。 いずれもポート 80 でリッスンします。 3 つ目は、エンド ツー エンド トランスポート層セキュリティ (TLS) ターミネーション (以前は Secure Sockets Layer (SSL) ターミネーションと呼ばれていた) を持つ基本リスナーです。

## <a name="infrastructure"></a>インフラストラクチャ

Application Gateway のインフラストラクチャには、仮想ネットワーク、サブネット、ネットワーク セキュリティ グループ、ユーザー定義ルートが含まれます。

詳細については、「[Application Gateway の構成の概要](configuration-infrastructure.md)」を参照してください。



## <a name="front-end-ip-address"></a>フロントエンド IP アドレス

アプリケーション ゲートウェイは、パブリック IP アドレス、プライベート IP アドレス、またはその両方を持つように構成できます。 パブリック IP が必要となるのは、インターネットに接続している仮想 IP (VIP) を介してインターネット上でクライアントがアクセスする必要があるバックエンドをホストするときです。

詳細については、「[Application Gateway のフロントエンド IP アドレスの構成](configuration-front-end-ip.md)」を参照してください。

## <a name="listeners"></a>リスナー

リスナーは、ポート、プロトコル、ホストおよび IP アドレスを使用して着信接続要求をチェックする論理エンティティです。 リスナーを構成するときは、ゲートウェイの着信要求の対応する値と同じ値をここに入力する必要があります。

詳細については、「[Application Gateway のリスナーの構成](configuration-listeners.md)」を参照してください。

## <a name="request-routing-rules"></a>要求ルーティング規則

Azure portal を使用してアプリケーション ゲートウェイを作成するときに、既定の規則 (*rule1*) を作成します。 この規則によって、既定のリスナー (*appGatewayHttpListener*) が既定のバックエンド プール (*appGatewayBackendPool*) と既定のバックエンド HTTP 設定 (*appGatewayBackendHttpSettings*) にバインドされます。 ゲートウェイを作成した後で、既定の規則の設定の編集や新しい規則の作成を行うことができます。

詳細については、「[Application Gateway リクエストのルーティング規則](configuration-request-routing-rules.md)」を参照してください。

## <a name="http-settings"></a>HTTP 設定

アプリケーション ゲートウェイは、ここで指定した構成を使用してトラフィックをバックエンド サーバーにルーティングします。 HTTP 設定を作成したら、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。

詳細については、「[Application Gateway の HTTP 設定の構成](configuration-http-settings.md)」を参照してください。

## <a name="back-end-pool"></a>バックエンド プール

バックエンド プールには、4 種類のバックエンド メンバー (特定の仮想マシン、仮想マシン スケール セット、IP アドレス/FQDN、または App Service) を指定できます。 

バックエンド プールを作成した後で、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。 アプリケーション ゲートウェイ上の各バックエンド プールに対して正常性プローブを構成する必要もあります。 要求のルーティング規則の条件が満たされると、アプリケーション ゲートウェイは、トラフィックを対応するバックエンド プール内の正常なサーバー (正常性プローブによって判別) に転送します。

## <a name="health-probes"></a>正常性プローブ

アプリケーション ゲートウェイは、既定でバック エンドのすべてのリソースの正常性を監視します。 ただし、正常性の監視をきめ細かく制御するには、バックエンドの HTTP 設定ごとにカスタム プローブを作成することを強くお勧めします。 カスタム プローブを構成する方法については、「[カスタムの正常性プローブの設定](application-gateway-probe-overview.md#custom-health-probe-settings)」を参照してください。

> [!NOTE]
> カスタムの正常性プローブを作成したら、バックエンド HTTP 設定に関連付ける必要があります。 対応する HTTP 設定が、規則を使用してリスナーに明示的に関連付けられていない限り、カスタム プローブはバックエンド プールの正常性を監視しません。

## <a name="next-steps"></a>次のステップ

Application Gateway コンポーネントについて学習したので、次は以下を行うことができます。

- [Azure portal 上でアプリケーション ゲートウェイを作成する](quick-create-portal.md)
- [PowerShell を使用して Application Gateway を作成する](quick-create-powershell.md)
- [Azure CLI を使用してアプリケーション ゲートウェイを作成する](quick-create-cli.md)
