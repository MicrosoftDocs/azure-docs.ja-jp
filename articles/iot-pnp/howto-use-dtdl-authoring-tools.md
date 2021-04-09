---
title: ツールを使用して DTDL モデルを作成および検証する | Microsoft Docs
description: Visual Studio Code または Visual Studio 2019 用の DTDL エディターをインストールし、それを使用して IoT プラグ アンド プレイ モデルを作成します。
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280201"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>DTDL 作成ツールをインストールして使用する

[Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) モデルは JSON ファイルです。 Visual Studio code または Visual Studio 2019 用の拡張機能を使用してこれらのモデル ファイルを作成し、検証することができます。

## <a name="install-and-use-the-vs-code-extension"></a>VS Code 拡張機能をインストールして使用する

VS Code 用の DTDL 拡張機能により、以下の DTDL 作成機能が追加されます。

- DTDL v2 構文の検証。
- IntelliSense (オートコンプリートを含む) は、言語の構文についてユーザーを支援します。
- コマンド パレットからインターフェイスを作成する機能。

DTDL 拡張機能をインストールするには、「[Visual Studio Code 用の DTDL エディター](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)」を参照してください。 VS Code の [拡張機能] ビューで **DTDL** を検索することもできます。

拡張機能をインストールしたら、VS Code で DTDL モデル ファイルを作成するのに役立てます。

- 拡張機能により、DTDL モデル ファイルでの構文の検証が提供され、次のスクリーンショットに示すように誤りが強調表示されます。

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="VS Code でのモデルの検証":::

- IntelliSense とオートコンプリートは、DTDL モデルの編集中に利用します。

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="VS Code で DTDL モデルに対して IntelliSense を使用する":::

- 新しい DTDL インターフェイスを作成します。 **DTDL: Create Interface** を使用すると、新しいインターフェイスを含む JSON ファイルが作成されます。 インターフェイスには、テレメトリ、プロパティ、コマンド定義の例が含められます。

## <a name="install-and-use-the-visual-studio-extension"></a>Visual Studio 拡張機能をインストールして使用する

Visual Studio 2019 用の DTDL 拡張機能により、以下の DTDL 作成機能が追加されます。

- DTDL v2 構文の検証。
- IntelliSense (オートコンプリートを含む) は、言語の構文についてユーザーを支援します。

DTDL 拡張機能をインストールするには、「[VS 2019 に対する DTDL 言語のサポート](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)」を参照してください。 Visual Studio の **[拡張機能の管理]** で、**DTDL** を検索することもできます。

拡張機能をインストールしたら、Visual Studio で DTDL モデル ファイルを作成するのに役立てます。

- 拡張機能により、DTDL モデル ファイルでの構文の検証が提供され、次のスクリーンショットに示すように誤りが強調表示されます。

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Visual Studio でのモデルの検証":::

- IntelliSense とオートコンプリートは、DTDL モデルの編集中に利用します。

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Visual Studio で DTDL モデルに対して IntelliSense を使用する":::

## <a name="next-steps"></a>次のステップ

方法に関するこの記事では、Visual Studio Code および Visual Studio 2019 用の DTDL 拡張機能を使用して、DTDL モデル ファイルを作成し、検証する方法について説明しました。 推奨される次のステップは、[モデルやデバイスを扱う Azure IoT エクスプローラー](./howto-use-iot-explorer.md)の使用方法について学習することです。
