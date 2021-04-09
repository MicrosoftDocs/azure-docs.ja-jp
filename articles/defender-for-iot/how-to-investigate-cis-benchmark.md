---
title: CIS ベンチマークの推奨事項を調査する
description: OS ベースラインの推奨事項に基づいて、基本的および高度な調査を行います。
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 01ca6e1fecddff9800872a3e5495a5cac578a74f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782029"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>OS ベースライン (CIS ベンチマークに基づく) の推奨事項を調査する 

OS ベースラインの推奨事項に基づいて、基本的および高度な調査を行います。

## <a name="basic-os-baseline-security-recommendation-investigation"></a>基本的な OS ベースライン セキュリティに関する推奨事項の調査  

Azure Defender for IoT ポータルの **[IoT Hub]** の下に移動して、OS ベースラインの推奨事項を調査することができます。 詳細については、[セキュリティに関する推奨事項を調査する](quickstart-investigate-security-recommendations.md)方法を参照してください。

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>高度な OS ベースラインのセキュリティに関する推奨事項の調査  

このセクションでは、OS ベースライン テストの結果をよりよく理解し、Azure Log Analytics でイベントのクエリを実行する方法について説明します。  

高度な OS ベースラインのセキュリティに関する推奨事項の調査は、Log Analytics を使用する場合にのみサポートされます。 作業を続行する前に、Defender for IoT を Log Analytics ワークスペースに接続してください。 高度な OS ベースラインのセキュリティに関する推奨事項の詳細については、[Azure Defender for IoT エージェントベースのソリューションを構成する](how-to-configure-agent-based-solution.md)方法を参照してください。

アラートについて、Log Analytics で IoT セキュリティ イベントのクエリを実行するには、次のようにします。

1. **[アラート]** ページに移動します。

1. **[Investigate recommendations in Log Analytics workspace]\(Log Analytics ワークスペースで推奨事項を調査\)** を選択します。

推奨事項について、Log Analytics で IoT セキュリティ イベントのクエリを実行するには、次のようにします。

1. **[推奨事項]** ページに移動します。

1. **[Investigate recommendations in Log Analytics workspace]\(Log Analytics ワークスペースで推奨事項を調査\)** を選択します。

1. 特定のデバイスの詳細を表示するには、 **[推奨事項の詳細]** クイック ビュー ページから **[Show Operation system (OS) baseline rules details]\(オペレーション システム (OS) の基準規則の詳細を表示\)** を選択します。

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="特定のデバイスの詳細を表示する。"::: 

Log Analytics ワークスペースで IoT セキュリティ イベントに直接クエリを実行するには、次のようにします。

1. **[ログ]** ページに移動します。

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="左側のペインから [ログ] を選択する。":::

1. **[Investigate the alerts]\(アラートの調査\)** を選択するか、任意のセキュリティに関する推奨事項、またはアラートから **[Investigate the alerts in Log Analytics]\(Log Analytics でアラートを調査\)** オプションを選びます。   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>OS ベースライン リソースを調査するための便利なクエリ: 

> [!Note]
> 次の各クエリでは、`<device-id>` を必ず、ご利用のデバイスに指定した名前に置き換えてください。 


### <a name="retrieve-the-latest-information"></a>最新の情報を取得する

- **デバイス フリートの失敗**: 次のクエリを実行し、デバイス フリート全体で失敗した確認に関する最新情報を取得します。 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **特定のデバイスの失敗** - 次のクエリを実行し、特定のデバイスで失敗した確認に関する最新情報を取得します。  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **特定のデバイスのエラー** - このクエリを実行し、特定のデバイスでエラーが発生した確認に関する最新情報を取得します。 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **特定の確認に失敗したデバイス フリートのデバイス一覧を更新する** - 特定の確認に失敗した (デバイス フリート全体の) デバイスの更新された一覧を取得します。  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>次のステップ

[セキュリティに関する推奨事項を調査する](quickstart-investigate-security-recommendations.md)。
 