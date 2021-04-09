---
title: 外部の同期されたデバイスで Azure Kinect レコーダーを使用する
description: Azure Kinect レコーダーを使用して、外部同期が構成されているデバイスからのデータを記録する方法について説明します。
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, センサー, ビューアー, 外部同期, フェーズ遅延, 深度, RGB, カメラ, オーディオ ケーブル, レコーダー
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276662"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>外部の同期されたデバイスで Azure Kinect レコーダーを使用する

この記事では、[Azure Kinect レコーダー](azure-kinect-recorder.md)で、外部同期が構成されているデバイスからのデータを記録する方法に関するガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

- [複数の Azure Kinect DK ユニットの外部同期を設定](https://support.microsoft.com/help/4494429)します。

## <a name="external-synchronization-constraints"></a>外部同期の制約

- マスター デバイスには SYNC IN ケーブルを接続できません。
- 同期を有効にするにはマスター デバイスが RGB カメラをストリーミングする必要があります。
- すべてのユニットが同じカメラ構成 (フレーム レートと解像度) を使用する必要があります。
- すべてのユニットが同じデバイス ファームウェアを実行する必要があります ([ファームウェアの更新](update-device-firmware.md)の手順)。
- すべての下位デバイスをマスター デバイスの前に起動する必要があります。
- すべてのデバイスに同じ露出値を設定する必要があります。
- 各下位の *[Delay off master] (マスターの遅延)* 設定はマスター デバイスを基準としています。

## <a name="record-when-each-unit-has-a-host-pc"></a>各ユニットにホスト PC が存在する場合の記録

次の例では、各デバイスに独自の専用ホスト PC が存在します。
USB 帯域幅や CPU/GPU 使用率の問題を防止するために、デバイスを専用 PC に接続することをお勧めします。

### <a name="subordinate-1"></a>下位-1

1. 最初のユニットのレコーダーを設定します。

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. デバイスが待機を開始します。

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>下位-2

1. 2 番目のユニットのレコーダーを設定します。

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. デバイスが待機を開始します。

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. マスターで記録を開始します。

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. 記録が完了するまで待ちます。

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>複数のユニットが 1 台のホスト PC に接続されている場合の記録

複数の Azure Kinect DK を 1 台のホスト PC に接続することができます。 ただし、それは USB 帯域幅やホスト コンピューティングにとって非常に厳しい要求になる場合があります。 この要求を削減するには、次のようにします。

- 各デバイスを独自の USB ホスト コントローラーに接続します。
- デバイスごとの深度エンジンを処理できる強力な GPU を用意します。
- 必要なセンサーのみを記録し、より低いフレーム レートを使用します。

常に、まず下位デバイスを起動し、最後にマスターを起動します。

## <a name="subordinate-1"></a>下位-1

1. 下位でレコーダーを起動します。

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. デバイスが待機状態に移行します。

## <a name="master"></a>Master

1. マスター デバイスを起動します。

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. 記録が完了するまで待ちます。

## <a name="playing-recording"></a>記録の再生

[Azure Kinect ビューアー](azure-kinect-viewer.md)を使用して記録を再生できます。



## <a name="tips"></a>ヒント

- 同期されたカメラの記録には手動露出を使用します。 RGB カメラの自動露出は、時刻同期に影響を与える可能性があります。
- 下位デバイスを再起動すると、同期が失われます。
- 一部の[カメラ モード](hardware-specification.md#depth-camera-supported-operating-modes)では最大 15 fps がサポートされます。デバイス間でモード/フレーム レートを混在させないようにすることをお勧めします。
- 複数のユニットを 1 台の PC に接続すると、USB 帯域幅が簡単に飽和する場合があります。デバイスごとに個別のホスト PC を使用することを検討してください。 CPU/GPU コンピューティングにも注意を払ってください。
- 信頼性を向上させるために必要ない場合は、マイクや IMU を無効にしてください。

何か問題がある場合は、[トラブルシューティング](troubleshooting.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

- [外部同期を設定する](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- レコーダーの設定や追加情報については、[Azure Kinect レコーダー](azure-kinect-recorder.md)。
- レコーダーでは使用できない記録の再生や RGB カメラのプロパティの設定については、[Azure Kinect ビューアー](azure-kinect-viewer.md)。
- デバイス ファームウェアの更新については、[Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)。
