---
title: Azure Percept セキュリティの概要
description: Azure Percept セキュリティの詳細
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 6a3049709c6c094f722a8132ee4c4b2051e24d95
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616691"
---
# <a name="azure-percept-security-overview"></a>Azure Percept セキュリティの概要

Azure Percept DK デバイスはハードウェア Root of Trust (信頼の基点) に基づいて設計されており、各デバイスに追加のセキュリティが組み込まれています。 これは、機密性の高いセンサー (カメラやマイクなど) と推論データの保護に役立つほか、Azure Percept Studio サービスでのデバイスの認証と承認を可能にします。

> [!NOTE]
> Azure Percept DK は、開発およびテスト環境での使用向けにライセンスされています。

## <a name="devices"></a>デバイス

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK にはトラステッド プラットフォーム モジュール (TPM) バージョン 2.0 が搭載されています。これを使用して、セキュリティが強化された Azure デバイス プロビジョニング サービスにデバイスを接続できます。 TPM は、業界で広く採用されている Trusted Computing Group の ISO 標準です。TPM の詳細については、[完全な TPM 2.0 仕様](https://trustedcomputinggroup.org/resource/tpm-library-specification/)または ISO/IEC 11889 仕様を参照してください。DPS でデバイスを安全にプロビジョニングする方法の詳細については、[Azure IoT Hub Device Provisioning Service - TPM の構成証明](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)に関するページを参照してください。

### <a name="azure-percept-system-on-module-som"></a>Azure Percept システム オン モジュール (SOM)

Azure Percept DK ビジョン対応システム オン モジュール (SOM) と Azure Percept Audio アクセサリ SOM のどちらにも、埋め込み AI センサーへのアクセスを保護するためのマイクロ コントローラー ユニット (MCU) が搭載されています。 ブートのたびに、MCU ファームウェアによって Azure Percept Studio サービスに対する AI アクセレータの認証と承認が行われます。その際、デバイス識別子コンポジション エンジン (DICE) アーキテクチャが使用されます。 DICE ではブートをレイヤーに分割し、一意デバイス シークレット (UDS) に基づいてレイヤーおよび構成ごとに一意のシークレットを作成するしくみになっています。 チェーン内のいずれかの時点で異なるコードまたは構成がブートされると、シークレットが異なります。 DICE の詳細については、[DICE ワークグループの仕様](https://trustedcomputinggroup.org/work-groups/dice-architectures/)を参照してください。Azure Percept Studio と必須サービスへのアクセスを構成するには、下記の「**Azure Percept DK のファイアウォールの構成**」を参照してください。

Azure Percept デバイスでは、信頼のハードウェアルートを使用してファームウェアを保護します。 ブート ROM によって ROM とオペレーティング システム (OS) の間でファームウェアの整合性が確保されます。それにより、その他のソフトウェア コンポーネントの整合性が確保され、信頼チェーンが実現します。

## <a name="services"></a>サービス

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK では、トランスポート層セキュリティ (TLS) プロトコルを使用して、セキュリティが強化された Azure Percept Studio  と、その他の Azure サービスに接続します。 Azure Percept DK は Azure IoT Edge に対応したデバイスです。 IoT Edge ランタイムは、デバイスを IoT Edge デバイスに変えるプログラムのコレクションです。 これらの IoT Edge ランタイム コンポーネントを使用することにより、IoT Edge デバイスは、エッジで実行するコードを受信し、結果を通信できます。 Azure Percept DK では、Docker コンテナーを利用してホスト オペレーティング システムとエッジ対応アプリケーションから IoT Edge ワークロードを分離します。 Azure IoT Edge セキュリティ フレームワークについて詳しくは、[IoT Edge セキュリティ マネージャー](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager)に関する記事をご覧ください。

### <a name="device-update-for-iot-hub"></a>Device Update for IoT Hub

Device Update for IoT Hub を使用すると、Azure Percept デバイスでの再生可能なセキュリティを実現する無線更新をさらに安全、スケーラブル、確実に行うことができます。 分析情報を通じて豊富な管理コントロールを実施し、更新プログラムのコンプライアンスを確保できます。 Azure Percept DK にはデバイス更新ソリューションがあらかじめ統合されていて、回復性がある方法でファームウェアから OS のレイヤーに更新 (A/B) できます。

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept DK のファイアウォールの構成

ネットワーク設定で、Azure Percept DK デバイスから行われた接続を明示的に許可する必要がある場合、次のコンポーネントのリストを確認します。

このチェックリストを使用して、ファイアウォール規則を開始できます。

|URL (* = ワイルドカード) |送信 TCP ポート|    使用法|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|   443|    Azure DK SOM の認証と承認|
|*.auth.projectsantacruz.azure.net| 443|    Azure DK SOM の認証と承認|

さらに、[Azure IoT Edge で使用される接続](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices)の一覧を確認します。

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>次のステップ

利用可能な [Azure Percept AI モデル](./overview-ai-models.md)について学習します。
