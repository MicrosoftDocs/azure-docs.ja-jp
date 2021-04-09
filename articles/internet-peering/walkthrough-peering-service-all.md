---
title: Peering Service パートナーのチュートリアル
titleSuffix: Azure
description: Peering Service パートナーのチュートリアル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 292c850f5352c80112e3f0bcc5c868f8128819be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592315"
---
# <a name="peering-service-partner-walkthrough"></a>Peering Service パートナーのチュートリアル

このセクションでは、Peering Service のための Direct ピアリングを有効にするためにプロバイダーが行うべき手順について説明します。

## <a name="create-direct-peering-connection-for-peering-service"></a>Peering Service のための Direct ピアリング接続を作成する
サービスプロバイダーは、 Peering Service をサポートする新しい Direct ピアリングを作成することにより、地理的なリーチを広げることができます。 このためには、以下を行う必要があります。
1. Peering Service パートナーになる (まだなっていない場合)
1. 指示に従い、[ポータルを使用して Direct ピアリングを作成または変更します](howto-direct-portal.md)。 高可用性の要件を満たしていることを確認してください。
1. 次に、ステップに従い、[ポータルを使用して Direct ピアリングでMicrospft Azure Peering Service を有効にします](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Peering Service にレガシの Direct ピアリング接続を使用する
レガシの Direct ピアリングを使用して Microsoft Azure Peering Service をサポートしたい場合は、以下を行う必要があります。
1. Peering Service パートナーになる (まだなっていない場合)
1. 指示に従い、[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換します](howto-legacy-direct-portal.md)。 必要に応じて、高可用性の要件を満たす追加の回線をオーダーします。
1. 次に、ステップに従い、[ポータルを使用して Direct ピアリングでMicrospft Azure Peering Service を有効にします](howto-peering-service-portal.md)。

## <a name="next-steps"></a>次のステップ

* [ピアリングポリシー](https://peering.azurewebsites.net/peering)の詳細を知る
* Microsoft との Direct ピアリングを設定する手順の詳細については、[Direct ピアリングのチュートリアル](walkthrough-direct-all.md)を参照してください。
* Microsoft との Exchange ピアリングを設定する手順の詳細については、[Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を参照してください。