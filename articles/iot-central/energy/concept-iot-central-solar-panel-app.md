---
title: Azure IoT Central でのアーキテクチャの概念 - ソーラー パネル | Microsoft Docs
description: この記事では、Azure IoT Central ソーラー パネル監視アプリのアーキテクチャに関連する主要な概念を紹介します。
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1efb693e1ad9b9e9bfdbeafa27e9cc75c645baf4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834291"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - ソーラー パネル アプリのアーキテクチャ

この記事では、ソーラー パネル監視アプリ テンプレートのアーキテクチャの概要について説明します。 次の図は、IoT Central プラットフォームを使用した Azure 上のソーラー パネル アプリに一般的に使用されるアーキテクチャを示しています。

> [!div class="mx-imgBorder"]
> ![スマート メーターのアーキテクチャ](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

このアーキテクチャは、次のコンポーネントで構成されます。 アプリケーションの中には、以下に一覧表示するすべてのコンポーネントを必要としないものもあります。

## <a name="solar-panels-and-connectivity"></a>ソーラー パネルと接続

ソーラー パネルは、再生可能エネルギーの重要なソースの 1 つです。 ソーラー パネルの種類と設定に応じて、ゲートウェイまたは他の中間デバイスや独自のシステムを使用して接続できます。 直接接続できないデバイスを接続するように IoT Central デバイス ブリッジを構築する必要がある場合があります。 IoT Central デバイス ブリッジはオープン ソース ソリューションであり、詳細については[こちら](../core/howto-build-iotc-device-bridge.md)で確認できます。 

## <a name="iot-central-platform"></a>IoT Central プラットフォーム
Azure IoT Central は、IoT ソリューションの構築を簡素化し、IoT の管理、運用、開発の負担とコストを削減するために役立つプラットフォームです。 IoT Central を使用すると、大規模なモノのインターネット (IoT) アセットを簡単に接続、監視、管理できます。 ソーラー パネルを IoT Central に接続すると、アプリ テンプレートにはデバイス モデル、コマンド、ダッシュボードなどの組み込みの機能が使用されます。 また、アプリ テンプレートには、ほぼリアルタイムのメーター データの監視、分析、ルール、視覚化など、ウォーム パス シナリオにも IoT Central ストレージが使用されます。


## <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central を使用して構築する拡張オプション
IoT Central プラットフォームには、次の 2 つの拡張オプションがあります。継続的データ エクスポート (CDE) と API。 顧客とパートナーは、特定のニーズに合わせてこれらのオプションのいずれかを選択し、ソリューションをカスタマイズすることができます。 たとえば、あるパートナーは Azure Data Lake Storage (ADLS) を使用して CDE を構成しました。 バッチ処理、監査、報告などの目的で、長期的なデータ保持やその他のコールド パス ストレージ シナリオに ADLS が使用されています。 

## <a name="next-steps"></a>次のステップ

* アーキテクチャについて学習したので、次は[ソーラー パネル アプリを無料で作成](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)します。
* IoT Central の詳細については、[IoT Central の概要](../index.yml)に関する記事を参照してください
