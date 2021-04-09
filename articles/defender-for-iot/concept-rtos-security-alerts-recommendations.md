---
title: Azure RTOS 用の Defender-IoT-micro-agent の組み込みアラーム、カスタム可能アラーム、および推奨事項
description: Azure IoT Defender-IoT-micro-agent - RTOS を使用したセキュリティ アラートと推奨される修復方法について説明します。
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784681"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Azure RTOS 用の Defender-IoT-micro-agent のセキュリティ アラートと推奨事項 (プレビュ)

Azure RTOS 用の Defender-IoT-micro-agent を使用すると、高度な分析と脅威インテリジェンスを使用して IoT ソリューションを継続的に分析し、潜在的な悪意のあるアクティビティと疑わしいシステムの変更に関するアラートを受け取ることができます。 さらに、期待されるデバイスの動作とベースラインの知識に基づいて、カスタム アラートを作成できます。

Azure RTOS 用の Defender-IoT-micro-agent のアラートは侵害の可能性のインジケーターとして機能し、調査して修復する必要があります。 Azure RTOS 用の Defender-IoT-micro-agent では、推奨事項によって、修復と更新の対象となる脆弱なセキュリティ態勢が識別されます。 

この記事では、既定の範囲に基づいてトリガーされる組み込みアラートと推奨事項に加え、想定される動作やベースラインの動作に基づく独自の値でカスタマイズ可能なものの一覧を示します。 

Defender for IoT サービスでのアラートのカスタマイズのしくみの詳細については、[カスタマイズ可能なアラート](concept-customizable-security-alerts.md)に関する記事を参照してください。 Azure RTOS 用の Defender-IoT-micro-agent の使用時にカスタマイズするために使用できる特定のアラートと推奨事項について、以下の表で詳しく説明します。 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Azure RTOS 用の Defender-IoT-micro-agent でサポートされるセキュリティ アラート

### <a name="device-related-security-alerts"></a>デバイス関連のセキュリティ アラート

|デバイス関連のセキュリティ アラート アクティビティ  |アラート名  |
|---------|---------|
|IP アドレス| 不審な IP アドレスとの通信が検出されました|
|X.509 デバイス証明書のサムプリント|x.509 デバイス証明書のサムプリントが一致しません|
|X.509 証明書| X.509 証明書の有効期限が切れました|
|SAS トークン| SAS トークンの有効期限が切れています|
|SAS トークン| SAS トークン署名が無効です|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub 関連のセキュリティ アラート

|IoT Hub セキュリティ アラート アクティビティ  |アラート名  |
|---------|---------|
|証明書を追加する    |  証明書を IoT ハブに追加しようとして失敗したことが検出されました       |
|診断設定の追加または編集    | IoT Hub の診断設定を追加または編集しようとしたことが検出されました      |
|証明書の削除    |  証明書を IoT ハブから削除しようとして失敗したことが検出されました       |
|診断設定の削除    |  IoT ハブから診断設定を削除しようとしたことが検出されました      |
|削除された証明書    | IoT Hub からの証明書の削除が検出されました        |
|新しい証明書     |  IoT Hub への新しい証明書の追加が検出されました       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Azure RTOS 用の Defender-IoT-micro-agent でサポートされるカスタマイズ可能アラート

### <a name="device-related-customizable-alerts"></a>デバイス関連のカスタマイズ可能アラート

|デバイス関連のアクティビティ |アラート名  |
|---------|---------|
|アクティブな接続|アクティブな接続の数が許容範囲内にありません|
|**MQTT** プロトコルでのクラウドからデバイスへのメッセージ|**MQTT** プロトコルでのクラウドからデバイスへのメッセージの数が許容範囲内にありません|
|送信接続| 許可されていない IP への送信接続です|

### <a name="hub-related-customizable-alerts"></a>ハブ関連のカスタマイズ可能アラート 

|ハブ関連アクティビティ  |アラート名  |
|---------|---------|
|コマンド キューの消去     |  コマンド キューの消去の回数が許容範囲内にありません       |
|**MQTT** プロトコルでのクラウドからデバイスへのメッセージ    |  **MQTT** プロトコルでのクラウドからデバイスへのメッセージの数が許容範囲外です       |
|**MQTT** プロトコルでのデバイスからクラウドへのメッセージ    | **MQTT** プロトコルでのデバイスからクラウドへのメッセージの数が許容範囲外です        |
|ダイレクト メソッドの呼び出し     |  ダイレクト メソッドの呼び出し回数が許容範囲外です       |
|**MQTT** プロトコルでの拒否されたクラウドからデバイスへのメッセージ     |   **MQTT** プロトコルでの拒否されたクラウドからデバイスへのメッセージの数が許容範囲外です      |
|ツイン モジュールへの更新     |  ツイン モジュールへの更新の数が許容範囲外です       |
|許可されていない操作    |  許可されていない操作の回数が許容範囲外です       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Azure RTOS 用の Defender-IoT-micro-agent でサポートされる推奨事項

### <a name="device-related-recommendations"></a>デバイス関連の推奨事項

|デバイス関連のアクティビティ  |推奨事項の名前 |
|---------|---------|
|認証資格情報    |  複数のデバイスで同一の認証資格情報が使われています       |

### <a name="hub-related-recommendations"></a>ハブ関連の推奨事項

|IoT Hub 関連のアクティビティ  |推奨事項の名前 |
|---------|---------|
|IP フィルター ポリシー   |  既定の IP フィルター ポリシーを **[拒否]** に設定する必要があります  |
|IP フィルター ルール| IP フィルター ルールには幅広い IP 範囲が含まれています|
|診断ログ|IoT Hub で診断ログを有効にするという提案|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Defender for IoT のすべてのアラートと推奨事項

すべての Defender for IoT サービスに関連するアラートと推奨事項の完全な一覧については、IoT の[セキュリティ アラート](concept-security-alerts.md)、IoT セキュリティの[推奨事項](concept-recommendations.md)を参照してください。

## <a name="next-steps"></a>次の手順

- [クイックスタート: Azure RTOS 用の Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
- [Azure RTOS 用の Defender-IoT-micro-agent を構成およびカスタマイズする](how-to-azure-rtos-security-module.md)
- [Azure RTOS 用の Defender-IoT-micro-agent の API](azure-rtos-security-module-api.md) を参照する