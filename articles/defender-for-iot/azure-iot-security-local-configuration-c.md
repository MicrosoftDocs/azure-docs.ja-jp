---
title: セキュリティ エージェントのローカル構成 (C)
description: Defender での C のエージェント ローカル構成について説明します。
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: dc4ea30080711633e52f966b89a16f7246a30673
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779411"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>LocalConfiguration.json ファイルについて - C エージェント

Defender for IoT セキュリティ エージェントでは、ローカル構成ファイルの構成が使用されます。
このセキュリティ エージェントは、エージェントの起動時に構成を 1 回読み取ります。
ローカル構成ファイルにある構成には、認証構成およびその他のエージェント関連の構成が含まれます。
このファイルには、JSON 表記の "キーと値" ペアの構成が含まれています。この構成はエージェントのインストール時に取り込まれます。

既定では、このファイルの場所は /var/ASCIoTAgent/LocalConfiguration.json です。

構成ファイルへの変更は、エージェントの再起動時に有効になります。

## <a name="security-agent-configurations-for-c"></a>C のセキュリティ エージェント構成

| 構成名 | 指定できる値 | 詳細 |
|:-----------|:---------------|:--------|
| AgentId | GUID | エージェントの一意識別子 |
| TriggerdEventsInterval | ISO8601 文字列 | トリガーされたイベント収集のスケジューラ間隔 |
| ConnectionTimeout | ISO8601 文字列 | IoThub への接続がタイムアウトになるまでの期間 |
| 認証 | JsonObject | 認証の構成。 このオブジェクトには、IoTHub に対する認証に必要なすべての情報が含まれます |
| ID | "DPS"、"SecurityModule"、"Device" | 認証 ID - 認証が DPS を使用して行われる場合は DPS、認証が Defender-IoT-micro-agent の資格情報を使用して行われる場合は SecurityModule、認証がデバイスの資格情報を使用して行われる場合は Device |
| AuthenticationMethod | "SasToken"、"SelfSignedCertificate" | 認証用のユーザー シークレット - ユーザー シークレットが対称キーの場合は SasToken を選択します。シークレットが自己署名証明書の場合は SelfSignedCertificate を選択します  |
| FilePath | ファイルへのパス (文字列) | 認証シークレットを含むファイルへのパス |
| HostName | string | Azure IoT ハブのホスト名。 通常 <my-hub>.azure-devices.net |
| deviceId | string | デバイスの ID (Azure IoT Hub に登録されているもの) |
| DPS | JsonObject | DPS 関連の構成 |
| IDScope | string | DPS の ID スコープ |
| RegistrationId | string  | DPS のデバイス登録 ID |
| ログ記録 | JsonObject | エージェントのロガー関連の構成 |
| SystemLoggerMinimumSeverity | 0 <= 数値 <= 4 | この重大度以上のログ メッセージは、/var/log/syslog に記録されます (0 は最も低い重大度です) |
| DiagnosticEventMinimumSeverity | 0 <= 数値 <= 4 | この重大度以上のログ メッセージは、診断イベントとして送信されます (0 は最も低い重大度です) |

## <a name="security-agent-configurations-code-example"></a>セキュリティ エージェント構成のコード例

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)について詳細を確認する
- Defender for IoT [サービス](quickstart-onboard-iot-hub.md)を有効にする
- Defender for IoT サービスの [FAQ](resources-frequently-asked-questions.md) を確認する
- [未加工のセキュリティ データ](how-to-security-data-access.md)にアクセスする方法を学習します
- [レコメンデーション](concept-recommendations.md)について理解します
- セキュリティの[アラート](concept-security-alerts.md)について理解します
