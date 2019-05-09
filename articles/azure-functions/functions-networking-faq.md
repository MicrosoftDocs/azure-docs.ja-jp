---
title: Azure Functions のネットワークについてよく寄せられる質問
description: Azure Functions のネットワークについてよく寄せられる質問とシナリオ。
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548643"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions のネットワークについてよく寄せられる質問

ネットワークについてよく寄せられる質問を次に示します。 より包括的な概要については、[Functions のネットワーク オプションのドキュメント](functions-networking-options.md)を参照してください

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Functions で静的 IP を設定するにはどうすればいいですか?

現時点で関数に静的な受信/送信 IP を設定する唯一の方法は、App Service Environment (ASE) で関数をデプロイすることです。 ASE の使用方法について詳しくは、最初にこちらの[ILB ASE の作成と使用](../app-service/environment/create-ilb-ase.md)に関するページを参照してください。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>自分の関数へのインターネット アクセスを制限するにはどうすればいいですか?

インターネット アクセスは、次に示すさまざまな方法で制限することができます。

* [IP 制限](../app-service/app-service-ip-restrictions.md): IP 範囲によって、関数アプリへの受信トラフィックを制限する。
* すべての HTTP トリガーを削除する。 一部のアプリケーションでは、単に HTTP トリガーを回避し、他のイベント ソースを使用して関数をトリガーするだけで十分です。

これを行う際に最も考慮しなければいけないのは、実行中の関数を使用するために、Azure portal エディターが直接アクセスする必要があるということです。 Azure portal からのコードの変更では、ポータルの参照に使用しているデバイスでその IP をホワイトリスト化する必要があります。 ただし、適切にネットワーク制限されたプラットフォーム機能タブの下にある機能は引き続き使用できます。

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>VNET に対して自分の関数アプリを制限するにはどうすればいいですか?

すべてのトラフィックが VNET を通るように関数を完全に制限するための唯一の方法は、内部で負荷分散された (ILB) App Service Environment (ASE) を使用することです。 このオプションにより、VNET 内の専用インフラストラクチャにサイトがデプロイされ、すべてのトリガーとトラフィックが VNET を通じて送信されます。 ASE の使用方法について詳しくは、最初にこちらの[ILB ASE の作成と使用](../app-service/environment/create-ilb-ase.md)に関するページを参照してください。

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>関数アプリから VNET 内のリソースにアクセスするにはどうすればいいですか?

VNET 内のリソースは、VNET 統合を使用して実行されている関数からアクセスできます。 詳細については、「[VNET integration](functions-networking-options.md#vnet-integration)」(VNET の統合) を参照してください

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>サービス エンドポイントで保護されたリソースにアクセスするにはどうすればいいですか?

新しい VNET 統合 (現在プレビュー段階です) を使用すると、実行中の関数からサービス エンドポイントで保護されたリソースにアクセスすることができます。 詳細については、「[preview VNET integration](functions-networking-options.md#preview-vnet-integration)」(VNET の統合のプレビュー) を参照してください。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>VNET 内のリソースから関数をトリガーするにはどうすればいいですか?

VNET 内のリソースから関数をトリガーする唯一の方法は、関数アプリを App Service Environment にデプロイすることです。 ASE の使用の詳細については、[ILB ASE の作成と使用](../app-service/environment/create-ilb-ase.md)に関するページを参照してください。


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>VNET で関数アプリをデプロイするにはどうすればいいですか?

VNET の内部のみに関数アプリを作成する唯一の方法は、App Service Environment にデプロイすることです。ILB ASE の使用について詳しくは、最初にこちらの [ILB ASE の作成と使用](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)に関するページを参照してください。

VNET のリソースへの一方向のアクセスのみが必要なシナリオ、または緩くネットワークを分離する必要があるシナリオについては、[Functions のネットワークの概要](functions-networking-options.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

ネットワークと関数の詳細については、以下を参照してください。 

* [VNET 統合の入門チュートリアル](./functions-create-vnet.md)
* [関数のネットワーク オプションの詳細については、こちらをご覧ください](./functions-networking-options.md)
* [App Service または Functions との VNET 統合についての詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure での VNET についての詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
