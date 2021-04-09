---
title: Azure IoT Hub のプレビュー モードを有効にする
description: IoT Hub のプレビュー モードを有効にする方法、必要な理由、そしていくつかの警告について説明します。
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621707"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>IoT Hub のプレビュー モードをオンにして、選択された新しい機能を試してみる

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

次のような IoT Hub の新しい機能は、パブリック プレビュー段階にあります。

- MQTT 5
- ECC サーバー証明書
- TLS フラグメント長ネゴシエーション
- HTTPS/WebSocket の X509 CA チェーン サポート

これらの機能は IoT Hub のプロトコルおよび認証レイヤーでの改良点であるため、現時点では **新しい** IoT ハブでのみ使用できます。 既存の IoT ハブではまだ使用 "*できません*"。 これらの機能をプレビューするには、プレビュー モードをオンにした状態で IoT ハブを作成する必要があります。

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>新しい IoT ハブに対してプレビュー モードをオンにする

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure ホームページから **[+ リソースの作成]** ボタンを選択し、 **[Marketplace を検索]** フィールドに「*IoT Hub*」と入力します。

1. 検索結果の **[IoT Hub]** を選択し、 **[作成]** を選択します。

1. **[基本]** タブで、そのフィールドに [通常と同じように](iot-hub-create-through-portal.md)入力します。ただし、 **[リージョン]** については例外です。 次のいずれかのリージョンを選択します。
    
    - 米国中部
    - 西ヨーロッパ
    - 東南アジア

1. **[管理]** タブを選択してから、 **[詳細設定]** セクションを展開します。

1. **[プレビュー モード]** の横にある **[オン]** を選択します。 警告テキストを注意深く確認します。

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="新しい IoT ハブを作成するときにプレビュー モード オプションを選択する場所を示す画像":::

1. **Next:[確認および作成]** 、 **[作成]** の順にクリックします。

作成されると、プレビュー モードの IoT Hub には常にこのバナーが表示されるので、この IoT ハブの使用をプレビュー目的に限定すべきであることがわかります。 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="プレビュー モード IoT ハブのバナーを示す画像":::

## <a name="using-an-iot-hub-in-preview-mode"></a>プレビュー モードでの IoT ハブの使用

プレビュー モードになっている IoT ハブは、運用環境で使用 "*しない*" でください。 プレビュー モードは、このページの上部に一覧にされている選択された機能をプレビューすること "*のみ*" を目的としています。 プレビュー モードになっている IoT Hub には、他にもいくつかの制限があります。

- IP フィルター、プライベート リンク、マネージド ID、デバイス ストリーム、フェールオーバーなどの既存の IoT Hub 機能の中には、予期しない動作をするものもあれば、まったく機能しないものもあります。
- プレビュー モードの IoT ハブは、変更することも、通常の IoT ハブにアップグレードすることもできません。
- 通常の [IoT Hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) を保証することはできません - 運用環境では使用しないでください。

> [!TIP]
> [デバイス ストリーム](iot-hub-device-streams-overview.md)と[分散トレース](iot-hub-distributed-tracing.md)には、プレビュー モードは必要ありません。 これらの古いプレビュー機能を使用するには、通常どおりそのドキュメントに従ってください。 

## <a name="next-steps"></a>次のステップ

- MQTT 5 のサポートをプレビューするには、「[IoT Hub MQTT 5 のサポートの概要 (プレビュー)](iot-hub-mqtt-5.md)」を参照してください。
- ECC サーバー証明書をプレビューするには、「[楕円曲線暗号 (ECC) サーバー TLS 証明書 (プレビュー)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)」を参照してください。
- TLS フラグメント サイズ ネゴシエーションをプレビューするには、「[TLS 最大フラグメント長ネゴシエーション (プレビュー)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)」を参照してください。