---
title: IoT Device Provisioning Service (DPS) インスタンスの IP アドレスについて | Microsoft Docs
description: IoT Device Provisioning Service (DPS) アドレスとそのプロパティに対してクエリを実行する方法について説明します。 DPS インスタンスの IP アドレスは、ディザスター リカバリーやリージョン間フェールオーバーなど、特定のシナリオを通じて変更される可能性があります。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "79237455"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS の IP アドレス

IoT Hub Device Provisioning Service (DPS) のパブリック エンドポイントの IP アドレス プレフィックスは、_AzureIoTHub_ [サービス タグ](../virtual-network/service-tags-overview.md)で定期的に発行されます。 これらの IP アドレス プレフィックスを使用して、さまざまなネットワーク分離の目標を実装するために、IoT DPS インスタンスとデバイスまたはネットワーク資産間の接続を制御することができます。

| 目標 | アプローチ |
|------|----------|
| デバイスとサービスが IoT Hub DPS エンドポイントのみと通信するようにする | _AzureIoTHub_ サービス タグを使用して、IoT Hub DPS インスタンスを検出します。 必要に応じて、これらの IP アドレスのプレフィックスに対して、デバイスとサービスのファイアウォール設定の許可規則を構成します。 デバイスやサービスが通信する必要のない他の送信先 IP アドレスへのトラフィックを破棄する規則を構成します。 |
| IoT Hub DPS のエンドポイントが、使用しているデバイスとネットワーク資産からのみ接続を受信するようにする | IoT DPS の [IP フィルター機能](iot-dps-ip-filtering.md)を使用して、デバイスと DPS サービス API のフィルター規則を作成します。 これらのフィルター規則を使用して、使用しているデバイスとネットワーク資産の IP アドレスからの接続のみを許可できます ([制限事項](#limitations-and-workarounds)に関するセクションを参照してください)。 | 




## <a name="best-practices"></a>ベスト プラクティス

* デバイスのファイアウォール構成で許可規則を追加する場合、[適用されるプロトコルで使用される特定のポート](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)を指定することをお勧めします。

* IoT DPS インスタンスの IP アドレス プレフィックスは変更されることがあります。 これらの変更は、有効になる前に、サービス タグ経由で定期的に発行されます。 そのため、最新のサービス タグを定期的に取得して使用するためのプロセスを開発することが重要です。 このプロセスは、[Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 経由で自動化できます。 Service Tag Discovery API はまだプレビュー段階であり、場合によってはタグと IP アドレスの完全な一覧が生成されないことがあります。 Discovery API が一般提供されるまでは、[ダウンロード可能な JSON 形式で サービス タグ](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)を使用することを検討してください。 

* 特定のリージョン内の DPS エンドポイントによって使用される IP プレフィックスを識別するには、*AzureIoTHub.[リージョン名]* タグを使用します。 データセンターのディザスター リカバリーまたは[リージョン内フェールオーバー](../iot-hub/iot-hub-ha-dr.md)に対処するには、DPS インスタンスの geo ペア リージョンの IP プレフィックスへの接続も有効になっていることを確認してください。

* DPS インスタンスのファイアウォール規則を設定すると、それに対して Azure CLI および PowerShell コマンドを実行するために必要な接続がブロックされる可能性があります。 これらの接続の問題を回避するには、クライアントの IP アドレスのプレフィックスに許可規則を追加し、CLI または PowerShell クライアントが再び DPS インスタンスと通信できるようにします。  


## <a name="limitations-and-workarounds"></a>制限事項と回避策

* DPS IP フィルター機能では、規則の数は 100 個に制限されています。 この上限は、Azure カスタマー サポートへリクエストすることで上げることができます。 

* 構成済みの [IP フィルタリング規則](iot-dps-ip-filtering.md)は、リンクされた IoT Hub のエンドポイントではなく、DPS エンドポイントにのみ適用されます。 リンクされた IoT Hub の IP フィルタリングは、個別に構成する必要があります。 詳細については、[IoT Hub IP フィルタリング規則](../iot-hub/iot-hub-ip-filtering.md)に関する記事を参照してください。

## <a name="support-for-ipv6"></a>IPv6 のサポート 

現在、IPv6 は IoT Hub または DPS ではサポートされていません。

## <a name="next-steps"></a>次のステップ

DPS による IP アドレス構成の詳細については、以下を参照してください。

* [IP フィルター処理を構成する](iot-dps-ip-filtering.md)
