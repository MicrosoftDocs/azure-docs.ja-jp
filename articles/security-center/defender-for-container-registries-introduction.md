---
title: コンテナー レジストリ用 Azure Defender - 利点と機能
description: コンテナー レジストリ用 Azure Defender の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ab2ad15da9b1676924197d28e734f6baf59a02ef
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176639"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Azure Defender for container registries の概要

Azure Container Registry (ACR) は、Azure デプロイ用のコンテナー イメージを中央のレジストリに格納して管理する、プライベートなマネージド Docker レジストリ サービスです。 これは、オープンソースの Docker Registry 2.0 を基にしています。

サブスクリプション内のすべての Azure Resource Manager ベースのレジストリを保護するには、サブスクリプション レベルで **コンテナー レジストリ用 Azure Defender** を有効にします。 その後、Security Center では、レジストリにプッシュされたイメージ、レジストリにインポートされたイメージ、または過去 30 日以内にプルされたイメージがスキャンされます。 この機能では、イメージごとに課金されます。

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>コンテナー レジストリ用 Azure Defender の利点は何ですか?

Security Center では、サブスクリプション内の Azure Resource Manager ベースの ACR レジストリが識別され、レジストリのイメージに対する Azure ネイティブの脆弱性の評価と管理がシームレスに提供されます。

**コンテナー レジストリ用 Azure Defender** には、Azure Resource Manager ベースの Azure Container Registry レジストリ内のイメージをスキャンし、画像の脆弱性をより詳細に把握するための脆弱性スキャナーが含まれています。 統合されたスキャナーは、業界トップレベルの脆弱性スキャン ベンダーである Qualys を使用しています。

Qualys または Security Center によって問題が見つかった場合は、Security Center ダッシュボードで通知されます。 Security Center には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、および問題の修正方法に関するガイダンスが表示されます。 コンテナーの Security Center の推奨事項の詳細については、[推奨事項の参照リスト](recommendations-reference.md#recs-compute)を参照してください。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 Security Center には、報告された各脆弱性の詳細と、重要度の分類が表示されます。 また、各イメージで検出された特定の脆弱性を修復する方法についてのガイダンスも提供されます。

問題があるときにだけ通知することにより、Security Center では不要な情報アラートの可能性が減ります。


> [!TIP]
> Security Center のコンテナーのセキュリティ機能の詳細については、以下をご覧ください。
>
> - [Azure Security Center とコンテナーのセキュリティ](container-security.md)
> - [Azure Defender for Kubernetes の概要](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>イメージはどのような場合にスキャンされますか。

イメージ スキャンには、次の 3 つのトリガーがあります。

- **プッシュ時** - イメージがレジストリにプッシュされるたびに、Security Center はそのイメージを自動的にスキャンします。 イメージのスキャンをトリガーするには、イメージをリポジトリにプッシュしてください。

- **最近プルされたもの** - 新しい脆弱性は日々検出されるため、**コンテナー レジストリ用 Azure Defender** は、過去 30 日以内にプルされたすべてのイメージもスキャンします。 再スキャンに対する追加料金は発生しません。前述のように、イメージごとに 1 回の課金となります。

- **インポート時** - Azure Container Registry には、Docker Hub、Microsoft Container Registry、またはその他の Azure コンテナー レジストリからレジストリにイメージを取り込むためのインポート ツールが用意されています。 **コンテナー レジストリ用 Azure Defender** は、インポートした、サポートされているイメージをスキャンします。 詳細については、「[コンテナー レジストリにコンテナー イメージをインポートする](../container-registry/container-registry-import-images.md)」を参照してください。
 
通常、スキャンは 2 分以内に完了しますが、最大で 15 分かかる場合もあります。 結果は、Security Center の推奨事項として、次のように表示されます。

[![Azure Container Registry (ACR) でホストされるイメージで検出された脆弱性に関する Azure Security Center の推奨事項のサンプル](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Security Center では Azure Container Registry がどのように使用されていますか。

コンポーネントの概要図、および Security Center でレジストリを保護する利点を次に示します。

![Azure Security Center と Azure Container Registry (ACR) の概要](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure Container Registry のイメージ スキャンに関する FAQ

### <a name="how-does-security-center-scan-an-image"></a>Security Center ではどのような方法でイメージがスキャンされますか。
Security Center は、レジストリからイメージをプルし、Qualys スキャナーを使用してそれを分離されたサンドボックス内で実行します。 スキャナーにより、既知の脆弱性の一覧が抽出されます。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 Security Center では、問題があるときにのみ通知することにより、発生する可能性のある不必要な情報アラートを削減しています。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API 経由でスキャン結果を取得できますか。
はい。 結果は [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/) の下にあります。 また、Azure Resource Graph (ARG) を利用できます。これはすべてのリソースを対象とする Kusto のような API であり、1 つのクエリで特定のスキャンをフェッチできます。

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>どのようなレジストリの種類がスキャンされますか。 どのような種類が課金されますか。
コンテナー レジストリ用 Azure Defender によってサポートされているコンテナー レジストリの種類の一覧については、「[可用性](#availability)」をご覧ください。

サポートされていないレジストリを Azure サブスクリプションに接続した場合、Azure Defender はそれらをスキャンしないため、課金は発生しません。

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>脆弱性スキャナーの結果をカスタマイズすることはできますか。
はい。 組織のニーズとして、検出結果を修復するのではなく無視する必要がある場合は、必要に応じて検出結果を無効にできます。 無効化された検出結果は、セキュリティ スコアに影響を与えたり、不要なノイズを生成したりすることはありません。

[統合された脆弱性評価ツールからの結果を無効にするルールを作成する方法について確認してください](defender-for-container-registries-usage.md#disable-specific-findings-preview)。

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>レジストリに含まれていないイメージに関する脆弱性に対して、Security Center の警告が表示されるのはなぜですか。
レジストリにプッシュまたはプルされたすべてのイメージに対して、Security Center による脆弱性評価が提供されています。 イメージの中には、既にスキャンされたイメージのタグを再利用しているものもあります。 たとえば、ダイジェストにイメージを追加するたびに "Latest" (最新) タグを再割り当てする場合があります。 このような場合は、"古い" イメージは引き続きレジストリに存在し、そのダイジェストによってプルされる可能性があります。 イメージにセキュリティの調査結果があり、プルされた場合は、セキュリティの脆弱性が明らかになります。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [イメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)