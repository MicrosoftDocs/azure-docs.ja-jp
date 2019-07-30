---
title: Azure IoT Edge のモジュール
description: Azure Marketplace でアプリとサービスのパブリッシャー用の IoT Edge モジュールが提供されています。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: pabutler
ms.openlocfilehash: 3010b63c7c4c575d915789c19b60710194c79196
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874666"
---
# <a name="iot-edge-modules"></a>IoT Edge のモジュール

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) プラットフォームは Azure クラウドによってサポートされます。  このプラットフォームでは、ユーザーは IoT  デバイスで直接実行するクラウド ワークロードをデプロイすることができます。  IoT Edge モジュールでは、オフラインのワークロードを実行でき、ローカルでデータを分析することができます。 このプランでは、帯域幅を保存したり、ローカルおよび機密データを保護することができ、低待機応答時間を提供します。  このような構築済みのワークロードを活用するためにいくつかのオプションがあります。 今までは、ごく少数の Microsoft のファーストパーティ  ソリューションが使用可能でした。  カスタマイズ IoT ソリューションの構築に時間とリソースを使う必要がありました。

[Azure Marketplace で IoT Edge モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)が提供されていることにより、1 か所でパブリッシャーが IoT のユーザーにソリューションを公開したり販売したりすることができるようになりました。 IoT 開発者は、適当な機能を検索して、それを購入することによって、自分のソリューションの開発を加速することができます。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace での IoT Edge モジュールの主なメリット:

| **パブリッシャーが対象**    | **お客様 (IoT 開発者) が対象**  |
| :------------------- | :-------------------|
| IoT Edge ソリューションをビルドおよびデプロイしようとしている何百万もの開発者にまで届きます。  | 安全でテストされたコンポーネントを使用するという自信を持って、IoT Edge ソリューションを作成します。 |
| 一度発行して、コンテナーをサポートする任意の IoT Edge ハードウェア全体で実行します。 | 特定のニーズに応じてファーストおよびサード パーティの IoT Edge モジュールを検索し、デプロイすることによって市場に投入する時間を短縮します。 |
| 柔軟な課金オプションで収益化します。 <ul> <li> 無料で自分のライセンスを取得する (BYOL) </li> </ul> | 選択した課金モデルで購入します。 <ul> <li> 無料で自分のライセンスを取得する (BYOL) </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge モジュールとは何ですか。

Azure IoT Edge では、ビジネス ロジックをモジュール形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Microsoft サービス、サードパーティー サービスまたは、独自ソリューション固有のコードを含めることができます。 IoT Edge モジュールの詳細については、[Azure IoT Edge モジュールを理解する](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)を参照してください。

**コンテナー プランの種類と、IoT Edge モジュール プランの種類の違いは何ですか。**

IoT Edge モジュール プランの種類は、IoT Edge デバイスで実行されているコンテナーの特定の種類です。 IoT Edge のコンテキストで実行する既定の構成設定が付属され、IoT Edge ランタイムと統合されるIoT Edge モジュール SDK を必要に応じて使用します。

## <a name="publishing-your-iot-edge-module"></a>IoT Edge モジュールの発行

**右側のネット ショップの選択**

IoT Edge モジュールが Azure Marketplace でのみ発行され、AppSource は適用されません。  ネット ショップ間の相違点と対象ユーザーの詳細については、[ソリューションの発行オプションの決定](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)を参照してください。
 
**課金オプション**

Marketplace は現在、IoT Edge モジュールの**無料**および**ライセンスを持ち込む (BYOL)** 課金オプションをサポートしています。
 
**発行オプション**

すべてのケースで IoT Edge モジュールでは、**Transact** 発行オプションを選択する必要があります。  発行オプションの詳細については、[発行オプションを選択する](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)を参照してください。  

## <a name="eligibility-criteria"></a>対象となる条件

Microsoft Azure Marketplace の契約とポリシーのすべてのご契約条件は、IoT Edge モジュールのプランに適用されます。  さらに、IoT Edge モジュールの前提条件と技術面の要件があります。  

**前提条件**

IoT Edge モジュールを Azure Marketplace で発行するには、次の前提条件を満たす必要があります。

- Cloud パートナー ポータル (CPP) へのアクセス 詳細については、 [Azure Marketplace と AppSource の発行ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)を参照してください。
- Azure Container registry で IoT Edge モジュールをホストします。 
- IoT Edge モジュールのメタデータを (非網羅的なリストのように) 用意してください： 
    - タイトル
    - (HTML 形式) での説明
    - ロゴのイメージ (40x40px、90x90px、115x115px 255x115px を含むPNG 形式および固定イメージのサイズ)
    - 使用条件とプライバシー ポリシー
    - 既定のモジュールの構成 (ルート、ツインの必要なプロパティ、createOptions、環境変数)
    - ドキュメント
    - サポートの連絡先

**技術的な要件**

Azure Marketplace で認定および発行されるためには、IoT Edge モジュールの主要な技術面の要件の詳細は、「[IoT Edge モジュールの技術アセットの準備](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)」を参照してください。  

## <a name="documentation-and-resources"></a>ドキュメントおよびリソース

[IoT Edge モジュール プランの作成](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – クラウド発行ポータルで新しい IoT Edge モジュールを発行するための手順を説明します。

## <a name="next-steps"></a>次の手順

まだ行っていない場合は、

- [Microsoft Partner Network に登録する](https://partner.microsoft.com/membership)
- [Microsoft アカウント](https://account.microsoft.com/account/)を作成する (Azure Marketplace 取引オファーに必要です。他のユーザーには推奨)。
- [Marketplace 登録フォーム](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)を送信する 詳細については、[パートナー センター アカウントの作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)方法を参照してください。

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [Cloud パートナー ポータルにサインイン](https://cloudpartner.azure.com/)して、オファーを作成または完成する。
- IoT Edge オファーを発行する方法の詳細については、「[IoT Edge モジュール オファー発行の概要](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts)」を参照してください。
