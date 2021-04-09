---
title: Azure Percept Vision とビジョン モジュールに関する問題をトラブルシューティングする
description: ビジョン AI のプロトタイプ作成エクスペリエンスで多く見られるいくつかの問題をトラブルシューティングするためのヒントを紹介します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678569"
---
# <a name="vision-solution-troubleshooting"></a>ビジョン ソリューションのトラブルシューティング

Azure Percept Studio でコーディングなしのビジョン ソリューションをトラブルシューティングする方法については、次のガイダンスを参照してください。

## <a name="delete-a-vision-project"></a>ビジョン プロジェクトを削除する

1. https://www.customvision.ai/projects にアクセスします。

1. 削除したいプロジェクトにマウス ポインターを合わせ、ごみ箱アイコンをクリックすると、プロジェクトが削除されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="削除アイコンが強調表示されている Custom Vision の [プロジェクト] ページ。":::

## <a name="check-which-modules-are-on-a-device"></a>デバイスに存在するモジュールを確認する

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT Hub]** アイコンをクリックします。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="[IoT Hub] アイコンが強調表示されている Azure portal ホームページ。" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. ターゲット デバイスの接続先の IoT ハブを選択します。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT ハブの一覧。":::

1. **[IoT Edge]** を選択し、 **[デバイス ID]** タブで目的のデバイスをクリックします。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge ホームページ。":::

1. **[モジュール]** タブにデバイス モジュールが一覧表示されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="選択したデバイスの [Iot Edge] ページに表示される [モジュール] タブの内容。" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>デバイスの削除

1. [Azure ポータル](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)にアクセスします。

1. **[IoT Hub]** アイコンをクリックします。

1. ターゲット デバイスの接続先の IoT ハブを選択します。

1. **[IoT Edge]** を選択し、ターゲット デバイス ID の横にあるチェック ボックスをオンにします。 ごみ箱アイコンをクリックすると、デバイスが削除されます。

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="IoT Edge ホームページで強調表示された削除アイコン。":::

## <a name="eye-module-troubleshooting-tips"></a>Eye モジュールのトラブルシューティングのヒント

**WebStreamModule** に問題がある場合は、ビジョン モデル推論を行う **azureeyemodule** が実行されていることを確認してください。 ランタイムの状態をチェックするには、[Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) に移動し、 **[すべてのリソース]**  ->  **\<your IoT hub>**  ->  **[IoT Edge]**  ->  **\<your device ID>** に移動します。 **[モジュール]** タブをクリックすると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="デバイス モジュールのランタイム状態画面。" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

**azureeyemodule** のランタイムの状態が **[running]\(実行中\)** になっていない場合は、 **[モジュールの設定]**  ->  **[azureeyemodule]** をクリックします。 **[モジュールの設定]** ページの **[必要な状態]** を **[running]\(実行中\)** に設定し、 **[更新]** をクリックします。

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="モジュール設定の構成画面。":::

## <a name="view-device-rtsp-video-stream"></a>デバイスの RTSP ビデオ ストリームを表示する

デバイスの RTSP ビデオ ストリームは、[Azure Percept Studio](./how-to-view-video-stream.md) または [VLC メディア プレーヤー](https://www.videolan.org/vlc/index.html)で表示します。

VLC メディア プレーヤーで RTSP ストリームを開くには、 **[Media]\(メディア\)**  ->  **[Open network stream]\(ネットワーク ストリームを開く\)**  ->  **[rtsp://<デバイスの IP アドレス>/result]** に移動します。

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。