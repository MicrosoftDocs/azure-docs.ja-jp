---
title: ポートと VLAN の名前解決を強化する
description: デバイスの解像度を強化するために、センサー上のポートと VLAN の名前をカスタマイズします。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c976671bccb420ae24d8def7a6574098d86ce6d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98803576"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>ポートと VLAN の名前解決を強化する

デバイスの解像度を強化するために、センサー上のポートと VLAN の名前をカスタマイズできます。

## <a name="customize-port-names"></a>ポート名をカスタマイズする

Azure Defender for IoT では、DHCP や HTTP など、最も一般的に予約されているポートに名前が自動的に割り当てられます。 Defender for IoT によって検出された他のポートのポート名をカスタマイズできます。 たとえば、予約されていないポートは異常に負荷の高いアクティビティを示すため、それに名前を割り当てます。

これらの名前は次の場合に表示されます。

  - デバイス マップから **[デバイス グループ]** を選択します。

  - ポート情報を提供するウィジェットを作成します。

### <a name="view-custom-port-names-in-the-device-map"></a>デバイス マップにカスタム ポート名を表示する

ユーザーによって定義された名前を含むポートは、デバイス マップの **[Known Applications]\(既知のアプリケーション\)** グループに表示されます。

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="[Known Applications]\(既知のアプリケーション\) グループを表示しているデバイス マップのスクリーンショット。":::

### <a name="view-custom-port-names-in-widgets"></a>ウィジェットにカスタム ポート名を表示する

定義したポート名は、ポート別のトラフィックを示すウィジェットに表示されます。

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="トラフィックを示します。":::

カスタム ポート名を定義するには:

1. **[システム設定]** を選択し、 **[Standard Aliases]\(標準エイリアス\)** を選択します。

2. **[Add Port Alias]\(ポート エイリアスの追加\)** を選択します。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="[Add Port Alias]\(ポート エイリアスの追加\)。":::

3. ポート番号を入力し、 **[TCP/UDP]\(TCP または UDP\)** を選択するか、 **[両方]** を選択して、名前を追加します。

4. **[保存]** を選択します。

## <a name="configure-vlan-names"></a>VLAN 名を構成する

デバイスの VLAN 番号とタグを使用して、デバイスのインベントリ データをエンリッチすることができます。 データ エンリッチメントに加えて、VLAN ごとのデバイス数を表示したり、VLAN ウィジェット別に帯域幅を表示したりすることができます。

VLAN のサポートは、802.1q (最大 VLAN ID 4094) に基づいています。

VLAN 情報を取得方法は 2 つあります。

- **自動検出**:既定では、センサーによって VLAN が自動的に検出されます。 トラフィックが検出された VLAN は、VLAN 構成画面、データ マイニング レポート、および VLAN 情報を含むその他のレポートに表示されます。 未使用の VLAN は表示されません。 これらの VLAN の編集や削除はできません。 

  各 VLAN に一意の名前を追加する必要があります。 名前を追加しない場合は、VLAN が報告されるすべての場所に VLAN 番号が表示されます。

- **手動で追加**:VLAN は手動で追加できます。 手動で追加された VLAN ごとに一意の名前を追加する必要があり、これらの VLAN を編集または削除することができます。

VLAN 名には、最大 50 個の ASCII 文字を含めることができます。

> [!NOTE]
> VLAN 名は、センサーと管理コンソールの間で同期されません。 管理コンソール上でも名前を定義する必要があります。  
Cisco スイッチの場合は、スパン構成に次の行を追加します: `monitor session 1 destination interface XX/XX encapsulation dot1q`。 このコマンドでは、*XX/XX* はポートの名前と番号です。

VLAN を構成するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[VLAN]** を選択します。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="システム設定を使用して VLAN を編集します。":::

3. 各 VLAN ID の横に一意の名前を追加します。

## <a name="next-steps"></a>次のステップ

増強されたデバイス情報をさまざまなレポートで表示する:

- [デバイス インベントリのセンサー検出を調査する](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [センサーの傾向と統計のレポート](how-to-create-trends-and-statistics-reports.md)
- [センサーのデータ マイニング クエリ](how-to-create-data-mining-queries.md)
