---
title: Azure IoT Hub Device Provisioning Service - デバイスの概念
description: Azure IoT Hub Device Provisioning Service (DPS) におけるデバイスの再プロビジョニングの概念について説明します
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842854"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub デバイスの再プロビジョニングの概念

IoT ソリューションのライフサイクル中に、デバイスを IoT ハブ間で移動することはよくあります。 この移動の理由として、次のようなシナリオが挙げられます。

* **位置情報/geo 待機時間**: デバイスが異なる場所の間を移動するときは、より近い IoT Hub にデバイスを移行することにより、ネットワーク待機時間が改善されます。

* **マルチ テナンシー**: デバイスは同じ IoT ソリューション内で使用され、新しい顧客または顧客サイトに再割り当てされる可能性があります。 この新しい顧客には、別の IoT ハブを使用してサービスが提供される可能性があります。

* **ソリューションの変更**: デバイスは、新しい IoT ソリューションまたは更新された IoT ソリューションに移動される場合があります。 この再割り当てでは、その他のバックエンド コンポーネントに接続されている新しい IoT ハブと通信するためにそのデバイスを必要とする可能性があります。

* **検疫**: ソリューションの変更と同様です。 正常に動作していないデバイス、セキュリティ侵害を受けたデバイス、または古いデバイスは、更新してコンプライアンスが確保された状態に戻す操作のみが可能な IoT ハブに再割り当てされることがあります。 デバイスが適切に機能するようになったら、そのメインのハブに再び移行されます。

Device Provisioning Service 内の再プロビジョニングのサポートでは、これらのニーズに対処します。 デバイスは、デバイスの登録エントリで構成された再プロビジョニング ポリシーに基づいて、新しい IoT Hub に自動的に再割り当てされる場合があります。

## <a name="device-state-data"></a>デバイスの状態データ

デバイスの状態データは、[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)とデバイスの機能で構成されています。 このデータは Device Provisioning Service インスタンスおよびデバイスが割り当てられている IoT Hub に格納されます。

![Device Provisioning Service でプロビジョニングが機能する方法を示す図。](./media/concepts-device-reprovisioning/dps-provisioning.png)

デバイスが Device Provisioning Service インスタンスで最初にプロビジョニングされる場合、次の手順が実行されます。

1. デバイスによって Device Provisioning Service インスタンスにプロビジョニング要求が送信されます。 サービス インスタンスでは、登録エントリに基づいてデバイス ID が認証され、デバイスの状態データの初期構成が作成されます。 サービス インスタンスによって、デバイスが登録の構成に基づいて IoT Hub に割り当てられ、IoT Hub 割り当てがそのデバイスに返されます。

2. プロビジョニング サービス インスタンスによって、任意のデバイスの初期状態のデータをコピーしたものが割り当てられた IoT Hub に提供されます。 デバイスが割り当てられた IoT Hub に接続され、操作を開始します。

時間の経過とともに、IoT Hub 上のデバイスの状態データは、[デバイスの操作](../iot-hub/iot-hub-devguide-device-twins.md#device-operations)と[バックエンドの操作](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations)によって更新される可能性があります。 Device Provisioning Service インスタンスに格納されているデバイスの初期状態の情報は、変更されずに保持されます。 この変更されていないデバイスの状態データが初期構成です。

![Device Provisioning Service でのプロビジョニング](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

シナリオに応じて、デバイスが IoT Hub 間で移動されるときに、前の IoT Hub で更新されたデバイスの状態を新しい IoT Hub に移行することが必要な場合もあります。 この移行は、Device Provisioning Service でポリシーを再プロビジョニングすることによってサポートされます。

## <a name="reprovisioning-policies"></a>ポリシーの再プロビジョニング

シナリオに応じて、通常、再起動時にデバイスからプロビジョニング サービス インスタンスに要求が送信されます。 また、オンデマンドでプロビジョニングを手動でトリガーする方法もサポートしています。 登録エントリの再プロビジョニング ポリシーによって、Device Provisioning Service インスタンスでこれらのプロビジョニング要求を処理する方法が決まります。 また、このポリシーによって、再プロビジョニング中にデバイスの状態データを移行する必要があるかどうかも決まります。 同じポリシーを個別の登録と登録グループに利用できます。

* **データの再プロビジョニングと移行**: このポリシーは新しい登録エントリの既定値です。 このポリシーは、登録エントリに関連付けられているデバイスが新しい要求 (1) を送信するときに処理されます。 登録エントリの構成に応じて、デバイスは別の IoT ハブに再割り当てされる可能性があります。 デバイスの割り当て先の IoT ハブが変更されると、最初の IoT ハブへのデバイスの登録は削除されます。 最初の IoT Hub から更新されたデバイスの状態情報は、新しい IoT Hub (2) に移行されます。 移行中、デバイスの状態は "**割り当てています**" と報告されます。

    ![登録エントリに関連付けられているデバイスが新しい要求を送信するときに処理されるポリシーを示す図。](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **初期構成の再プロビジョニングとリセット**: このポリシーは、登録エントリに関連付けられているデバイスが新しいプロビジョニング要求 (1) を送信するときに処理されます。 登録エントリの構成に応じて、デバイスは別の IoT ハブに再割り当てされる可能性があります。 デバイスの割り当て先の IoT ハブが変更されると、最初の IoT ハブへのデバイスの登録は削除されます。 プロビジョニング サービス インスタンスが、デバイスがプロビジョニングされたときに受け取った初期構成のデータは、新しい IoT Hub (2) に提供されます。 移行中、デバイスの状態は "**割り当てています**" と報告されます。

    このポリシーは、IoT Hub を変更せずにファクトリ リセットに使用されることが多いです。

    ![登録エントリに関連付けられているデバイスが新しいプロビジョニング要求を送信するときにポリシーが処理される方法を示す図。](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **再プロビジョニングしない**: デバイスは別のハブに再プロビジョニングされることはありません。 このポリシーは、下位互換性を管理するために提供されます。

### <a name="managing-backwards-compatibility"></a>下位互換性を管理する

2018 年 9 月より前は、IoT Hub へのデバイスの割り当てには固定の動作がありました。 デバイスがプロビジョニング プロセスから戻された場合、同じ IoT Hub に戻るように割り当てられるだけです。

この動作の依存関係を取得するソリューションの場合、プロビジョニング サービスには下位互換性が含まれます。 この動作は現在、次の条件に従ってデバイスに対して保持されます。

1. デバイスは、Device Provisioning Service のネイティブな再プロビジョニング サポートが利用可能になる前の API バージョンに接続されます。 以下の API の表を参照してください。

2. デバイスに対する登録エントリには、再プロビジョニングのポリシー セットがありません。

この互換性によって、以前にデプロイしたデバイスで、初期テスト時に存在したのと同じ動作が確実に行われるようになります。 前の動作を保持するには、再プロビジョニング ポリシーをこれらの登録に保存しないでください。 再プロビジョニング ポリシーが設定されると、再プロビジョニング ポリシーは動作より優先されます。 再プロビジョニング ポリシーが優先されることを許可することによって、顧客はデバイスの再イメージ化することなく、デバイスの動作を更新できます。

次のフロー チャートは、動作が存在するときを示しています。

![下位互換性のフロー チャート](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

以下の表は、Device Provisioning Service のネイティブな再プロビジョニング サポートが利用可能になる前の API バージョンを示しています。

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 以前](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 以前](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 以前](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 以前](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 以前](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 以前](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> これらの値とリンクは、変更される可能性があります。 これは、バージョンを顧客が決定できる場所、および想定されるバージョンの内容を決定しようとする単なるプレースホルダーです。

## <a name="next-steps"></a>次のステップ

* [デバイスを再プロビジョニングする方法](how-to-reprovision.md)
