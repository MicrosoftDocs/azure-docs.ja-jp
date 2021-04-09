---
title: Azure Kinect の再生 API
description: Azure Kinect Sensor SDK を使用し、再生 API を使用して記録ファイルを開く方法について説明します。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, センサー, SDK, 深度, RGB, 記録, 再生, Matroska, mkv
ms.openlocfilehash: fe403f314c1df415537d090433f34627eb1249e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276657"
---
# <a name="the-azure-kinect-playback-api"></a>Azure Kinect の再生 API

Sensor SDK には、デバイス データを Matroska (.mkv) ファイルに記録するための API が用意されています。 Matroska コンテナー形式では、ビデオ トラック、IMU サンプル、デバイス較正が格納されます。 記録は、提供されている [k4arecorder](record-sensor-streams-file.md) コマンド ライン ユーティリティを使用して生成できます。 記録はまた、記録 API を使用して直接カスタマイズして記録することもできます。

記録 API の詳細については、「[`k4a_record_create()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gae14f4181e9688e710d1c80b215413831.html#gae14f4181e9688e710d1c80b215413831)」を参照してください。

Matroska ファイル形式の仕様の詳細については、[ファイル形式の記録](record-file-format.md)に関するページを参照してください。

## <a name="use-the-playback-api"></a>再生 API を使用する

記録ファイルは、再生 API を使用して開くことができます。 再生 API を使用すると、Sensor SDK の残りの部分と同じ形式でセンサー データにアクセスできます。

### <a name="open-a-record-file"></a>記録ファイルを開く

次の例では、[`k4a_playback_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gacb254ac941b2ab3c202ca68f4537f368.html#gacb254ac941b2ab3c202ca68f4537f368) を使用して記録を開き、記録の長さを出力した後、[`k4a_playback_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga76f415f2076f1c8c544e094a649306ff.html#ga76f415f2076f1c8c544e094a649306ff) を使用してファイルを閉じます。

```C
k4a_playback_t playback_handle = NULL;
if (k4a_playback_open("recording.mkv", &playback_handle) != K4A_RESULT_SUCCEEDED)
{
    printf("Failed to open recording\n");
    return 1;
}

uint64_t recording_length = k4a_playback_get_last_timestamp_usec(playback_handle);
printf("Recording is %lld seconds long\n", recording_length / 1000000);

k4a_playback_close(playback_handle);
```

### <a name="read-captures"></a>キャプチャを読み取る

ファイルが開いたら、記録からのキャプチャの読み取りを開始できます。 この次の例では、ファイル内の各キャプチャを読み取ります。

```C
k4a_capture_t capture = NULL;
k4a_stream_result_t result = K4A_STREAM_RESULT_SUCCEEDED;
while (result == K4A_STREAM_RESULT_SUCCEEDED)
{
    result = k4a_playback_get_next_capture(playback_handle, &capture);
    if (result == K4A_STREAM_RESULT_SUCCEEDED)
    {
        // Process capture here
        k4a_capture_release(capture);
    }
    else if (result == K4A_STREAM_RESULT_EOF)
    {
        // End of file reached
        break;
    }
}
if (result == K4A_STREAM_RESULT_FAILED)
{
    printf("Failed to read entire recording\n");
    return 1;
}
```

### <a name="seek-within-a-recording"></a>記録内でシークする

ファイルの最後に達したら、戻って、再び読み取ることもできます。 このプロセスは、[`k4a_playback_get_previous_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga54732e3aa0717e1ca4eb76ee385e878c.html#ga54732e3aa0717e1ca4eb76ee385e878c) を使用して逆方向に読み取ることによって行うこともできますが、記録の長さによっては非常に遅くなる場合があります。
代わりに、[`k4a_playback_seek_timestamp()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaea748994a121543bd77f90417cf428f6.html#gaea748994a121543bd77f90417cf428f6) 関数を使用してファイル内の特定のポイントに移動できます。

この例では、ファイル内のさまざまなポイントにシークするためにタイムスタンプ (マイクロ秒単位) を指定します。

```C
// Seek to the beginning of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to the end of the file
if (k4a_playback_seek_timestamp(playback_handle, 0, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the start
if (k4a_playback_seek_timestamp(playback_handle, 10 * 1000000, K4A_PLAYBACK_SEEK_BEGIN) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}

// Seek to 10 seconds from the end
if (k4a_playback_seek_timestamp(playback_handle, -10 * 1000000, K4A_PLAYBACK_SEEK_END) != K4A_RESULT_SUCCEEDED)
{
    return 1;
}
```

### <a name="read-tag-information"></a>タグ情報を読み取る

記録には、デバイスのシリアル番号やファームウェア バージョンなどのさまざまなメタデータを含めることもできます。 このメタデータは、[`k4a_playback_get_tag()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga320f966fc89b4ba0d758f787f70d5143.html#ga320f966fc89b4ba0d758f787f70d5143) 関数を使用してアクセスできる記録タグに格納されます。

```C
// Print the serial number of the device used to record
char serial_number[256];
size_t serial_number_size = 256;
k4a_buffer_result_t buffer_result = k4a_playback_get_tag(playback_handle, "K4A_DEVICE_SERIAL_NUMBER", &serial_number, &serial_number_size);
if (buffer_result == K4A_BUFFER_RESULT_SUCCEEDED)
{
    printf("Device serial number: %s\n", serial_number);
}
else if (buffer_result == K4A_BUFFER_RESULT_TOO_SMALL)
{
    printf("Device serial number too long.\n");
}
else
{
    printf("Tag does not exist. Device serial number was not recorded.\n");
}
```

### <a name="record-tag-list"></a>タグの一覧を記録する

記録ファイルに含まれている可能性があるすべての既定のタグの一覧を次に示します。 これらの値の多くは [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 構造体の一部として使用でき、[`k4a_playback_get_record_configuration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaab54a85c1f1e98d170d009042b449255.html#gaab54a85c1f1e98d170d009042b449255) 関数を使用して読み取ることができます。

タグが存在しない場合は、既定値が設定されていると見なされます。

| タグ名                     | Default value      | [`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) のフィールド | Notes     |
|------------------------------|--------------------|--------------------------------------|----------------------------------------------------------------------------------------------------------------|
| `K4A_COLOR_MODE`             | "OFF"              | `color_format` / `color_resolution`  | 指定できる値"OFF"、"MJPG_1080P"、"NV12_720P"、"YUY2_720P" など                                      |
| `K4A_DEPTH_MODE`             | "OFF"              | `depth_mode` / `depth_track_enabled` | 指定できる値"OFF"、"NFOV_UNBINNED"、"PASSIVE_IR" など                                                |
| `K4A_IR_MODE`                | "OFF"              | `depth_mode` / `ir_track_enabled`    | 指定できる値"OFF"、"ACTIVE"、"PASSIVE"                                                                    |
| `K4A_IMU_MODE`               | "OFF"              | `imu_track_enabled`                  | 指定できる値"ON"、"OFF"                                                                                   |
| `K4A_CALIBRATION_FILE`       | "calibration.json" | 該当なし                                  | [`k4a_device_get_raw_calibration()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8c4e46642cee3115aeb0b33e2b43b24f.html#ga8c4e46642cee3115aeb0b33e2b43b24f) をご覧ください。 |
| `K4A_DEPTH_DELAY_NS`         | "0"                | `depth_delay_off_color_usec`         | ナノ秒で格納された値、API ではマイクロ秒が提供されます。                                                        |
| `K4A_WIRED_SYNC_MODE`        | "STANDALONE"       | `wired_sync_mode`                    | 指定できる値"STANDALONE"、"MASTER"、"SUBORDINATE"                                                         |
| `K4A_SUBORDINATE_DELAY_NS`   | "0"                | `subordinate_delay_off_master_usec`  | ナノ秒で格納された値、API ではマイクロ秒が提供されます。                                                        |
| `K4A_COLOR_FIRMWARE_VERSION` | ""                 | 該当なし                                  | デバイス カラーのファームウェア バージョン (例: "1.x.xx")                                                            |
| `K4A_DEPTH_FIRMWARE_VERSION` | ""                 | 該当なし                                  | デバイス深度のファームウェア バージョン (例: "1.x.xx")                                                            |
| `K4A_DEVICE_SERIAL_NUMBER`   | ""                 | 該当なし                                  | 記録デバイスのシリアル番号                                                                                 |
| `K4A_START_OFFSET_NS`        | "0"                | `start_timestamp_offset_usec`        | 後の「[タイムスタンプの同期](record-playback-api.md#timestamp-synchronization)」を参照してください。                       |
| `K4A_COLOR_TRACK`            | なし               | 該当なし                                  | [ファイル形式の記録 - トラックの識別](record-file-format.md#identifying-tracks)に関するページを参照してください。                     |
| `K4A_DEPTH_TRACK`            | なし               | 該当なし                                  | [ファイル形式の記録 - トラックの識別](record-file-format.md#identifying-tracks)に関するページを参照してください。                     |
| `K4A_IR_TRACK`               | なし               | 該当なし                                  | [ファイル形式の記録 - トラックの識別](record-file-format.md#identifying-tracks)に関するページを参照してください。                     |
| `K4A_IMU_TRACK`              | なし               | 該当なし                                  | [ファイル形式の記録 - トラックの識別](record-file-format.md#identifying-tracks)に関するページを参照してください。                     |

## <a name="timestamp-synchronization"></a>タイムスタンプの同期

Matroska 形式では、記録が 0 のタイムスタンプから始まる必要があります。 [カメラを外部と同期している](record-external-synchronized-units.md)場合は、各デバイスからの最初のタイムスタンプは 0 以外でもかまいません。

デバイスからの元のタイムスタンプを記録と再生の間で保持するために、ファイルには各タイムスタンプに適用されるオフセットが格納されます。

`K4A_START_OFFSET_NS` タグは、記録の後にファイルを再同期できるようにタイムスタンプ オフセットを指定するために使用されます。 このタイムスタンプ オフセットをファイル内の各タイムスタンプに追加すると、元のデバイス タイムスタンプを再構成できます。

この開始オフセットは、[`k4a_record_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__record__configuration__t.html) 構造体でも使用できます。
