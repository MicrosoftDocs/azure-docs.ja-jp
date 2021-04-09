---
title: Microsoft Azure での Cloud Foundry の概要
description: Microsoft Azure で OSS または Pivotal Cloud Foundry を実行する
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d3b13dffcf251f79a04182d32f23fd40e6ed6044
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562896"
---
# <a name="cloud-foundry-on-azure"></a>Azure での Cloud Foundry

Cloud Foundry は、さまざまな言語やフレームワークで開発された 12 ファクター アプリケーションをビルド、デプロイ、運用するためのオープンソースのサービスとしてのプラットフォーム (PaaS) です。 このドキュメントでは、Azure で Cloud Foundry を実行するためのオプションと、その使い方について説明します。

## <a name="cloud-foundry-offerings"></a>Cloud Foundry の種類

Azure で実行できる Cloud Foundry には、オープン ソースの Cloud Foundry (OSS CF) と Pivotal Cloud Foundry (PCF) という 2 つの形式があります。 OSS CF は完全に[オープン ソース](https://github.com/cloudfoundry)で作られた Cloud Foundry で、Cloud Foundry Foundation によって管理されています。 Pivotal Cloud Foundry は、Pivotal Software Inc. から配布されているエンタープライズ向けの Cloud Foundry です。ここでは両者の違いをいくつか紹介します。

### <a name="open-source-cloud-foundry"></a>オープン ソースの Cloud Foundry

OSS Cloud Foundry を Azure にデプロイするには、まず BOSH ディレクターをデプロイしてから Cloud Foundry をデプロイします ([GitHub の手順](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)を参照)。 OSS CF の使用方法の詳細については、Cloud Foundry Foundation が提供する[ドキュメント](https://docs.cloudfoundry.org/)を参照してください。

Microsoft では、次のコミュニティ チャネルを通じて OSS CF のサポートをベストエフォート型で提供しています。

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>[Cloud Foundry Slack](https://slack.cloudfoundry.org/) の bosh azure-cpi チャネル
- [cf-bosh メーリング リスト](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub 発行の [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) と [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Azure リソース (Cloud Foundry を実行する仮想マシンなど) のサポート レベルは、Azure サポート契約に基づきます。 ベスト エフォート型のコミュニティ サポートは、Cloud Foundry 固有のコンポーネントにのみ適用されます。

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry には、OSS 配布と同じコア プラットフォームに加え、独自の管理ツールとエンタープライズ サポートが含まれています。 PCF を Azure で実行するには、Pivotal からライセンスを取得する必要があります。 Azure Marketplace から入手できる PCF プランには、90 日間の試用版ライセンスが用意されています。

[Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/) (Cloud Foundry のデプロイと管理を容易にする Web アプリケーション) や [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/) (ユーザーとアプリケーションを管理するための Web アプリケーション) などのツールが含まれています。

上記に記載されている OSS CF のサポート チャネルのほか、PCF のライセンスを購入すると、Pivotal のサポートも利用できます。 Microsoft と Pivotal は共同でサポート ワークフローを確立しており、どちらのサポートに問い合わせをしても、問題に応じて適切にルーティングされるようになっています。

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry では、ステートレスなアプリケーション プロセスとステートフルなバックアップ サービスを明確に分離する、["twelve-factor app"](https://12factor.net/) (アプリ開発に必要な 12 要素) の方法論を推奨しています。 [Service Broker](https://docs.cloudfoundry.org/services/api.html) は、バックアップ サービスをアプリケーションにをプロビジョニングしてバインドするための一貫した方法を提供します。 [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) は、Azure Storage や Azure SQL などの主要な Azure サービスの一部を、このチャネルを通じて提供します。

Pivotal Cloud Foundry を使用している場合、Service Broker は Pivotal Network から[タイルとしても使用可能](https://docs.pivotal.io/azure-sb/installing.html)です。

## <a name="related-resources"></a>関連リソース

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services プラグイン

Cloud Foundry は、継続的インテグレーション (CI) や継続的デリバリー (CD) の使用を含めたアジャイル ソフトウェア開発に適しています。 Azure DevOps Services を使用してプロジェクトを管理しており、Cloud Foundry をターゲットとする CI/CD パイプラインを設定する場合は、[Azure DevOps Services の Cloud Foundry ビルド拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)を使用できます。 このプラグインを利用すると、環境が Azure かどうかにかかわらず、簡単に Cloud Foundry へのデプロイを構成して自動化することができます。

## <a name="next-steps"></a>次のステップ

- [Azure Marketplace から Pivotal Cloud Foundry をデプロイする](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [アプリを Azure の Cloud Foundry にデプロイする](./cloudfoundry-deploy-your-first-app.md)
