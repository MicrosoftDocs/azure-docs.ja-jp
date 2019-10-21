---
title: IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールを理解する | Microsoft Docs
description: IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールのアーキテクチャと機能を理解します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315886"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge セキュリティ モジュール

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) では、エッジでのビジネス ワークフローを管理および実行するための強力な機能が提供されます。
IoT Edge は IoT 環境内で重要な役割を果たしているため、悪意のあるアクターの注意を特に引きます。

Azure Security Center for IoT セキュリティ モジュールにより、IoT Edge デバイスの包括的なセキュリティ ソリューションが提供されます。
Azure Security Center for IoT モジュールでは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データが収集、集約、分析されて、アクションにつながるセキュリティ推奨事項とアラートに変換されます。

IoT デバイスに対する Azure Security Center for IoT セキュリティ エージェントと同様、Azure Security Center for IoT Edge モジュールもそのモジュール ツインによって高度なカスタマイズが可能です。
詳しくは、[エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールでは、次の機能が提供されます。

- 基になるオペレーティング システム (Linux) および IoT Edge コンテナー システムから、生のセキュリティ イベントを収集します。
  
  使用可能なセキュリティ データ コレクターの詳細については、[Azure Security Center for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- IoT Edge のデプロイ マニフェストを分析します。

- [IoT Edge ハブ](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)経由で送信されるメッセージに、未加工のセキュリティ イベントを集約します。

- セキュリティ モジュール ツインを使用して構成を削除します。

  詳細については、[Azure Security Center for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールは、IoT Edge において特権モードで実行されます。
モジュールでオペレーティング システムおよび他の IoT Edge モジュールを監視できるためには、特権モードが必要です。

## <a name="module-supported-platforms"></a>モジュールのサポート対象プラットフォーム

現在、IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールは、Linux でのみ利用できます。 

## <a name="next-steps"></a>次の手順

この記事では、IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールのアーキテクチャと機能について説明しました。

Azure Security Center for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [IoT Edge 用のセキュリティ モジュール](how-to-deploy-edge.md)をデプロイする
- [セキュリティ モジュールを構成する](how-to-agent-configuration.md)方法を学習する
- Azure Security Center for IoT [サービスの前提条件](service-prerequisites.md)を確認する
- [IoT Hub で Azure Security Center for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法を学習する
- [Azure Security Center for IoT に関してよく寄せられる質問](resources-frequently-asked-questions.md)から、サービスについて確認する