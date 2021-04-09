---
title: Speech Devices SDK Roobo Smart Audio Dev Kit v1 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK Roobo Smart Audio Dev Kit v1 の使用を開始するための前提条件と手順です。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015292"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>デバイス: Roobo Smart Audio Dev Kit

この記事では、Roobo Smart Audio Dev Kit のデバイス固有の情報を提供します。

## <a name="set-up-the-development-kit"></a>開発キットをセットアップする

1. 開発キットには、2 つのマイクロ USB コネクタがあります。 左側のコネクタは開発キットに電源を供給するためのものです。下の画像では Power と強調表示されています。 右側のコネクタは開発キットを制御するためのものです。画像では Debug とマークされています。

    ![開発キットの接続](media/speech-devices-sdk/qsg-1.png)

1. マイクロ USB ケーブルを使用して電源ポートを PC または電源アダプターに接続して、開発キットに電源を供給します。 トップ ボードの下で緑色の電源インジケーターが点灯します。

1. 開発キットを制御するために、2 本目のマイクロ USB ケーブルを使用してデバッグ ポートをコンピューターに接続します。 信頼性の高い通信を確保するためには、高品質のケーブルを使用することが不可欠になります。

1. 直線または円形のいずれかの構成になるように開発キットの方向を指定します。

    |開発キットの構成|Orientation|
    |-----------------------------|------------|
    |円形|縦。マイクが天井に向くように置く|
    |Linear|横。マイクが自分を向くように置く (下のイメージを参照)|

    ![直線開発キットの向き](media/speech-devices-sdk/qsg-2.png)

1. 証明書をインストールし、サウンド デバイスのアクセス許可を設定します。 コマンド プロンプト ウィンドウで次のコマンドを入力します。

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > これらのコマンドでは、Android Studio のインストールの一部である Android Debug Bridge `adb.exe` を使用します。 このツールは C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools にあります。 このディレクトリをパスに追加して、`adb` を呼び出しやすくすることができます。 それ以外の場合は、`adb` を呼び出すすべてのコマンドで adb.exe の完全なインストール パスを指定する必要があります。
    >
    > エラー `no devices/emulators found` が発生した場合は、USB ケーブルが接続されていること、および USB ケーブルが高品質のケーブルであることを確認してください。 `adb devices` を使用すると、デバイスの一覧が返されるため、コンピューターが開発キットと通信できることを確認できます。
    >
    > [!TIP]
    > PC のマイクとスピーカーをミュートし、開発キットのマイクを操作していることがわかるようにします。 こうすることによって、PC からの音声によってデバイスが誤ってトリガされなくなります。

1. スピーカーを開発キットに接続する場合は、オーディオのライン出力に接続できます。3.5mm のアナログ プラグがある高品質のスピーカーを選択するようにします。

    ![Vysor オーディオ](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>開発情報

開発の詳細については、[Roobo の開発ガイド](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)を参照してください。

## <a name="audio"></a>オーディオ

Roobo には、すべてのオーディオをキャプチャし、メモリをフラッシュするツールが用意されています。 オーディオに関する問題のトラブルシューティングに役立ちます。 ツールのバージョンは、開発キットの構成ごとに提供されています。 [Roobo サイト](http://ddk.roobo.com/)でデバイスを選択し、ページの下部にある **[Roobo Tools]\(Roobo ツール\)** リンクを選択します。

## <a name="next-steps"></a>次のステップ

* [Android サンプル アプリを実行する](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)