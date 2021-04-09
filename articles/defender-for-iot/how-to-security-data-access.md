---
title: セキュリティおよび推奨事項データにアクセスする
description: Defender for IoT を使用する場合に、セキュリティ アラートと推奨事項データにアクセスする方法について説明します。
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 160f7c014c890f5d8c4dd6366d3acca70f21ad11
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781689"
---
# <a name="access-your-security-data"></a>セキュリティ データにアクセスする

Defender for IoT では、セキュリティ アラート、推奨事項、および未加工のセキュリティ データ (保存することを選択した場合) が Log Analytics ワークスペースに格納されます。

## <a name="log-analytics"></a>Log Analytics

使用する Log Analytics ワークスペースを構成するには:

1. IoT ハブを開きます。
1. **[セキュリティ]** セクションの下の **[設定]** ブレードをクリックします
1. **[データ収集]** をクリックし、Log Analytics ワークスペースの構成を変更します。

構成後に Log Analytics ワークスペース内でアラートと推奨事項にアクセスするには:

1. Defender for IoT でアラートまたは推奨事項を選択します。
1. **[Further investigation]** \(さらに調査\) をクリックし、 **[To see which devices have this alert click here and view the DeviceId column]** \(このアラートがどのデバイスのものかを確認するには、ここをクリックして DeviceId 列を見てください\) をクリックします。

Log Analytics からデータのクエリを実行する方法の詳細については、「[Log Analytics のクエリの概要](../azure-monitor/logs/get-started-queries.md)」を参照してください。

## <a name="security-alerts"></a>セキュリティのアラート

セキュリティ アラートは、Defender for IoT ソリューションに対して構成されている Log Analytics ワークスペース内の _AzureSecurityOfThings.SecurityAlert_ テーブルに格納されます。

セキュリティ アラートの調査を始めるときに役立つ便利なクエリがいくつか用意されています。

### <a name="sample-records"></a>サンプル レコード

レコードをいくつかランダムに選択します

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 説明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | デバイスに対するブルート フォース攻撃が成功しました        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId":"IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | デバイスへのローカル ログインの成功が検出されました     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"28207", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスへのローカル ログインの試行に失敗しました  | デバイスにローカル ログインの試行の失敗が検出されました |    { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id":"22644", "User Name": "attacker", "DeviceId":"IoT-Device-Linux" } |

### <a name="device-summary"></a>デバイスの概要

先週検出された個別のセキュリティ アラートの数を取得して、IoT ハブ、デバイス、アラートの重大度、アラートの種類ごとにグループ化します。

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | ブルート フォース攻撃が成功しました           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | デバイスでのローカル ログインの成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 暗号化コイン採掘者                     | 4   |

### <a name="iot-hub-summary"></a>IoT ハブの概要

IoT Hub、アラートの重要度、アラートの種類で、先週アラートが発生した個々のデバイスの数を選択します

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | ブルート フォース攻撃が成功しました           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | デバイスへのローカル ログインの試行に失敗しました  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | デバイスでのローカル ログインの成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | 暗号化コイン採掘者                     | 1          |

## <a name="security-recommendations"></a>セキュリティに関する推奨事項

セキュリティに関する推奨事項は、Defender for IoT ソリューション に対して構成されている Log Analytics ワークスペース内の _AzureSecurityOfThings.SecurityRecommendation_ テーブルに格納されます。

セキュリティに関する推奨事項の調査を始めるときに役立つ便利なクエリがいくつか用意されています。

### <a name="sample-records"></a>サンプル レコード

レコードをいくつかランダムに選択します

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 説明 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | アクティブ | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | アクティブ | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>デバイスの概要

個別のアクティブなセキュリティに関する推奨事項の数を取得して、IoT Hub、デバイス、推奨事項の重大度、および種類ごとにグループ化します。

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 4   |

## <a name="next-steps"></a>次のステップ

- Defender for IoT の[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)を確認する
- [Defender for IoT アラート](concept-security-alerts.md)を理解して探索する
- [Defender for IoT の推奨事項](concept-recommendations.md)を理解して探索する