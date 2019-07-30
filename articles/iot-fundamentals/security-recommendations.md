---
title: Azure IoT のセキュリティに関する推奨事項 | Microsoft Docs
description: この記事は、Azure IoT Hub ソリューションのセキュリティを確保するための追加手順をまとめたものです。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722859"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure のモノのインターネット (IoT) デプロイのセキュリティに関する推奨事項

この記事には、Azure IoT サービスのセキュリティに関する推奨事項が含まれています。 これらの推奨事項の実装は、Azure IoT の顧客としてのセキュリティに関する義務を果たすのに役立ち、IoT ソリューションの全体的なセキュリティが向上します。 Azure IoT によって提供される固有のセキュリティ機能について詳しくは、[徹底的な IoT セキュリティ](iot-security-ground-up.md)に関するページを参照してください。

## <a name="general"></a>全般

| 推奨 | 説明 |
|-|-|
| 常に最新の状態に保つ | サポート対象プラットフォーム、プログラミング言語、プロトコル、およびフレームワークの最新バージョンを使用します。 |
| 認証キーを安全に保管する | デプロイ後にデバイス ID とその認証キーを物理的に安全に保管します。 これにより、悪意のあるデバイスが登録済みデバイスになりすますことを防げます。 |
| 可能な場合はデバイス SDK を使用する | デバイス SDK では、暗号化認証などのさまざまなセキュリティ機能を実装し、堅牢で安全なデバイス アプリケーションの開発を支援します。 詳細については、「[Azure IoT Hub SDK の概要と使用方法](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)」を参照してください。 |


## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| 推奨 | 説明 |
|-|-|
| ハブのアクセス制御を定義する | 機能に基づき、IoT Hub ソリューションで各コンポーネントに付与される[アクセス権の種類を理解して定義](iot-security-deployment.md#securing-the-cloud)します。 許可される権限は、*Registry Read*、*RegistryReadWrite*、*ServiceConnect*、および *DeviceConnect* です。 既定の [IoT ハブの共有アクセス ポリシー](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)は、各コンポーネントの権限をそのロールで定義するのにも役立ちます。 |
| バックエンド サービスのアクセス制御を定義する | IoT Hub ソリューションによって取り込まれたデータは、[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)、[Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)、[App Service](https://docs.microsoft.com/azure/app-service/)、[Logic Apps](https://docs.microsoft.com/azure/logic-apps/)、および [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) などの他の Azure サービスで使用することができます。 これらのサービスに関する文書化された適切なアクセス権限を必ず理解し、許可してください。 |


## <a name="data-protection"></a>データ保護

| 推奨 | 説明 |
|-|-|
| デバイス認証をセキュリティで保護する | [一意の ID キーまたはセキュリティ トークン](iot-security-deployment.md#iot-hub-security-tokens)、あるいは各デバイスの[デバイス上の X.509 証明書](iot-security-deployment.md#x509-certificate-based-device-authentication)を使用して、デバイスと IoT ハブ間のセキュリティで保護された通信を確保します。 適切な方法を使って、[選択されたプロトコル (MQTT、AMQP、または HTTPS) に基づいてセキュリティ トークンを使用](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)します。 |
| デバイスの通信をセキュリティで保護する | IoT Hub では、バージョン 1.2、1.2、および 1.0 がサポートされる、トランスポート層セキュリティ (TLS) 標準を使用して、デバイスへの接続をセキュリティで保護します。 最大のセキュリティを確保するには、[TLS 1.2](https://tools.ietf.org/html/rfc5246) を使用します。 |
| サービスの通信をセキュリティで保護する | IoT Hub では、[Azure Storage](/azure/storage/) または [Event Hubs](/azure/event-hubs) (TLS プロトコルのみを使用) などのバックエンド サービスに接続するためのエンドポイントが提供され、エンドポイントは非暗号化チャネルでは公開されません。 このデータが格納または分析のためにこれらのバックエンド サービスに達したら、必ず、そのサービスに適したセキュリティと暗号化の方法を採用し、バックエンドの機密情報を保護するようにしてください。 |


## <a name="networking"></a>ネットワーク

| 推奨 | 説明 |
|-|-|
| デバイスへのアクセスを保護する | 望ましくないアクセスを回避するために、デバイスのハードウェア ポートを最小限に抑えます。 さらに、デバイスの物理的な改ざんを防止または検出するためのメカニズムを構築します。 詳細については、[IoT セキュリティのベスト プラクティス](iot-security-best-practices.md)に関するページを参照してください。 |
| セキュリティで保護されたハードウェアを構築する | 暗号化されたストレージ、つまり、トラステッド プラットフォーム モジュール (TPM) などのセキュリティ機能を組み込み、デバイスとインフラストラクチャをより安全に保ちます。 デバイスのオペレーティング システムとドライバーを常に最新バージョンにアップグレードし、スペースに空きがある場合は、ウイルス対策およびマルウェア対策機能をインストールします。 [IoT セキュリティのアーキテクチャ](iot-security-architecture.md)に関するページをお読みになり、これがいくつかのセキュリティ脅威を軽減するのにどのように役立つかを理解してください。 |


## <a name="monitoring"></a>監視

| 推奨 | 説明 |
|-|-|
| デバイスへの未承認アクセスを監視する |  デバイスまたはそのポートのセキュリティ違反または物理的な改ざんを監視するには、デバイスのオペレーティング システムのログ機能を使用します。 |
| クラウドから IoT ソリューションを監視する | [Azure Monitor のメトリック](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)を使用して、IoT Hub ソリューションの全体的な正常性を監視します。 |
| 診断を設定する | ソリューションのイベントをログに記録してから、パフォーマンスを視覚化するために診断ログを Azure Monitor に送信することで、操作を注意深く観察します。 詳細については、[IoT ハブでの問題の監視と診断](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)に関するページを参照してください。 |

## <a name="next-steps"></a>次の手順

Azure IoT に関する高度なシナリオでは、追加のセキュリティ要件を考慮する必要がある場合があります。 詳細なガイダンスについては、[IoT セキュリティのアーキテクチャ](iot-security-architecture.md)に関するページを参照してください。

