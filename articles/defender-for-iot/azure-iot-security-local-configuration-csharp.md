---
title: Defender for IoT セキュリティ エージェントのローカル構成 (C#)
description: Defender for IoT セキュリティ サービスのセキュリティ エージェントの C# ローカル構成ファイルについて説明します。
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 42cf7f129005f057a9d51ce2e09db735e825f476
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784783"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>ローカル構成ファイル (C# エージェント) について

Defender for IoT セキュリティ エージェントでは、ローカル構成ファイルの構成が使用されます。

セキュリティ エージェントにより、エージェントの実行開始時に構成ファイルが 1 回読み取られます。 ローカル構成ファイルで指定されている構成には、認証構成とその他のエージェント関連の構成の両方が含まれます。

C# セキュリティ エージェントでは、複数の構成ファイルが使用されます。

- **General.config** - エージェント関連の構成。
- **Authentication.config** - 認証関連の構成 (認証の詳細を含む)。
- **SecurityIotInterface.config** - IoT 関連の構成。

構成ファイルには、既定の構成が含まれます。 認証の構成は、エージェントのインストール中に取り込まれます。構成ファイルへの変更は、エージェントの再起動時に有効になります。

## <a name="configuration-file-location"></a>構成ファイルの場所

Linux の場合:

- オペレーティング システムの構成ファイルは、`/var/ASCIoTAgent` にあります。

Windows の場合:

- オペレーティング システムの構成ファイルは、セキュリティ エージェントのディレクトリ内にあります。

### <a name="generalconfig-configurations"></a>General.config の構成

| 構成名 | 指定できる値 | 詳細 |
|:-----------|:---------------|:--------|
| agentId | GUID | エージェントの一意識別子 |
| readRemoteConfigurationTimeout | TimeSpan | IoT Hub からリモート構成を取り込む期間。 エージェントが指定時間内に構成を取り込むことができない場合、操作はタイムアウトになります。|
| schedulerInterval | TimeSpan | 内部スケジューラの間隔。 |
| producerInterval | TimeSpan | イベント プロデューサー ワーカーの間隔。 |
| consumerInterval | TimeSpan | イベント コンシューマー ワーカーの間隔。 |
| highPriorityQueueSizePercentage | 0 < 数値 < 1 | 優先度が高いメッセージ専用の合計キャッシュの部分。 |
| logLevel | "Off"、"Fatal"、"Error"、"Warning"、"Information"、"Debug"  | この重大度以上のログ メッセージは、デバッグ コンソール (Linux では Syslog) に記録されます。 |
| fileLogLevel |  "Off"、"Fatal"、"Error"、"Warning"、"Information"、"Debug"| この重大度以上のログ メッセージは、ファイル (Linux では Syslog) に記録されます。 |
| diagnosticVerbosityLevel | "None"、"Some"、"All" | 診断イベントの詳細レベル。 None - 診断イベントは送信されません。 Some - 重要度の高い診断イベントだけが送信されます。 All - すべてのログも、診断イベントとして送信されます。 |
| logFilePath | ファイルへのパス | fileLogLevel > Off の場合、このファイルにログが書き込まれます。 |
| defaultEventPriority | "High"、"Low"、"Off" | 既定のイベント優先度。 |

### <a name="generalconfig-example"></a>General.config の例

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| 構成名 | 指定できる値 | 詳細 |
|:-----------|:---------------|:--------|
| moduleName | string | Defender-IoT-micro-agent ID の名前。 この名前は、デバイスのモジュール ID 名に対応している必要があります。 |
| deviceId | string | デバイスの ID (Azure IoT Hub に登録されているもの)。 |
| schedulerInterval | TimeSpan 文字列 | 内部スケジューラの間隔。 |
| gatewayHostname | string | Azure Iot Hub のホスト名。 通常 <my-hub>.azure-devices.net |
| filePath | 文字列 - ファイルへのパス | 認証シークレットを含むファイルへのパス。|
| type | "SymmetricKey"、"SelfSignedCertificate" | 認証用のユーザー シークレット。 ユーザー シークレットが対称キーの場合は、*SymmetricKey* を選択します。シークレットが自己署名証明書の場合は、*SelfSignedCertificate* を選択します。 |
| identity | "DPS"、"Module"、"Device" | 認証 ID - 認証が DPS を使用して行われる場合は DPS、認証がモジュールの資格情報を使用して行われる場合は Module、認証がデバイスの資格情報を使用して行われる場合は Device。
| certificateLocationKind |  "LocalFile"、"Store" | 証明書がファイルに保存されている場合は LocalFile、証明書が証明書ストアにある場合は Store。 |
| idScope | string | DPS の ID スコープ |
| registrationId | string  | DPS のデバイス登録 ID。 |
|

### <a name="authenticationconfig-example"></a>Authentication.config の例

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| 構成名 | 指定できる値 | 詳細 |
|:-----------|:---------------|:--------|
| transportType | "Ampq"、"Mqtt" | IoT Hub のトランスポートの種類。 |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config の例

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)を確認します
- Defender for IoT [サービス](quickstart-onboard-iot-hub.md)を有効にする
- Defender for IoT サービスの [FAQ](resources-frequently-asked-questions.md) を確認する
- [未加工のセキュリティ データ](how-to-security-data-access.md)にアクセスする方法を学習します
- [レコメンデーション](concept-recommendations.md)について理解します
- セキュリティの[アラート](concept-security-alerts.md)について理解します
