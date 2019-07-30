---
title: Azure portal からモジュールをデプロイする - Azure IoT Edge | Microsoft Docs
description: Azure Portal を使用して IoT Edge デバイスにモジュールをデプロイする
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8337c8672eb886d79b38b2a38a74037f88604497
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448558"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal から Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

この記事では、Azure Portal を使用して配置マニフェストを作成し、IoT Edge デバイスにデプロイをプッシュする方法を紹介します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-portal.md)。

## <a name="select-your-device"></a>デバイスを選択する

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. **[Set Modules] \(モジュールの設定)** を選択します。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Azure Portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。 3 つのステップがあります。**モジュールの追加**、**ルートの指定**、および**デプロイの確認**。

### <a name="add-modules"></a>モジュールを追加する

1. ページの **[Container Registry の設定]** セクションで、モジュール イメージを格納するプライベート コンテナー レジストリにアクセスするための資格情報を指定します。

1. ページの **[デプロイ モジュール]** セクションで、 **[追加]** を選択します。

1. ドロップダウン リストでモジュールの種類を確認します。

   * **IoT Edge モジュール** - 既定のオプションです。
   * **Azure Stream Analytics モジュール** - Azure Stream Analytics ワークロードから生成されるモジュールのみです。
   * **Azure Machine Learning モジュール** - Azure Machine Learning ワークスペースから生成されたモデル イメージのみです。

1. **[IoT Edge モジュール]** を選択します。

1. モジュールの名前を入力し、コンテナー イメージを指定します。 例:

   * **名前** - tempSensor
   * **イメージの URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. 必要な場合は、省略可能なフィールドに入力します。 コンテナー作成オプション、再起動ポリシー、および必要な状態について詳しくは、「[edgeAgent の必要なプロパティ](module-edgeagent-edgehub.md#edgeagent-desired-properties)」をご覧ください。 モジュール ツインについて詳しくは、「[必要なプロパティの定義または更新](module-composition.md#define-or-update-desired-properties)」をご覧ください。

1. **[保存]** を選択します。

1. 手順 2 ～ 6 を繰り返してモジュールをデプロイに追加します。

1. **[次へ]** を選択して、ルートのセクションに進みます。

### <a name="specify-routes"></a>ルートを指定する

既定では、**FROM /\* INTO $upstream** として定義済みの **route** という名前のルートがウィザードから提供されます。つまり、モジュールによるメッセージ出力は IoT ハブに送信されます。  

[ルートの宣言](module-composition.md#declare-routes)の情報を使用してルートを追加または更新し、 **[次へ]** を選択して確認のセクションに進みます。

### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール ( **$edgeAgent** および **$edgeHub**) が宣言されていることに注目してください。 これらの 2 つのモジュールは、[IoT Edge ランタイム](iot-edge-runtime.md)を構成し、すべてのデプロイの既定値として使用されます。

デプロイ情報を確認し、 **[送信]** を選択します。

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを Portal の **[デバイスの詳細]** ページで表示できます。 このページには、デプロイした各モジュールの名前、およびデプロイ状態や終了コードなどの役立つ情報が表示されます。

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace からモジュールをデプロイする

Azure Marketplace は、アプリケーションとサービスのオンライン マーケットプレースであり、[IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)など、Azure での実行を認定されてそれに最適化されている、さまざまなエンタープライズ アプリケーションとソリューションを参照できます。 Azure Marketplace には、Azure portal の **[リソースの作成]** からアクセスすることもできます。

Azure Marketplace または Azure portal のいずれかから、IoT Edge モジュールをインストールできます。

1. モジュールを見つけて、デプロイ プロセスを開始します。

   * Azure portal:モジュールを見つけて、 **[作成]** を選択します。

   * Azure Marketplace:

     1. モジュールを見つけて、 **[今すぐ入手する]** を選択します。
     1. **[続行]** を選択して、プロバイダーの使用条件とプライバシー ポリシーを確認します。

1. お使いのサブスクリプションと、ターゲット デバイスをアタッチする IoT ハブを選択します。

1. **[デバイスへのデプロイ]** を選択します。

1. デバイスの名前を入力するか、または **[デバイスの検索]** を選択してハブに登録されているデバイスを参照します。

1. **[作成]** を選択して、必要な場合の他のモジュールの追加など、配置マニフェストの構成の標準的な処理を続行します。 イメージの URI、作成オプション、必要なプロパティなどの新しいモジュールの詳細はあらかじめ定義されていますが、変更できます。

## <a name="next-steps"></a>次の手順

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)の方法を学習します
