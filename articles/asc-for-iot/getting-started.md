---
title: Azure Security Center (ASC) for IoT プレビューの使用開始 | Microsoft Docs
description: Azure Security Center for IoT の機能とサービスの基本的なワークフローへの理解を深めます。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: aac15d766439a725f593ca421cbdc6da496f29f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862694"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Azure Security Center for IoT の使用を開始する 

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Security Center (ASC) for IoT サービスのさまざまな構成要素、および[サービスの有効化](quickstart-onboard-iot-hub.md)を開始する方法について説明します。 

ASC for IoT は、IoT Hub の構成、デバイス ID、およびハブとデバイス間の通信パターンのセキュリティ分析を提供するために、IoT Hub にシームレスに統合できます。
セキュリティ機能を強化するために、ASC for IoT は、IoT デバイスからのエージェント ベースの収集を提供します。

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC for IoT のシームレスな IoT Hub 統合

個々の IoT デバイスの保護を試行するときに、デバイスからデータを直接収集する機能、またはネットワークから収集する機能が必要になります。 この取り組みをサポートするために、ASC for IoT では、フットプリントの小さいセキュリティ エージェントによる対策を提供し、デバイスの監視と強化を行います。

ASC for IoT プレビューでは、Linux および Windows のセキュリティ エージェントの参照アーキテクチャが C# と C の両方で提供されています。
エージェントは、デバイスのオペレーティング システムからの未加工イベント収集、コストを削減するためのイベント集計、およびデバイス モジュール ツインの構成を処理します。
セキュリティー メッセージは、IoT Hub を介して ASC for IoT 分析サービスへ送信されます。

## <a name="asc-for-iot-basics"></a>ASC for IoT の基本

IoT デバイスと環境の要件に最適なワークフロー シナリオを選択します。

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>ASC for IoT のシームレスな IoT Hub 統合を開始する 

>[!Note]
>このワークフローでは、ASC for IoT セキュリティ エージェントを使用せずに、サービスを使用することができます。 

デバイス ID 管理およびデバイスとクラウド間の通信パターンの監視を有効にするには、次の基本的なワークフローをテストとサービスの開始に使用します。 

1. [IoT Hub で ASC for IoT を有効](quickstart-onboard-iot-hub.md)にします。
1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)します。
1. デバイス用の [azureiotsecurity セキュリティ モジュールを作成](quickstart-create-security-twin.md)します。 
1. [カスタム アラート](quickstart-create-custom-alerts.md)を使用して、デバイスとシステムの通常の動作を定義します。 
1. サービスとデバイスの状態を確認するためにシステムのテストを実行します。 
1. IoT Hub を使用して、[アラート](concept-security-alerts.md)、[推奨事項](concept-recommendations.md)、および [Log Analytics を使用した詳細情報](how-to-security-data-access.md)を確認します。 


### <a name="get-started-with-asc-for-iot-security-agents"></a>ASC for IoT セキュリティ エージェントを使用して作業を開始する

次の基本的なワークフローを使用して、ASC for IoT の強化されたセキュリティ機能 (リモート接続、アクティブ アプリケーション、ログイン イベント、および OS 構成のベスト プラクティスの監視など) を活用し、サービスのテストと有効化を行います。 

1. [IoT Hub で ASC for IoT サービスを有効化](quickstart-onboard-iot-hub.md)します。
1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)します。
1. デバイス用の [azureiotsecurity セキュリティ モジュール](quickstart-create-security-twin.md)を作成します。
1. 実際のデバイスにインストールするのではなく、Azure シミュレート デバイスにエージェントをインストールするには、使用可能なゾーンで[新しい Azure Virtual Machine (VM ) を作成](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)します。 
1. IoT デバイスまたは新しい VM 上に [ASC for IoT セキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。
1. 無害な攻撃のシミュレーションを実行するには、[trigger_events](https://aka.ms/iot-security-github-trigger-events) の手順に従います。
1. 前の手順のシミュレートした攻撃への応答で ASC for IoT アラートを確認します。 スクリプトを実行した 5 分後に確認を開始してください。
1. IoT Hub を使用して、[アラート](concept-security-alerts.md)、[推奨事項](concept-recommendations.md)、および [Log Analytics を使用した詳細情報](how-to-security-data-access.md)を確認します。 

## <a name="next-steps"></a>次の手順

- [ASC for IoT を有効にする](quickstart-onboard-iot-hub.md)
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
