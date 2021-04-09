---
title: Azure Marketplace での IoT Edge モジュール オファーの認定前チェックリスト
description: Azure Marketplace で IoT Edge モジュール オファーを発行するための特定の認定要件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: c1d4c9be1f76e62be3c17d4dec22479db003b77a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608259"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>IoT Edge モジュールの認定前チェックリスト

> [!NOTE]
> 認定依頼を送信する前に、発行元はこのチェックリストを確認し、モジュールの機能を検証することを強くお勧めします。 これにより、変更や再依頼する必要が減少し、認定プロセスが高速化されます。

## <a name="validation-of-image"></a>イメージの検証

Edge モジュール イメージを送信する準備ができたら、こちらの手順を実行して、Microsoft が必要とする方法でイメージが動作することを確認します。

### <a name="steps-to-perform-in-the-azure-portal"></a>Azure portal で実行する手順

1. [Azure Portal](https://partner.microsoft.com/)を開きます。
1. リソース グループを作成します。
1. IoT Hub を作成します。
1. IoT Edge デバイスを作成します。
1. 接続文字列をコピーし、メモ帳に保存します。
1. **[set Modules on Edge Device Created]\(作成した Edge デバイスにモジュールを設定する\)** を選択します。
1. 最新バージョンのイメージが配置されている ACR の詳細を追加します。
1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選択し、以下を指定します。
    - モジュール設定のイメージ URI
    - 環境変数 (パートナー センターで追加したものと同じ)
    - コンテナー作成オプション (パートナー センターで追加したものと同じ)
    - モジュール ツインの設定 (パートナー センターで追加したものと同じ)
1. ルートを追加します (パートナーセンターで追加したものと同じ)。
1. **[確認および作成]** を選択します。

Edge モジュールが、Azure で作成された Edge デバイスにデプロイされます。

### <a name="steps-to-perform-on-the-device"></a>デバイスで実行する手順

#### <a name="device-details"></a>[デバイスの詳細]

認定チームは、次のハードウェアを使用して、さまざまなアーキテクチャでイメージを検証します。

- X64 イメージの場合、Ubuntu Server 18.04 または Ubuntu Server 16.04 を実行する、構成サイズが Standard D2s v3 の Azure VM。
- Azure Resource Manager 32 イメージの場合、Raspberry Pi 3 Model B。
- Azure Resource Manager 64 イメージの場合、NVIDIA Jetson Nano 4Gb。

#### <a name="steps"></a>手順

1. Putty を使用して、作成したデバイスと VM にアクセスできることを確認します。
1. [IoT Edge ランタイム](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge)をデバイスにダウンロードします。
1. 手順 5 でコピーした接続文字列を config.yaml ファイルに貼り付けて更新します。
1. `sudo systemctl restart iotedge` を使用して Edge モジュールを再起動します。
1. `sudo iotedge list` を使用して、モジュールがデバイスにデプロイされたかどうかを確認します。実行状態になっている必要があります。
1. `sudo iotedge logs “Module Name“ -f` を使用して、デプロイされたモジュールのログにエラーがないことを確認します。 既知のエラーがある場合は、オファーを送信する前に、パートナー センターの **[Notes to reviewer]\(レビュー担当者へのメモ\)** でこれについて説明します。

## <a name="metadata-validation"></a>メタデータの検証

次の点を確認します。

- 最新のタグが、パートナー センターと Azure Container Registry の両方に一覧表示されている。
- 最小ハードウェア要件が、オファーの説明に追加されている。
- パートナー センターで、Azure Container Registry のユーザー名とパスワードが更新され、追加されている。
- 必要な **ツイン プロパティ** が正確であること " *(該当する場合)* "。
- 必要な **環境変数** が正確であること " *(該当する場合)* "。
- 必要な **作成オプション** が正確であること " *(該当する場合)* "。
- リード管理の接続文字列が存在する。
- プライバシー ポリシーが存在する
- 使用条件が存在する
- サポートされている IoT Edge デバイス リンクを [Azure IoT デバイス カタログ](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible)から追加する 

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースからモジュールをデプロイする](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [パートナー センターで Edge モジュールを公開する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [IoT Edge モジュールをデプロイする](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
