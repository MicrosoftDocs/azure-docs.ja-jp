---
title: Azure Marketplace の IoT Edge モジュール オファー
description: Azure Marketplace での IoT Edge モジュール オファーの公開について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 90267f2b75b7b9c1e77a45d7e3faa4b0bf6dd63a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708545"
---
# <a name="iot-edge-modules"></a>IoT Edge のモジュール

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) プラットフォームは Microsoft Azure に支えられています。  このプラットフォームでは、ユーザーは IoT  デバイスで直接実行するクラウド ワークロードをデプロイすることができます。  IoT Edge モジュールでは、オフラインのワークロードを実行でき、ローカルでデータを分析することができます。 このプランでは、帯域幅を保存したり、ローカルおよび機密データを保護することができ、低待機応答時間を提供します。  このような構築済みのワークロードを活用するためにいくつかのオプションがあります。 今までは、ごく少数の Microsoft のファーストパーティ  ソリューションが使用可能でした。  カスタマイズ IoT ソリューションの構築に時間とリソースを使う必要がありました。

[Azure Marketplace の IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)により、パブリッシャーは IoT の対象ユーザーへのソリューションの公開や販売を 1 か所で行うことができるようになりました。 IoT 開発者は、適当な機能を検索して、それを購入することによって、自分のソリューションの開発を加速することができます。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace の IoT Edge モジュールの主なメリット

| **パブリッシャーが対象**    | **お客様 (IoT 開発者) が対象**  |
| :------------------- | :-------------------|
| IoT Edge ソリューションをビルドおよびデプロイしようとしている何百万もの開発者にまで届きます。  | 安全でテストされたコンポーネントを使用するという自信を持って、IoT Edge ソリューションを作成します。 |
| 一度発行して、コンテナーをサポートする任意の IoT Edge ハードウェア全体で実行します。 | 特定のニーズに応じてファーストおよびサード パーティの IoT Edge モジュールを検索し、デプロイすることによって市場に投入する時間を短縮します。 |
| 柔軟な課金オプションで収益化します。 <ul> <li> 無料で自分のライセンスを取得する (BYOL) </li> </ul> | 選択した課金モデルで購入します。 <ul> <li> 無料で自分のライセンスを取得する (BYOL) </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge モジュールとは何ですか。

Azure IoT Edge では、ビジネス ロジックをモジュール形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Microsoft サービス、サードパーティ サービス、独自ソリューション固有のコードのいずれかを含めることができます。 IoT Edge モジュールの詳細については、[Azure IoT Edge モジュールを理解する](../iot-edge/iot-edge-modules.md)を参照してください。

**コンテナー プランの種類と、IoT Edge モジュール プランの種類の違いは何ですか。**

IoT Edge モジュール プランの種類は、IoT Edge デバイスで実行されているコンテナーの特定の種類です。 IoT Edge のコンテキストで実行する既定の構成設定が付属され、IoT Edge ランタイムと統合されるIoT Edge モジュール SDK を必要に応じて使用します。

## <a name="publishing-your-iot-edge-module"></a>IoT Edge モジュールの発行

**正しいオンライン ストアの選択**

IoT Edge モジュールが Azure Marketplace でのみ発行され、AppSource は適用されません。 オンライン ストア間の相違点の詳細については、「[公開オプションを選定する](determine-your-listing-type.md)」を参照してください。

**課金オプション**

Marketplace は現在、IoT Edge モジュールの **無料** および **ライセンスを持ち込む (BYOL)** 課金オプションをサポートしています。

### <a name="publishing-options"></a>公開オプション

すべてのケースで IoT Edge モジュールでは、**Transact** 発行オプションを選択する必要があります。  発行オプションの詳細については、[発行オプションを選択する](determine-your-listing-type.md)を参照してください。  

## <a name="eligibility-criteria"></a>対象となる条件

Microsoft Azure Marketplace の契約とポリシーのすべてのご契約条件は、IoT Edge モジュールのプランに適用されます。  さらに、IoT Edge モジュールの前提条件と技術面の要件があります。  

### <a name="prerequisites"></a>前提条件

IoT Edge モジュールを Azure Marketplace で発行するには、次の前提条件を満たす必要があります。

- パートナー センターへのアクセス。 詳細については、「[商業マーケットプレース アカウントをパートナー センターに作成する](partner-center-portal/create-account.md)」を参照してください。
- Azure Container registry で IoT Edge モジュールをホストします。
- IoT Edge モジュールのメタデータを (非網羅的なリストのように) 用意してください：
    - タイトル
    - (HTML 形式) での説明
    - ロゴ イメージ (48 x 48 (オプション)、90 x 90 (オプション)、および 216 x 216 から 350 x 350 px までのサイズ (すべて PNG 形式))
    - 使用条件とプライバシー ポリシー
    - 既定のモジュールの構成 (ルート、ツインの必要なプロパティ、createOptions、環境変数)
    - ドキュメント
    - サポートの連絡先

### <a name="technical-requirements"></a>技術的な要件

Azure Marketplace で認定および発行されるためには、IoT Edge モジュールの主要な技術面の要件の詳細は、「[IoT Edge モジュールの技術アセットの準備](./partner-center-portal/create-iot-edge-module-asset.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- パートナー センターで [IoT Edge モジュール オファーを作成](./partner-center-portal/azure-iot-edge-module-creation.md)します。
