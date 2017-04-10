---
title: "Azure IoT Hub の IP 接続フィルター | Microsoft Docs"
description: "Azure IoT Hub への特定の IP アドレスからの接続をブロックする IP フィルター処理を使用する方法。 個別の IP アドレスまたは一定の範囲の IP アドレスからの接続をブロックできます。"
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: ddb729d29072724f691c178967b6181f6ce06df4
ms.openlocfilehash: a9207d116e9b7360865c950ba00210ed67c3e028


---

# <a name="use-ip-filters"></a>IP フィルターの使用

セキュリティは、Azure IoT Hub をベースとするすべての IoT ソリューションの重要な側面です。 場合によっては、セキュリティの構成の一部として、特定の IP アドレスをブラックリストまたはホワイトリストに登録する必要があります。 _IP フィルター_機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

## <a name="when-to-use"></a>使用時の注意

特定の IP アドレスの IoT Hub エンドポイントをブロックすると有用な特定のユース ケースには、次の&2; つがあります。

- IoT ハブが指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、IoT ハブを [Azure ExpressRoute] と共に使用して IoT ハブとオンプレミス インフラストラクチャとの間にプライベート接続を作成する場合が該当します。
- IoT ハブの管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、IoT Hub サービス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。

IoT ハブの拒否 IP 規則に一致する IP アドレスからの接続試行に対しては、認証されていないアクセスを示す 401 状態コードとその説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。

## <a name="default-setting"></a>既定の設定
既定では、ポータルの IoT ハブの **[IP フィルター]** は空白になっています。 この既定の設定は、ハブが任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

![][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>IP フィルター規則の追加または編集

IP フィルター規則を追加する場合は、次の値を求められます。

- **IP フィルター規則名**。最大 128 文字までの一意な英数字文字列である必要があります (大文字と小文字は区別されません)。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。
- IP フィルター規則の **[アクション]** として、**[reject (拒否)]** または **[accept (許可)]** を選択します。
- 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。

![][img-ip-filter-add-rule]

規則を保存すると、更新が進行中であることを通知するアラートが表示されます。

![][img-ip-filter-save-new-rule]

IP フィルター規則は最大で&10; 個設定でき、この制限に達すると **[追加]** オプションが無効になります。

既存の規則を編集するには、規則を含む行をダブルクリックします。

> [!NOTE]
> IP アドレスを拒否すると、他の Azure サービス (Azure Stream Analytics、Azure Virtual Machines、ポータルのデバイス エクスプローラーなど) が IoT Hub と対話できなくなる可能性があります。

## <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには、グリッド内で&1; つまたは複数の規則を選択し、**[削除]** をクリックします。

![][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

たとえば、範囲 192.168.100.0/22 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 192.168.100.0/22 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

グリッド内の IP フィルター規則の順序を変更するには、行の先頭のドットが縦に&3; つ並んだ部分をクリックし、ドラッグ アンド ドロップします。

新しい IP フィルター規則の順序を保存するには、**[保存]** をクリックします。

![][img-ip-filter-rule-order]

## <a name="next-steps"></a>次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [操作の監視][lnk-monitor]
* [IoT Hub メトリック][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure ExpressRoute]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md


<!--HONumber=Jan17_HO4-->


