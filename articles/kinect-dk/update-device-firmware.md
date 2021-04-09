---
title: Azure Kinect DK ファームウェアを更新する
description: Azure Kinect ファームウェア ツールを使用して Azure Kinect DK デバイス ファームウェアを更新する方法について説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、ファームウェア、更新、復旧
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94356228"
---
# <a name="update-azure-kinect-dk-firmware"></a>Azure Kinect DK ファームウェアを更新する

このドキュメントは、Azure Kinect DK でデバイスのファームウェアを更新する方法に関するガイダンスを提供します。

Azure Kinect DK では、ファームウェアは自動的に更新されません。 [Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)を使用すると、ファームウェアを、使用可能な最新バージョンに手動で更新できます。

## <a name="prepare-for-firmware-update"></a>ファームウェアの更新を準備する

1. [SDK をダウンロードします](sensor-sdk-download.md)。
2. SDK をインストールします。
3. (SDK のインストール場所)\tools\SDK にあるインストール場所に、以下があります。

    - AzureKinectFirmwareTool.exe
    - ファームウェア フォルダーのファームウェア .bin ファイル (*AzureKinectDK_Fw_1.5.926614.bin* など)。

4. デバイスをホスト PC に接続し、さらに電源を入れます。

> [!IMPORTANT]
> ファームウェアの更新時は、USB と電源を接続したままにします。 更新中にいずれかの接続が切れると、ファームウェアが破損した状態になる可能性があります。

## <a name="update-device-firmware"></a>デバイスのファームウェアの更新

1. (SDK のインストール場所)\tools\folder でコマンド プロンプトを開きます。
2. Azure Kinect ファームウェア ツールを使用したファームウェアの更新

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    例:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. ファームウェアの更新が完了するまで待ちます。 画像のサイズによっては数分かかることがあります。

### <a name="verify-device-firmware-version"></a>デバイスのファームウェアのバージョンを確認する

1. ファームウェアが更新されていることを確認します。

    `AzureKinectFirmwareTool.exe -q`

2. 次の例を参照してください。

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. 上記の出力が表示された場合、ファームウェアは更新されています。

4. ファームウェアの更新後に [Azure Kinect ビューアー](azure-kinect-viewer.md)を実行して、すべてのセンサーが想定どおりに動作していることを確認できます。

## <a name="troubleshooting"></a>トラブルシューティング

ファームウェアの更新は、いくつかの理由で失敗する可能性があります。 ファームウェアの更新に失敗した場合は、次の軽減手順を試してください。

1. ファームウェア更新コマンドをもう一度実行してみてください。

2. ファームウェアのバージョンを照会して、デバイスがまだ接続されていることを確認します。        AzureKinectFirmareTool.exe

3. 他のすべてが失敗した場合は、[回復](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)手順に従って、出荷時のファームウェアに戻してから、もう一度やり直してください。

その他の問題については、[Microsoft サポート ページ](./index.yml)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)