---
title: Azure Kinect ファームウェア ツール
description: Azure Kinect ファームウェア ツールを使用して、クエリを実行し、デバイス ファームウェアを更新する方法について説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, ファームウェア, 更新
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276717"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Azure Kinect DK ファームウェアを更新する

Azure Kinect ファームウェアツールを使用して、クエリを実行し Azure Kinect DK のデバイス ファームウェアを更新できます。

## <a name="list-connected-devices"></a>接続したデバイスを一覧表示する

-l オプションを使用して、接続されているデバイスの一覧を取得できます。  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>デバイス ファームウェアのバージョンを確認する

たとえば `AzureKinectFirmwareTool.exe -q` のように -q オプションを使用して、最初に接続されたデバイスの現在のファームウェア バージョンを確認できます。

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

複数のデバイスが接続されている場合は、次のようにコマンドに完全なシリアル番号を追加することで、クエリを実行するデバイスを指定できます。

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>デバイスのファームウェアの更新

このツールは、デバイスのファームウェアを更新する場合に最もよく使用されます。 `-u` オプションを使用してツールを呼び出して更新を行います。 ファームウェアの更新は、更新する必要があるファームウェア ファイルによっては数分かかることがあります。

ファームウェアの詳細な更新手順については、[Azure Kinect ファームウェアの更新](update-device-firmware.md)に関するページを参照してください。  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

複数のデバイスが接続されている場合は、コマンドに完全なシリアル番号を追加することで、クエリを実行するデバイスを指定できます。

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>デバイスをリセットする

デバイスを既知の状態にする必要がある場合は、-r オプションを使用して、接続された Azure Kinect DK をリセットできます。

複数のデバイスが接続されている場合は、コマンドに完全なシリアル番号を追加することで、クエリを実行するデバイスを指定できます。

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>ファームウェアを検査する

ファームウェアを検査することで、実際のデバイスを更新する前にファームウェア ビン ファイルからバージョン情報を取得できます。

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>ファームウェア更新ツールのオプション

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[デバイスのファームウェアの詳細な更新手順](update-device-firmware.md)
