---
title: IoT Edge のバージョンのナビゲーションと履歴 - Azure IoT Edge
description: IoT Edge の新機能を示します。これには、最新リリースの新機能に関する情報が含まれます。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 877b488fc888b4ced8165c19bcd5c51b5f982745
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492508"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge のバージョンとリリース ノート

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge は、GitHub でホストされるオープンソースの IoT Edge プロジェクトから構築された製品です。 新しいすべてのリリースは、[Azure IoT Edge プロジェクト](https://github.com/Azure/azure-iotedge)で使用できます。 [オープンソースの IoT Edge プロジェクト](https://github.com/Azure/iotedge)で、投稿とバグ報告を行うことができます。

## <a name="documented-versions"></a>ドキュメント化されたバージョン

このサイトの IoT Edge ドキュメントは、2 つの異なるバージョンの製品に関するものです。このため、ご使用の IoT Edge 環境に対応したコンテンツを選択できます。 現時点では、次の 2 つのバージョンがサポートされています。

* **IoT Edge 1.1 (LTS)** は、IoT Edge の最初の長期サポート (LTS) バージョンです。 このバージョンのドキュメントは、以前のバージョンから 1.1 までのすべての機能をカバーしています。 このドキュメントのバージョンは、バージョン 1.1 のサポート期間全体で変更がなく、以降のバージョンでリリースされた新機能は反映されません。 1\.1 リリースは、IoT Edge の最新の一般提供バージョンです。
* **IoT Edge 1.2 (プレビュー)** には、最新のプレビュー リリース [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4) に含まれる機能の追加コンテンツが含まれています。

IoT Edge リリースの詳細については、「[Azure IoT Edge のサポートされるシステム](support.md)」を参照してください。

## <a name="version-history"></a>バージョン履歴

次の表に、IoT Edge パッケージ リリースの最新のバージョン履歴と、各バージョンで行われたドキュメントの更新を示します。

| リリース ノートと資産 | Type | Date | ハイライト |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | プレビュー | 2021 年 3 月 | 新しいインストールと構成の手順による新しい IoT Edge パッケージの導入。 詳細については、[1.0 または 1.1 から 1.2 への更新](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)に関するセクションを参照してください。
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | 長期サポート (LTS) | 2021 年 2 月 | [長期サポート プランとサポート対象システムの更新](support.md) |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | プレビュー | 2020 年 11 月 | [IoT Edge デバイスのゲートウェイ](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT ブローカー](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020 年 10 月 | [UploadSupportBundle ダイレクト メソッド](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[ランタイム メトリックのアップロード](how-to-access-built-in-metrics.md)<br>[ルートの優先順位と有効期限](module-composition.md#priority-and-time-to-live)<br>[モジュールの起動順序](module-composition.md#configure-modules)<br>[X.509 の手動プロビジョニング](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020 年 3 月 | [DPS による X.509 の自動プロビジョニング](how-to-auto-provision-x509-certs.md)<br>[RestartModule ダイレクト メソッド](how-to-edgeagent-direct-method.md#restart-module)<br>[support-bundle コマンド](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>次のステップ

* [すべての Azure IoT Edge リリースを確認する](https://github.com/Azure/azure-iotedge/releases)
* [フィードバック フォーラムで機能に関する要求を作成または確認する](https://feedback.azure.com/forums/907045-azure-iot-edge)