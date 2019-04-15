---
title: IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールを理解する | Microsoft Docs
description: IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールのアーキテクチャと機能を理解します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: c6ac3d9dbbb16caed51243fea852adea541b9f04
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862235"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge セキュリティ モジュール

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) では、エッジでのビジネス ワークフローを管理および実行するための強力な機能が提供されます。
IoT Edge は IoT 環境内で重要な役割を果たしているため、悪意のあるアクターの注意を特に引きます。

Azure Security Center (ASC) for IoT セキュリティ モジュールでは、IoT Edge デバイスの包括的なセキュリティ ソリューションが提供されます。
ASC for IoT モジュールでは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データが収集、集約、分析されて、アクションにつながるセキュリティ推奨事項とアラートに変換されます。

IoT デバイスに対する ASC for IoT セキュリティ エージェントと同様、ASC for IoT Edge モジュールもそのモジュール ツインによって高度なカスタマイズが可能です。
詳しくは、[エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

IoT Edge 用の ASC for IoT セキュリティ モジュールでは、次の機能が提供されます。

- 基になるオペレーティング システム (Linux) および IoT Edge コンテナー システムから、生のセキュリティ イベントを収集します。
  
  使用可能なセキュリティ データ コレクターについて詳しくは、[ASC for IoT エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

- IoT Edge のデプロイ マニフェストを分析します。

- [IoT Edge ハブ](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub)経由で送信されるメッセージに、未加工のセキュリティ イベントを集約します。

- セキュリティ モジュール ツインを使用して構成を削除します。

  詳しくは、[ASC for IoT エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

IoT Edge 用の ASC for IoT セキュリティ モジュールは、IoT Edge において特権モードで実行されます。
モジュールでオペレーティング システムおよび他の IoT Edge モジュールを監視できるためには、特権モードが必要です。

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

現在、IoT Edge 用の ASC for IoT セキュリティ モジュールは、Linux でのみ利用できます。

## <a name="next-steps"></a>次の手順

この記事では、IoT Edge 用の ASC for IoT セキュリティ モジュールのアーキテクチャと機能について説明しました。

ASC for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [IoT Edge 用のセキュリティ モジュール](how-to-deploy-edge.md)をデプロイする
- [セキュリティ モジュールを構成する](how-to-agent-configuration.md)方法を学習する
- ASC for IoT の[サービスの要件](service-prerequisites.md)を確認する
- [IoT Hub で ASC for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [ASC for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスについて確認する