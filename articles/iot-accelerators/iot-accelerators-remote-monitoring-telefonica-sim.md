---
title: リモート監視ソリューションに SIM データを統合する - Azure| Microsoft Docs
description: この記事では、Telefónica SIM データをリモート監視ソリューションに統合する方法について説明します。
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606794"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM データをリモート監視ソリューションに統合する

IoT デバイスは、どこからでもデータ ストリームを送信できるようにする SIM カードを使用してクラウドに接続することがよくあります。 Azure IoT リモート監視ソリューションによって IoT 管理接続データの統合が可能になるため、オペレーターは、IoT SIM が提供するデータによってデバイスの正常性を追跡することもできます。

リモート監視は、追加設定なしで Telefónica IoT Connectivity との統合を可能にするため、IoT Connectivity Platform を使用しているユーザーはデバイスの SIM 接続性データをソリューションと同期できるようになります。 このソリューションは、GitHub [リポジトリ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)を介して他の IoT Connectivity プロバイダーをサポートするように拡張することができます。

このチュートリアルでは、以下の内容を学習します。

* Telefónica IoT SIM データをリモート監視ソリューションに統合する
* テレメトリをリアルタイムで表示する
* SIM データを表示する

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT の統合の設定

### <a name="prerequisites"></a>前提条件

この追加のリモート監視機能は、現在プレビューの段階です。 接続データを Azure リモート監視ソリューションに同期するには、次の手順に従います。

1. [Telefónica のサイト](https://iot.telefonica.com/contact) で要求を入力し、連絡先データなど、**[Azure Remote Monitoring]\ (Azure リモート監視\)** オプションを選択します。
2. Telefónica が、あなたのアカウントをアクティブにします。
3. あなたがまだ Telefónica のクライアントではなく、このサービス、または他の IoT Connectivity Cloud Ready サービスを利用したい場合は、[Telefónica のサイト](https://iot.telefonica.com/) にアクセスし、オプション **Connectivity** を選択してください。

### <a name="telefnica-sim-setup"></a>Telefónica SIM の設定
Telefónica SIM & Azure Twin デバイス ID の関連付けは、Telefónica IoT SIM「エイリアス」プロパティに基づいています。 

[Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM インベントリ > あなたの SIM を選択へ移動し、目的の Twin デバイス ID で各 SIM「エイリアス」を更新します。 このタスクは、一括モードで実行することもできます (Telefónica IoT Connectivity Platform のユーザー マニュアルを参照してください)。

このタスクは、一括モードで実行することもできます (Telefónica IoT Connectivity Platform のユーザー マニュアルを参照してください)

![Telefónica の更新](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

デバイスをリモート監視に接続するには、[C](iot-accelerators-connecting-devices-linux.md) または [Node](iot-accelerators-connecting-devices-node.md) を使用したこれらのチュートリアルに従います。 

## <a name="view-device-telemetry-and-sim-properties"></a>デバイス テレメトリと SIM プロパティの表示

Telefónica アカウントが正しく構成され、デバイスが接続されると、デバイスの詳細と SIM データを表示できます。

次の接続パラメーターが公開されます。

* ICCID
* IP
* ネットワークの有無
* SIM の状態
* ネットワークベースの場所
* 消費されたデータ トラフィック

![ダッシュボード](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>次の手順

Azure IoT リモート監視に SIM データを統合する方法の概要を確認できたので、ソリューション アクセラレータに推奨される次の手順を以下に示します。

* [Azure IoT リモート監視ソリューションの操作](quickstart-remote-monitoring-deploy.md)
* [高度な監視の実行](iot-accelerators-remote-monitoring-monitor.md)
* [デバイスの管理](iot-accelerators-remote-monitoring-manage.md)
* [デバイスの問題のトラブルシューティング](iot-accelerators-remote-monitoring-maintain.md)

