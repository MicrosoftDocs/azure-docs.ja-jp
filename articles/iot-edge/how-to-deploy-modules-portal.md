---
title: Azure portal からモジュールをデプロイする - Azure IoT Edge
description: Azure portal で IoT Hub を使用して、デプロイマニフェストでの構成時に、 IoT Edge モジュールを、IoT Hub から IoT Edge デバイスにプッシュします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 754c106db42f3f0695ad023e736993bee82e9757
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82133917"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal から Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

この記事では、Azure Portal を使用して配置マニフェストを作成し、IoT Edge デバイスにデプロイをプッシュする方法を紹介します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[IoT Edge モジュールを大規模にデプロイおよび監視する](how-to-deploy-at-scale.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device.md#register-in-the-azure-portal)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Azure Portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。 3 つのステップがあります。**モジュールの追加**、**ルートの指定**、および**デプロイの確認**。

### <a name="select-device-and-add-modules"></a>デバイスを選択してモジュールを追加する

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. 左側のウィンドウで、メニューの **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. 上部のバーで **[モジュールの設定]** を選択します。
1. ページの **[Container Registry の設定]** セクションで、モジュール イメージを格納するプライベート コンテナー レジストリにアクセスするための資格情報を指定します。
1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** を選択します。
1. ドロップダウン メニューでモジュールの種類を確認します：

   * **IoT Edge モジュール** - モジュール名とコンテナー イメージの URI を指定します。 たとえば、サンプルの SimulatedTemperatureSensor モジュールのイメージ URI は `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`です。 モジュール イメージがプライベート コンテナー レジストリに格納されている場合は、このページに資格情報を追加して、イメージにアクセスします。
   * **Marketplace モジュール** - Azure Marketplace でホストされているモジュール。 Marketplace モジュールによっては、追加の構成が必要になる場合があります。そのため、[Azure Marketplace IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 一覧でモジュールの詳細を確認してください。
   * **Azure Stream Analytics モジュール** - Azure Stream Analytics ワークロードから生成されるモジュールです。

1. モジュールを追加したら、一覧からモジュール名を選択してモジュールの設定を開きます。 必要な場合は、省略可能なフィールドに入力します。 コンテナー作成オプション、再起動ポリシー、および必要な状態について詳しくは、「[edgeAgent の必要なプロパティ](module-edgeagent-edgehub.md#edgeagent-desired-properties)」をご覧ください。 モジュール ツインについて詳しくは、「[必要なプロパティの定義または更新](module-composition.md#define-or-update-desired-properties)」をご覧ください。
1. 必要に応じて、手順 5 ～ 8 を繰り返して、モジュールをデプロイに追加します。
1. **ルート** 　でルート のセクションに進みます。

### <a name="specify-routes"></a>ルートを指定する

**[ルート]** タブで、モジュールと IoT Hub の間でメッセージが渡される方法を定義します。 メッセージは、名前と値のペアを使用して作成されます。 既定では、ルートは **route** という名前で、**FROM /messages/\* INTO $upstream** として定義されています。つまり、任意のモジュールによって出力されたすべてのメッセージは IoT Hub に送信されます。  

[ルートの宣言](module-composition.md#declare-routes) の情報を使用してルートを追加または更新し、 **[次へ]：レビュー + 作成** を選択して、ウィザードの次の手順に進みます。

### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール ( **$edgeAgent** および **$edgeHub**) が宣言されていることに注目してください。 これらの 2 つのモジュールは、[IoT Edge ランタイム](iot-edge-runtime.md)を構成し、すべてのデプロイの既定値として使用されます。

デプロイ情報を確認し、 **[作成]** を選択します。

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイすると、そのすべてを IoT Hub のデバイスの詳細ページで表示できます。 このページには、デプロイした各モジュールの名前、およびデプロイ状態や終了コードなどの役立つ情報が表示されます。

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace からモジュールをデプロイする

[Azure Marketplace](https://azuremarketplace.microsoft.com/) は、アプリケーションとサービスのオンライン マーケットプレースであり、[IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) など、Azure での実行を認定されてそれに最適化されている、さまざまなエンタープライズ アプリケーションとソリューションを参照できます。

Azure Marketplace および IoT Hub から、IoT Edge モジュールをデプロイできます。

### <a name="deploy-from-azure-marketplace"></a>Azure Marketplace からデプロイする

Marketplace の IoT Edge モジュールを調べて、必要なモジュールを見つけたら、**作成** または**今すぐ取得する**を選択してデプロイできます。 デプロイウィザードの手順を続行します。これは、選択した IoT Edge モジュールによって異なります：

1. **[続行]** を選択して、プロバイダーの使用条件とプライバシー ポリシーを確認します。 最初に連絡先情報を提供する必要があります。
1. お使いのサブスクリプションと、ターゲット デバイスをアタッチする IoT ハブを選択します。
1. **[デバイスへのデプロイ]** を選択します。
1. デバイスの名前を入力するか、または **[デバイスの検索]** を選択してハブに登録されているデバイスを参照します。
1. **[作成]** を選択して、必要な場合の他のモジュールの追加など、配置マニフェストの構成の標準的な処理を続行します。 イメージの URI、作成オプション、必要なプロパティなどの新しいモジュールの詳細はあらかじめ定義されていますが、変更できます。

モジュールが Azure portal の IoT Hub にデプロイされていることを確認します。 デバイスを選択し、 **[モジュールの設定]** を選択します。モジュールは **IoT Edge モジュール** セクションに表示されます。

### <a name="deploy-from-azure-iot-hub"></a>Azure IoT Hub からのデプロイ

Azure Marketplace のモジュールを、Azure portal の IoT Hub デバイスにすばやくデプロイできます。

1. Azure portal で、お使いの IoT Hub に移動します。
1. 左側のペインの **[デバイスの自動管理]** の下で、 **[IoT Edge]** を選択します。
1. デプロイを受信する IoT Edge デバイスを選択します。
1. 上部のバーで **[モジュールの設定]** を選択します。
1. **[IoT Edge モジュール]** セクションで、 **[追加]** をクリックし、ドロップダウンメニューから **[Marketplace モジュール]** を選択します。

![IoT Hub にモジュールを追加する](./media/how-to-deploy-modules-portal/iothub-add-module.png)

**[IoT Edge モジュールの Marketplace]** ページでモジュールを選択します。 選択したモジュールは、サブスクリプション、リソース グループ、およびデバイスに対して自動的に構成されます。 その後、それは IoT Edge モジュールの一覧に表示されます。 このモジュールには追加の構成が必要な場合があります。

> [!TIP]
> Azure IoT Hub の IoT Edge モジュールに関する情報は限られています。 詳細については、Azure Marketplace の [IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) に関するページを参照してください。

**この記事の前で記載した [[ルートの指定]](#specify-routes) と [[デプロイの確認]](#review-deployment) で説明されているように、デプロイをルーティング**  して続行します。

## <a name="next-steps"></a>次のステップ

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)の方法を学習します
