---
title: Azure Functions のネットワークについてよく寄せられる質問
description: Azure Functions のネットワークについてよく寄せられる質問とシナリオ。
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592304"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions のネットワークについてよく寄せられる質問

この記事では、Azure Functions のネットワークについてよく寄せられる質問をまとめています。 より包括的な概要については、[Functions のネットワーク オプション](functions-networking-options.md)に関する記事を参照してください。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Functions で静的 IP を設定するにはどうすればいいですか?

関数に静的な受信/送信 IP アドレスを設定する主な方法は、App Service Environment で関数をデプロイすることです。 App Service Environment の使用の詳細については、まず「[App Service Environment で内部ロード バランサーを作成して使用する](../app-service/environment/create-ilb-ase.md)」の記事から参照してください。

また、仮想ネットワーク NAT ゲートウェイを使用して、制御するパブリック IP アドレスを介して送信トラフィックをルーティングすることもできます。 詳しくは、「[チュートリアル: Azure 仮想ネットワーク NAT ゲートウェイを使用して Azure Functions の送信 IP を制御する](functions-how-to-use-nat-gateway.md)」を参照してください。 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>自分の関数へのインターネット アクセスを制限するにはどうすればよいですか?

インターネット アクセスは、次のようにいくつかの方法で制限することができます。

* [IP 制限](../app-service/app-service-ip-restrictions.md):IP 範囲によって、関数アプリへの受信トラフィックを制限する。
    * IP 制限下で[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を構成できます。これにより関数は特定の仮想ネットワークから受信トラフィックのみを受け付けるよう制限できます。
* すべての HTTP トリガーを削除する。 一部のアプリケーションでは、単に HTTP トリガーを回避し、他のイベント ソースを使用して関数をトリガーするだけで十分です。

Azure portal エディターから実行中の関数に直接アクセスする必要がある点に留意してください。 Azure portal からのコードの変更では、ポータルの参照に使用しているデバイスでその IP を承認済み一覧に追加する必要があります。 ただし、適切にネットワーク制限されたプラットフォーム機能タブの下にある機能は引き続き使用できます。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>仮想ネットワークに対して自分の関数アプリを制限するにはどうすればよいですか?

[サービス エンドポイント](./functions-networking-options.md#use-service-endpoints)を使用して、関数アプリの **受信** トラフィックを仮想ネットワークに制限できます。 この構成により、関数アプリでインターネットに送信呼び出しを行うこともできます。

すべてのトラフィックが仮想ネットワークを通過するように関数を完全に制限するには、送信仮想ネットワーク統合または App Service Environment で[プライベート エンドポイント](./functions-networking-options.md#private-endpoint-connections)を使用することができます。 詳しくは、「[プライベート エンドポイントを使用して Azure Functions を Azure 仮想ネットワークに統合する](functions-create-vnet.md)」を参照してください。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>関数アプリから仮想ネットワーク内のリソースにアクセスするにはどうすればいいですか?

実行中の関数から仮想ネットワーク内のリソースにアクセスするには、仮想ネットワーク統合を使用します。 詳細については、[仮想ネットワーク統合](functions-networking-options.md#virtual-network-integration)に関する記事を参照してください。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>サービス エンドポイントで保護されたリソースにアクセスするにはどうすればよいですか?

仮想ネットワーク統合を使用すると、実行中の関数からサービスエンドポイントで保護されたリソースにアクセスすることができます。 詳細については、[仮想ネットワーク統合](functions-networking-options.md#virtual-network-integration)に関する記事を参照してください。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>仮想ネットワーク内のリソースから関数をトリガーするにはどうすればよいですか?

[サービス エンドポイント](./functions-networking-options.md#use-service-endpoints)または[プライベート エンドポイント接続](./functions-networking-options.md#private-endpoint-connections)を使用して、仮想ネットワークから HTTP トリガーを呼び出されるようにすることができます。 

Premium プラン、App Service プラン、または App Service Environment に関数アプリをデプロイすることによって、仮想ネットワーク内の他のすべてのリソースから関数をトリガーすることもできます。 詳細については、「[仮想ネットワーク トリガー (非 HTTP)](./functions-networking-options.md#virtual-network-triggers-non-http)」を参照してください。

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>仮想ネットワークで関数アプリをデプロイするにはどうすればよいですか?

全体が仮想ネットワーク内にある関数アプリを作成する唯一の方法は、App Service Environment にデプロイすることです。 内部ロード バランサーと App Service Environment の併用の詳細については、まず「[App Service Environment で内部ロード バランサーを作成して使用する](../app-service/environment/create-ilb-ase.md)」の記事から参照してください。

仮想ネットワークのリソースへの一方向のアクセスのみが必要なシナリオ、または緩くネットワークを分離する必要があるシナリオについては、[Functions のネットワークの概要](functions-networking-options.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

ネットワークと関数の詳細については、以下を参照してください。 

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [Azure Functions のネットワーク オプションに関する詳細情報](./functions-networking-options.md)
* [仮想ネットワーク統合と App Service および Functions に関する詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
