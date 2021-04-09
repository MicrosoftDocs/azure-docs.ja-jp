---
title: Azure IoT Hub のクラシック IP フィルター (非推奨) | Microsoft Docs
description: クラシック IP フィルターからのアップグレード方法と利点
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661156"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub のクラシック IP フィルターとアップグレード方法 

IoT Hub のアップグレードされた IP フィルターは、 既定でセキュリティによって保護され、組み込みのエンドポイントを保護します。 Microsoft では破壊的変更を行わないよう努めていますが、アップグレードされた IP フィルターの強化されたセキュリティ モデルにはクラシック IP フィルターとの互換性がないため、ご提供を終了することを発表いたします。 アップグレードされた新しい IP フィルターについては、[最新情報](#whats-new)と [IoT ハブの IP フィルター](iot-hub-ip-filtering.md)に関するページを参照してください。

サービスの中断を回避するには、アップグレードが自動的に実行される移行期限の前に、ガイド付きアップグレードを実行する必要があります。 移行タイムラインの詳細については、[Azure の更新](https://aka.ms/ipfilterv2azupdate)に関するページを参照してください。

## <a name="how-to-upgrade"></a>アップグレードする方法

1.  Azure portal にアクセスします
2.  IoT Hub に移動します。
3.  左側のメニューで **[ネットワーキング]** を選択します。
4.  IP フィルターを新しいモデルにアップグレードするよう求めるバナーが表示されます。 **[はい]** を選択して続行します。
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="クラシック IP フィルターからアップグレードするためのバナー プロンプトを示す画像":::
5.  新しい IP フィルターでは既定ですべての IP がブロックされるため、アップグレードすると個別の拒否規則は削除されます。ただし、保存する前にそれらを確認できます。 規則を注意深く検討して、問題ないことを確認してください。
6.  画面の指示に従ってアップグレードを完了します。

## <a name="whats-new"></a>新着情報

### <a name="secure-by-default"></a>既定でのセキュリティ保護

クラシック IP フィルターでは、既定ですべての IP アドレスが IoT Hub への接続が暗黙的に許可されます。これは、多くの一般的なネットワーク セキュリティ シナリオとうまく合致しません。 通常は、信頼できる IP アドレスのみが IoT ハブに接続できるようにし、他のすべてを拒否します。 クラシック IP フィルターを使用してこの目標を達成する場合は、複数の手順から成るプロセスとなります。 たとえば、`192.168.100.0/22` からのトラフィックのみを受け入れる場合は、次のようにする必要があります。

1. `192.168.100.0/22` に対して 1 つの "*許可*" 規則を構成します。
1. `0.0.0.0/0` に対して別の "*ブロック*" 規則 ("すべてブロック" 規則) を構成します。
1. 規則が正しく順序付けされていることを確認します (許可規則の順序は、ブロック規則より上になります)。

実際には、この複数の手順から成るプロセスによって混乱が生じます。 ユーザーが "すべてブロック" 規則を構成しなかったり、規則を正しく順序付けしなかったりした場合は、意図しない露出が発生します。 

新しい IP フィルターでは、既定ですべての IP アドレスがブロックされます。 明示的に追加した IP 範囲だけが IoT Hub への接続を許可されます。 上記の例の手順 2 と 3 はもう必要ありません。 この新しい動作では、構成が簡素化され、[Secure By Default (既定でのセキュリティ保護) の原則](https://wikipedia.org/wiki/Secure_by_default)が順守されます。

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>イベント ハブと互換性のある組み込みのエンドポイントを保護する

クラシック IP フィルターは、組み込みのエンドポイントには適用できません。 この制限は、すべてブロック規則 (`0.0.0.0/0` をブロック) が構成されているイベントでは、引き続きどの IP アドレスからでも組み込みのエンドポイントにアクセスできることを意味します。

新しい IP フィルターには、組み込みのエンドポイントに規則を適用するオプションが用意されています。これにより、ネットワーク セキュリティの脅威に対する暴露を削減できます。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="組み込みのエンドポイントに適用するかどうかのトグルを示す画像":::

> [!NOTE]
> このオプションは、無料 (F1) の IoT ハブでは使用できません。 組み込みのエンドポイントに IP フィルター規則を適用するには、有料の IoT ハブを使用してください。

### <a name="api-impact"></a>API の影響

アップグレードされた IP フィルターは、`2020-08-31` (および `2020-08-31-preview`) 以降の IoT Hub リソース API で使用できます。 クラシック IP フィルターは、すべての API バージョンで引き続き使用できますが、移行期限に近い将来の API バージョンで削除されます。 移行タイムラインの詳細については、[Azure の更新](https://aka.ms/ipfilterv2azupdate)に関するページを参照してください。

## <a name="tip-try-the-changes-before-they-apply"></a>ヒント: 適用する前に変更を試す

新しい IP フィルターでは既定ですべての IP アドレスがブロックされるため、個々のブロック規則には互換性がなくなります。 そのため、ガイド付きアップグレード プロセスでは、これらの個々のブロック規則は削除されます。 

クラシック IP フィルターを使って変更を試すには、次の操作を行います。

1. IoT ハブで **[ネットワーク]** タブにアクセスします。
1. ロールバックする場合に備えて、既存の IP フィルター (クラシック) の構成をメモしておきます。
1. **ブロック** の規則の横で、ごみ箱のアイコンを選択してそれらを削除します。
1. `0.0.0.0/0` を使用して下部に別の規則を追加し、 **[ブロック]** を選択します。
1. **[保存]** を選びます。

この構成では、クラシックからアップグレードした後に新しい IP フィルターがどのように動作するかを模倣します。 1 つの例外は、組み込みのエンドポイント保護です。これは、クラシック IP フィルターを使用して試行できません。 ただし、その機能は省略可能なので、それによって何かが破損するおそれがあると思われる場合は使用する必要はありません。

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>ヒント: IoT ハブへのすべての IP 接続の診断ログを確認する

円滑な切り替えを行うために、[接続] カテゴリの診断ログを確認します。 `maskedIpAddress` プロパティを検索して、範囲が想定どおりかどうかを確認します。 注意: 新しい IP フィルターでは、明示的に追加されていないすべての IP アドレスがブロックされます。

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT Hub クラシック IP フィルターのドキュメント (廃止)

> [!IMPORTANT]
> 次に示すのは、クラシック IP フィルターの元のドキュメントです。これは廃止されます。

セキュリティは、Azure IoT Hub をベースとするすべての IoT ソリューションの重要な側面です。 場合によっては、セキュリティ構成の一部として、デバイスが接続できる IP アドレスを明示的に指定する必要があります。 *IP フィルター* 機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

### <a name="when-to-use"></a>使用する場合

特定の IP アドレスの IoT Hub エンドポイントをブロックすると有用な特定のユース ケースには、次の 2 つがあります。

* IoT Hub が指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、IoT Hub を [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) と共に使用して、IoT Hub とオンプレミス インフラストラクチャとの間にプライベート接続を作成する場合が該当します。

* IoT Hub の管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

### <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、IoT Hub サービス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。 ただし、(IoT Hub 接続文字列経由ではなく) [組み込みイベント ハブ互換エンドポイント](iot-hub-devguide-messages-read-builtin.md)から直接読み取るクライアントには IP フィルター規則は適用されません。 

IoT ハブの拒否 IP 規則に一致する IP アドレスからの接続試行に対しては、認証されていないアクセスを示す 401 状態コードとその説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。 IP アドレスを拒否すると、Azure Stream Analytics、Azure Virtual Machines、Azure portal のデバイス エクスプローラーなど、他の Azure サービスが IoT Hub と対話できなくなる可能性があります。

> [!NOTE]
> Azure Stream Analytics (ASA) を使用し、IP フィルターを有効にして IoT Hub からメッセージを読み取る必要がある場合、イベント ハブと互換性のある名前と IoT Hub のエンドポイントを利用し、ASA に[イベント ハブ ストリーム入力](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)を手動で追加します。

### <a name="default-setting"></a>既定の設定

既定では、ポータルの IoT ハブの **[IP フィルター]** は空白になっています。 この既定の設定は、ハブが任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

[IP フィルター設定] ページに移動するには、 **[ネットワーク]** 、 **[パブリック アクセス]** 、 **[Selected IP Ranges]\(選択された IP 範囲\)** の順に選択します。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT Hub の既定の IP フィルター設定":::

### <a name="add-or-edit-an-ip-filter-rule"></a>IP フィルター規則の追加または編集

IP フィルター規則を追加するには、 **[+ IP フィルター規則の追加]** を選択します。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="IoT Hub への IP フィルター規則の追加":::

**[IP フィルター規則の追加]** を選択したら、フィールドに入力します。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="[IP フィルター規則の追加] を選択した後":::

* IP フィルター規則の **名前** を指定します。 これは、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。

* 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。

* IP フィルター規則の **[アクション]** として **[許可]** または **[ブロック]** を選択します。

フィールドに入力したら、 **[保存]** を選択して規則を保存します。 更新が進行中であることを通知するアラートが表示されます。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="IP フィルター規則の保存に関する通知":::

IP フィルター規則が最大値の 10 個に達すると、 **[追加]** オプションは無効になります。

既存の規則を編集するには、変更するデータを選択して変更を加え、 **[保存]** を選択して編集内容を保存します。

### <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには、その行のごみ箱アイコンを選択し、次に **[保存]** を選択します。 規則が削除されて、変更が保存されます。

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="IoT Hub の IP フィルター規則の削除":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI を使用した IP フィルターの取得および更新

[Azure CLI](/cli/azure/) を使用して、IoT Hub の IP フィルターを取得および更新できます。

IoT Hub の現在の IP フィルターを取得するには、以下を実行します。

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

これにより、既存の IP フィルターが `properties.ipFilterRules` キーの下にリストされている JSON オブジェクトが返されます。

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

IoT Hub の新しい IP フィルターを追加するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

IoT Hub の既存の IP フィルターを削除するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

`<ipFilterIndexToRemove>` は、IoT Hub の `properties.ipFilterRules` の IP フィルターの順序に対応している必要があるので注意してください。

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell を使用した IP フィルターの取得および更新

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) を使用して、IoT Hub の IP フィルターを取得および設定できます。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>REST を使用した IP フィルター規則の更新

Azure リソース プロバイダーの REST エンドポイントを使用することでも、IoT Hub の IP フィルターを取得および変更できます。 [createorupdate メソッド](/rest/api/iothub/iothubresource/createorupdate)の `properties.ipFilterRules` を参照してください。

### <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

たとえば、範囲 192.168.100.0/22 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 192.168.100.0/22 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

グリッド内の IP フィルター規則の順序を変更するには、行の先頭のドットが縦に 3 つ並んだ部分をクリックし、ドラッグ アンド ドロップします。

新しい IP フィルター規則の順序を保存するには、 **[保存]** をクリックします。

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="IoT Hub の IP フィルター規則の順序の変更":::

## <a name="next-steps"></a>次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IP フィルターの使用](iot-hub-ip-filtering.md)