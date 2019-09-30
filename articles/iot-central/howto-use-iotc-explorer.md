---
title: Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する
description: IoT Central エクスプローラー CLI を使用して、デバイスのメッセージを監視し、デバイス ツインの変更を観察します。
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845691"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する

*このトピックはビルダーおよび管理者向けです。*

IoT Central エクスプローラー CLI を使用して、デバイスから IoT Central に送信されるメッセージを確認し、IoT Hub ツインでの変更を観察します。 このオープン ソース ツールを使用して、デバイスの接続状態に関するより詳細な分析情報を取得し、クラウドに達しないデバイス メッセージの問題や、ツイン変更に応答しないデバイスの問題を診断することができます。

[GitHub の iotc-explorer リポジトリを参照します。](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>前提条件

+ Node.js バージョン 8.x 以上 - https://nodejs.org
+ アプリケーションの管理者が、iotc-explorer で使用するアクセス トークンを生成する必要があります。

## <a name="install-iotc-explorer"></a>iotc-explorer をインストールする

インストールするには、コマンド ラインから次のコマンドを実行します。

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 通常は、Unix のような環境で `sudo` を使用して、インストール コマンドを実行する必要があります。

## <a name="run-iotc-explorer"></a>iotc-explorer を実行する

次のセクションでは、`iotc-explorer` を実行するときに使用できる一般的なコマンドとオプションについて説明します。 コマンドおよびオプションの完全なセットを表示するには、`--help` を `iotc-explorer` またはそのいずれかのサブコマンドに渡します。

### <a name="login"></a>ログイン

作業を開始する前に、ご利用の IoT Central アプリケーションの管理者に連絡して、使用するアクセス トークンを取得してもらう必要があります。 管理者は次の手順を行います。

1. **[管理]** 、 **[アクセス トークン]** の順に移動します。
1. **[トークンの生成]** を選択します。
    ![アクセス トークン ページのスクリーンショット](media/howto-use-iotc-explorer/accesstokenspage.png)

1. トークン名を入力し、 **[次へ]** を選択し、 **[コピー]** を選択します。
    > [!NOTE]
    > トークンの値が表示されるのは 1 回だけなので、ダイアログを閉じる前にコピーする必要があります。 ダイアログを閉じた後、再び表示されることはありません。

    ![アクセス トークンのコピーに関するダイアログのスクリーンショット](media/howto-use-iotc-explorer/copyaccesstoken.png)

次のようにトークンを使用して CLI にログインできます。

```cmd/sh
iotc-explorer login "<Token value>"
```

シェル履歴にトークンを保持したくない場合は、次のようにトークンを省略し、代わりにプロンプトが表示されたときに指定することができます。

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>デバイス メッセージを監視する

`monitor-messages` コマンドを使用して、ご利用のアプリケーションで特定のデバイスまたはすべてのデバイスからのメッセージを監視することができます。 このコマンドは、新しいメッセージが到着すると連続してそれらを出力するウォッチャーを開始します。

アプリケーションですべてのデバイスを監視するには、次のコマンドを実行します。

```cmd/sh
iotc-explorer monitor-messages
```

出力:

![monitor-messages コマンドの出力](media/howto-use-iotc-explorer/monitormessages.png)

特定のデバイスを監視するには、単にコマンドの末尾にデバイス ID を追加します。

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

次のようにコマンドに `--raw` オプションを追加することにより、よりコンピューターで解釈しやすい形式で出力することもできます。

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>デバイス ツインを取得する

`get-twin` コマンドを使用して、IoT Central デバイス用のツインのコンテンツを取得することができます。 これを行うには、次のコマンドを実行します。

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

出力:

![get-twin コマンドの出力](media/howto-use-iotc-explorer/getdevicetwin.png)

`monitor-messages` の場合と同じように、`--raw` オプションを渡して、コンピューターでより解釈しやすい出力にすることができます。

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>次の手順

これで IoT Central エクスプローラーの使用方法についての学習が終わりました。推奨される次の手順は、[IoT Central でのデバイスの管理](howto-manage-devices.md)について確認することです。
