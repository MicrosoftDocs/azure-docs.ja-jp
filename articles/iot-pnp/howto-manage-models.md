---
title: リポジトリ内の IoT プラグ アンド プレイ プレビューのモデルを管理する | Microsoft Docs
description: Azure Certified for IoT ポータル、Azure CLI、Visual Studio Code を使用して、リポジトリ内のデバイス機能モデルを管理する方法。
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 4320a975f74d000bd492d291f5e610f591536b0d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979106"
---
# <a name="manage-models-in-the-repository"></a>リポジトリ内のモデルを管理する

IoT プラグ アンド プレイ プレビューのモデル リポジトリには、デバイス機能のモデルとインターフェイスが格納されます。 リポジトリは、ソリューション開発者がモデルとインターフェイスを検出し、使用できるようにします。

リポジトリの管理に使用できるツールは 3 つあります。

- Azure Certified for IoT ポータル
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>モデルのリポジトリ

デバイス機能のモデルとインターフェイスを格納するためのモデル リポジトリには、次の 2 種類があります。

- [Azure Certified for IoT デバイス カタログ](https://aka.ms/iotdevcat)内のデバイスに関するデバイス機能のモデルとインターフェイスを格納する「_パブリック リポジトリ_」が 1 つあります。 このリポジトリには、[一般的なインターフェイス](./concepts-common-interfaces.md)と [Microsoft のパートナーによって公開された DCM およびインターフェイス](./howto-onboard-portal.md)も格納されます。 デバイスを認定し、そのデバイス機能モデルをパブリック リポジトリに追加する方法については、チュートリアル「[IoT プラグ アンド プレイ デバイスの認定](./tutorial-certification-test.md)」を参照してください。
- 複数の「_会社リポジトリ_」があります。 [Azure Certified for IoT ポータルにオンボード](./howto-onboard-portal.md)すると、組織の会社リポジトリが自動的に作成されます。 会社リポジトリを使用して、開発およびテスト中にデバイス機能のモデルとインターフェイスを格納できます。

## <a name="azure-certified-for-iot-portal"></a>Azure Certified for IoT ポータル

[Azure Certified for IoT ポータル](https://preview.catalog.azureiotsolutions.com)では、次のタスクを実行できます。

- [IoT デバイスの認定プロセスを完了する](./tutorial-certification-test.md)。
- IoT プラグ アンド プレイのデバイス機能モデルを検索する。 これらのモデルを使用すると、[IoT 対応デバイスをすばやく構築し、ソリューションに統合](./quickstart-connect-pnp-device-solution.md)できます。

## <a name="azure-cli"></a>Azure CLI

Azure CLI には、IoT プラグ アンド プレイのパブリック リポジトリと会社リポジトリ内のデバイス機能のモデルとインターフェイスを管理するためのコマンドが用意されています。 詳細については、「[Azure CLI 用 Azure IoT 拡張機能のインストールおよび使用のハウツー ガイド](./howto-install-pnp-cli.md)」を参照してください。

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code で **[Model Repository]** \(モデル リポジトリ\) ビューを開くには、次のようにします。

1. Visual Studio Code を開き、**Ctrl + Shift + P** キーを押し、「**IoT Plug and Play:Open Model Repository**」(IoT プラグ アンド プレイ: モデル リポジトリを開く) と入力して選択します。

1. **[Open Public Model Repository]** \(パブリック モデル リポジトリを開く\) または **[Open Organizational Model Repository]** \(組織のモデル リポジトリを開く\) を選択できます。 会社のモデル リポジトリの場合、モデル リポジトリの接続文字列を入力する必要があります。 この接続文字列は、[Azure Certified for IoT ポータル](https://preview.catalog.azureiotsolutions.com)の **[Company repository]** \(会社リポジトリ\) の **[Connection strings]** \(接続文字列\) タブで確認できます。

1. 新しいタブで **[Model Repository]** \(モデル リポジトリ\) ビューが開きます。

    このビューを使用して、デバイス機能のモデルとインターフェイスを追加、ダウンロード、削除します。 フィルターを使用して、一覧内の特定の項目を検索することができます。

1. 会社のモデル リポジトリとパブリック モデル リポジトリを切り替えるには、**Ctrl + Shift + P** キーを押し、「**IoT Plug and Play:Sign out Model Repository**」(IoT プラグ アンド プレイ: モデル リポジトリからサインアウトする) と入力して選択します。 次に、 **[IoT Plug and Play:Open Model Repository]\(IoT プラグ アンド プレイ: モデル リポジトリを開く\)** コマンドをもう一度使用します。

> [!NOTE]
> VS Code では、パブリック モデル リポジトリは読み取り専用です。 Microsoft パートナーは、[Azure Certified for IoT ポータル](https://preview.catalog.azureiotsolutions.com)でパブリック リポジトリを更新できます。

## <a name="next-steps"></a>次の手順

推奨される次の手順は、[IoT プラグ アンド プレイ デバイスを認定のために送信する](tutorial-certification-test.md)方法を学習することです。
