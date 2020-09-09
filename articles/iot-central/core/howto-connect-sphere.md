---
title: Azure IoT Central で Azure Sphere デバイスを接続する | Microsoft Docs
description: Azure Sphere (DevKit) デバイスを Azure IoT Central アプリケーションに接続する方法について説明します。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.openlocfilehash: a42b483a5ca4b4948f14c1ccbf0b7af6e49224bb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715064"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Azure Sphere デバイスを Azure IoT Central アプリケーションに接続する

"*この記事は、デバイス開発者を対象としています。* "

この記事では、Azure Sphere (DevKit) デバイスを Azure IoT Central アプリケーションに接続する方法を説明します。

Azure Sphere とは、インターネットに接続されたデバイスのための通信およびセキュリティ機能が組み込まれている、セキュリティで保護された高水準のアプリケーション プラットフォームです。 これには、セキュリティで保護されたコネクテッド クロスオーバー マイクロコントローラー ユニット (MCU) と、カスタムの高レベル Linux ベース オペレーティングシステム (OS) と、継続的で更新可能なセキュリティを提供するクラウドベースのセキュリティ サービスが含まれます。 詳細については、「[Azure Sphere とは](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere)」を参照してください。

[Azure Sphere 開発キット](https://azure.microsoft.com/services/azure-sphere/get-started/)では、Azure Sphere アプリケーションのプロトタイプ作成と開発を始めるために必要なすべてのものが提供されています。 Azure IoT Central と Azure Sphere を使用すると、IoT ソリューションに対するエンド ツー エンドのスタックが有効になります。 Azure Sphere では、デバイスのサポートと、コード不要のマネージド IoT アプリケーション プラットフォームとしての IoT Central が提供されます。

このハウツー記事では、次のことを行います。

- IoT Central で、ライブラリの Azure Sphere DevKit デバイス テンプレートを使用して、Azure Sphere デバイスを作成します。
- Azure Sphere DevKit デバイスを Azure IoT 用に準備します。
- Azure Sphere DevKit を Azure IoT Central に接続します。
- IoT Central で、デバイスからのテレメトリを表示します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

- Azure IoT Central アプリケーション。
- Visual Studio 2019 バージョン 16.4 以降。
- [Seeed Studios 提供の Azure Sphere MT3620 開発キット](https://docs.microsoft.com/azure-sphere/hardware/mt3620-reference-board-design)。

> [!NOTE]
> 物理デバイスがない場合は、最初のステップの後で最後のセクションにスキップし、シミュレートされたデバイスを試してください。

## <a name="create-the-device-in-iot-central"></a>IoT Central でデバイスを作成する

IoT Central で Azure Sphere デバイスを作成するには:

1. Azure IoT Central アプリケーションで、 **[デバイス テンプレート]** タブを選択し、 **[+ 新規]** を選択します。 **[Use a featured device template]\(おすすめのデバイス テンプレートを使用する\)** セクションで、 **[Azure Sphere Sample Device]\(Azure Sphere サンプル デバイス\)** を選択します。

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Azure Sphere DevKit 用のデバイス テンプレート":::

1. デバイス テンプレートで、 **[概要]** という名前のビューを編集して、 **[Temperature]\(温度\)** と **[Button Press]\(ボタン押下\)** を表示します。

1. **[デバイスとクラウドのデータの編集]** ビューの種類を選択して、読み取りおよび書き込みプロパティ **[Status LED]\(状態 LED\)** を表示する別のビューを追加します。 **[Status LED]\(状態 LED\)** プロパティを、フォームの右側にある空の点線の四角形にドラッグします。 **[保存]** を選択します。

## <a name="prepare-the-device"></a>デバイスの準備

Azure Sphere DevKit デバイスを IoT Central に接続するには、[デバイスと開発環境をセットアップする](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT)必要があります。

## <a name="connect-the-device"></a>デバイスを接続する

サンプルを IoT Central に接続できるようにするには、[Azure IoT Central アプリケーションを構成してから、サンプルのアプリケーション マニフェストを変更する](https://aka.ms/iotcentral-sphere-git-readme)必要があります。

## <a name="view-the-telemetry-from-the-device"></a>デバイスからのテレメトリを表示する

デバイスが IoT Central に接続されたら、ダッシュボードでテレメトリを表示できます。

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Azure Sphere DevKit 用のダッシュボード":::

## <a name="create-a-simulated-device"></a>シミュレート対象デバイスを作成します

物理的な Azure Sphere DevKit デバイスがない場合は、シミュレートされたデバイスを作成して Azure IoT Central アプリケーションを試すことができます。

シミュレートされたデバイスを作成するには:

- **[Devices] > [Azure IoT Sphere]** を選択します
- **[+新規]** を選択します。
- 一意の **[デバイス ID]** と、わかりやすい **[デバイス名]** を入力します。
- **シミュレートされた**設定を有効にします。
- **［作成］** を選択します

## <a name="next-steps"></a>次のステップ

デバイス開発者にお勧めする次の手順は以下です。

- [Azure IoT Central のデバイス接続](./concepts-get-connected.md)について確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
