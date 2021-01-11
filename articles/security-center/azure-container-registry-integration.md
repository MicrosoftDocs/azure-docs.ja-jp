---
title: Azure Security Center と Azure Container Registry
description: Azure Security Center を使用したコンテナー レジストリのスキャンについて説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 718f9a29b70dab34269c959ccd62452e56a32d72
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056603"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Security Center による Azure Container Registry のイメージ スキャン

Azure Container Registry (ACR) は、Azure デプロイ用のコンテナー イメージを中央のレジストリに格納して管理する、プライベートなマネージド Docker レジストリ サービスです。 これは、オープンソースの Docker Registry 2.0 を基にしています。

Azure Security Center の 標準レベルを使用している場合には、Container Registries のバンドルを追加できます。 このオプションの機能を使用すると、Azure Resource Manager ベースのレジストリ内のイメージの脆弱性をより詳しく把握できます。 サブスクリプション レベルでバンドルを有効または無効にし、サブスクリプション内のすべてのレジストリをカバーします。 この機能は、[価格のページ](security-center-pricing.md)に示されているように、イメージごとに課金されます。 Container Registries のバンドルを有効にすると、Security Center は、レジストリにプッシュされたイメージをスキャンできるようになります。 

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開|
|価格:|Standard レベル|
|サポートされているレジストリとイメージ:|![はい](./media/icons/yes-icon.png) パブリック インターネットからアクセスでき、シェル アクセスを提供する、Linux でホストされている ACR レジストリ。<br>![いいえ](./media/icons/no-icon.png) Windows でホストされている ACR レジストリ。<br>![いいえ](./media/icons/no-icon.png) 'プライベート' レジストリ - パブリック インターネットからこのレジストリにアクセスできることが Security Center によって求められます。 現在、ファイアウォール、サービス エンドポイント、または Azure Private Link などのプライベート エンドポイントによってアクセスが制限されたレジストリへの接続、このようなレジストリのスキャンは、Security Center ではサポートされていません。<br>![いいえ](./media/icons/no-icon.png) [Docker スクラッチ](https://hub.docker.com/_/scratch/) イメージのようなスーパー ミニマリスト イメージ、またはアプリケーションとそのランタイム依存関係のみが含まれ、パッケージ マネージャー、シェル、OS は含まれない "ディストリビューションレス" イメージ。|
|必要なロールとアクセス許可:|**セキュリティ閲覧者**と [Azure Container Registry 閲覧者ロール](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||




## <a name="when-are-images-scanned"></a>イメージはどのような場合にスキャンされますか。

イメージがレジストリにプッシュされるたびに、Security Center はそのイメージを自動的にスキャンします。 イメージのスキャンをトリガーするには、イメージをリポジトリにプッシュしてください。

スキャンが完了すると (通常は約 2 分後ですが、最大で 15 分かかることもあります)、結果が次のような Security Center の推奨事項で表示されます。

[![Azure Container Registry (ACR) でホストされるイメージで検出された脆弱性に関する Azure Security Center の推奨事項のサンプル](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>統合の利点

サブスクリプション内の Azure Resource Manager ベースの ACR レジストリが識別されて、次のものがシームレスに提供されます。

* プッシュされたすべての Linux イメージに対する **Azure ネイティブの脆弱性スキャン**。 Security Center では、業界をリードする脆弱性スキャン ベンダーである Qualys のスキャナーを使ってイメージのスキャンを行います。 このネイティブ ソリューションは、既定でシームレスに統合されています。

* 既知の脆弱性がある Linux イメージの**セキュリティに関する推奨事項**。 Security Center には、報告された各脆弱性の詳細と、重要度の分類が表示されます。 また、レジストリにプッシュされた各イメージで検出された特定の脆弱性を修復する方法についてのガイダンスも提供されます。

![Azure Security Center と Azure Container Registry (ACR) の概要](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry のイメージ スキャンに関する FAQ

### <a name="how-does-security-center-scan-an-image"></a>Security Center ではどのような方法でイメージがスキャンされますか。
イメージがレジストリから抽出されます。 その後、隔離されたサンドボックスの中で、既知の脆弱性の一覧を抽出する Qualys スキャナーによって実行されます。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 問題があるときにだけ通知することにより、Security Center では不要な情報アラートの可能性が減ります。

### <a name="how-often-does-security-center-scan-my-images"></a>Security Center ではどのくらいの頻度でイメージがスキャンされますか。
イメージ スキャンはプッシュのたびにトリガーされます。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API 経由でスキャン結果を取得できますか。
はい。 結果は [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/) の下にあります。 また、Azure Resource Graph (ARG) を利用できます。これはすべてのリソースを対象とする Kusto のような API であり、1 つのクエリで特定のスキャンをフェッチできます。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>どのようなレジストリの種類がスキャンされますか。 どのような種類が課金されますか。
[可用性セクション](#availability)には、コンテナー レジストリ バンドルによってサポートされているコンテナー レジストリの種類が一覧表示されています。 

サポートされていないレジストリが Azure サブスクリプションに接続されている場合、これらはスキャンされず、課金対象にもなりません。


## <a name="next-steps"></a>次のステップ

Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。

* [Azure Security Center とコンテナーのセキュリティ](container-security.md)

* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)

* [仮想マシンの保護](security-center-virtual-machine-protection.md) - Security Center の推奨事項について説明します。
