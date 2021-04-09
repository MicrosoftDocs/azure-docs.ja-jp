---
title: Azure IoT Central でのアーキテクチャの概念 - スマート メーター | Microsoft Docs
description: この記事では、Azure IoT Central エネルギー アプリ テンプレートのアーキテクチャに関連する主要な概念を紹介します。
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 48dd95acf725e57d63f85c54c97b641935b049b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832370"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - スマート メーター アプリのアーキテクチャ

この記事では、スマート メーター監視アプリ テンプレートのアーキテクチャの概要について説明します。 次の図は、IoT Central プラットフォームを使用した Azure 上のスマート メーター アプリに一般的に使用されるアーキテクチャを示しています。

> [!div class="mx-imgBorder"]
> ![スマート メーターのアーキテクチャ](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

このアーキテクチャは、次のコンポーネントで構成されます。 一部のソリューションでは、ここに記載されているすべてのコンポーネントが必要ではない場合があります。

## <a name="smart-meters-and-connectivity"></a>スマート メーターと接続性 

スマート メーターは、すべてのエネルギー アセットの中でも最も重要なデバイスの 1 つです。 これを使ってエネルギー消費データを記録し、監視やその他のユース ケース (請求書や需要応答など) のために公共事業体に伝達できます。 メーターの種類に基づいて、ゲートウェイ、またはその他の中間デバイスや中間システム (エッジ デバイス、ヘッドエンド システムなど) を使用して IoT Central に接続できます。 直接接続できないデバイスを接続するには、IoT Central デバイス ブリッジを構築します。 IoT Central デバイス ブリッジはオープン ソース ソリューションであり、詳細については[こちら](../core/howto-build-iotc-device-bridge.md)で確認できます。 

## <a name="iot-central-platform"></a>IoT Central プラットフォーム

Azure IoT Central は、IoT ソリューションの構築を簡素化し、IoT の管理、運用、開発の負担とコストを削減するために役立つプラットフォームです。 IoT Central を使用すると、大規模なモノのインターネット (IoT) アセットを簡単に接続、監視、管理できます。 スマート メーターを IoT Central に接続すると、アプリ テンプレートにはデバイス モデル、コマンド、ダッシュボードなどの組み込みの機能が使用されます。 また、アプリ テンプレートには、ほぼリアルタイムのメーター データの監視、分析、ルール、視覚化など、ウォーム パス シナリオにも IoT Central ストレージが使用されます。 

## <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central を使用して構築する拡張オプション
IoT Central プラットフォームには、次の 2 つの拡張オプションがあります。継続的データ エクスポート (CDE) と API。 顧客とパートナーは、特定のニーズに合わせてこれらのオプションのいずれかを選択し、ソリューションをカスタマイズすることができます。 たとえば、あるパートナーは Azure Data Lake Storage (ADLS) を使用して CDE を構成しました。 バッチ処理、監査、報告などの目的で、長期的なデータ保持やその他のコールド パス ストレージ シナリオに ADLS が使用されています。 

## <a name="next-steps"></a>次のステップ

* アーキテクチャについて学習したので、次は[スマート メーター アプリを無料で作成します](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* IoT Central の詳細については、[IoT Central の概要](../index.yml)に関する記事を参照してください
