---
title: Azure Percept Audio と音声モジュールに関する問題のトラブルシューティング
description: Azure Percept Audio と azureearspeechclientmodule のトラブルシューティングのヒントを得る
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635571"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio と音声モジュールのトラブルシューティング

音声アシスタント アプリケーションの問題をトラブルシューティングするには、以下のガイドラインを使用してください。

## <a name="collecting-speech-module-logs"></a>音声モジュールのログを収集する

これらのコマンドを実行するには、[Azure Percept DK Wi-Fi アクセス ポイントに接続して SSH で開発キットに接続](./how-to-ssh-into-percept-dk.md)し、SSH ターミナルでコマンドを入力します。

```console
sudo iotedge logs azureearspeechclientmodule
```

詳しく分析するために、出力を .txt ファイルにリダイレクトするには、次の構文を使用します。

```console
sudo [command] > [file name].txt
```

出力を .txt ファイルにリダイレクトしたら、SCP を使用してファイルをホスト PC にコピーします。

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

<local host file path> は、.txt ファイルのコピー先となるホスト PC 上の場所です。 <remote username> は、[オンボード エクスペリエンス](./quickstart-percept-dk-set-up.md)中に選択した SSH ユーザー名です。 Azure Percept DK オンボード エクスペリエンス中に SSH ログインを設定しなかった場合、リモートユーザー名は root になります。

## <a name="checking-runtime-status-of-the-speech-module"></a>音声モジュールのランタイムの状態を確認する

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** になっているかどうかを確認します。 デバイス モジュールのランタイムの状態を特定するには、[Azure portal](https://portal.azure.com/) を開いて **[すべてのリソース]**  ->  **\<your IoT hub>**  ->  **[IoT Edge]**  ->  **\<your device ID>** に移動します。 **[モジュール]** タブをクリックすると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure portal のエッジ デバイス ページ。":::

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** として表示されていない場合は、 **[モジュールの設定]**  ->  **[azureearspeechclientmodule]** をクリックします。 **[モジュールの設定]** ページの **[必要な状態]** を **[running]\(実行中\)** に設定し、 **[更新]** をクリックします。

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED インジケーターについて

LED インジケーターを使用して、デバイスの状態を把握できます。 通常、"*電源投入*" 後、モジュールの初期化が完了するまでに約 2 分かかります。 初期化の手順を終えると、次のことが確認できます。

1. 中央の 1 つの LED が白色 - デバイスの電源がオンです。
2. 中央の 1 つの LED が白色で点滅 - 認証が進行中です。
3. デバイスが認証されて使用できる状態になると、3 つの LED がすべて青色に変化します。

|LED|LED の状態|Ear SoM 状態|
|---|---------|--------------|
|L02|1 つが白色、静的オン|電源投入 |
|L02|1 つが白色、0.5 Hz 点滅|認証が進行中 |
|L01、L02、L03|3 つが青色、静的オン|キーワードの待機中|
|L01、L02、L03|LED アレイ点滅、20fps |リッスン中または話し中|
|L01、L02、L03|LED アレイ レーシング、20fps|処理中|
|L01、L02、L03|3 つが赤色、静的オン |Mute|

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。
