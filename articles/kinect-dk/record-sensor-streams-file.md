---
title: クイックスタート - Azure Kinect センサー ストリームをファイルに記録する
description: このクイックスタートでは、Sensor SDK からファイルにデータ ストリームを記録する方法について説明します。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Azure, Kinect, 記録, 再生, リーダー, Matroska, MKV, ストリーム, 深度, RGB, カメラ, 色, IMU, オーディオ, センサー
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277832"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>クイック スタート:Azure Kinect センサー ストリームをファイルに記録する

このクイックスタートでは、[Azure Kinect レコーダー](azure-kinect-recorder.md) ツールを使用して、Sensor SDK からファイルにデータ ストリームを記録する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のことを想定しています。

- Azure Kinect DK がホスト PC に接続され、適切に電源が供給されている。
- ハードウェアの[設定](set-up-azure-kinect-dk.md)が完了している。

## <a name="create-recording"></a>記録を作成する

1. コマンド プロンプトを開き、[Azure Kinect レコーダー](azure-kinect-recorder.md)のパスを、インストール済みツールのディレクトリにある `k4arecorder.exe` として指定します (例: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`)。
2. 5 秒記録します。

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. 既定では、レコーダーでは NFOV Unbinned 深度モードを使用し、1080p RGB を 30 fps で IMU データを含めて出力します。 記録オプションの完全な概要とヒントについては、[Azure Kinect レコーダー](azure-kinect-recorder.md)に関する記事をご覧ください。

## <a name="play-back-recording"></a>記録を再生する

[Azure Kinect ビューアー](azure-kinect-viewer.md)を使用して、記録を再生できます。

1. [`k4aviewer.exe`](azure-kinect-viewer.md) を起動する。
2. **[Open Recording]\(記録を開く\)** タブを展開して、記録を開きます。

## <a name="next-steps"></a>次のステップ

センサー ストリームをファイルに記録する方法を学習したので、次のステップに進みましょう。

> [!div class="nextstepaction"]
> [最初の Azure Kinect アプリケーションを構築する](build-first-app.md)
