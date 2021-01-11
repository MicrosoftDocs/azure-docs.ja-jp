---
title: マシンとアプリケーションを保護する
description: このドキュメントでは、仮想マシン、コンピューター、Web アプリ、および App Service 環境を保護するのに役立つ、Security Center の推奨事項について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d79d1bf846cc023b86c3e33b17c91cce77ffe9ee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089874"
---
# <a name="protect-your-machines-and-applications"></a>マシンとアプリケーションを保護する
Azure Security Center によって潜在的なセキュリティの脆弱性が識別されると、リソースを堅牢化および保護するために必要な管理を構成するプロセスを説明する推奨事項が作成されます。

この記事では、Security Center のリソース セキュリティ セクションの **[計算とアプリ]** ページについて説明します。

このページに表示される可能性のある推奨事項の完全な一覧については、「[コンピューティングとアプリの推奨事項](recommendations-reference.md#recs-computeapp)」を参照してください。


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>計算とアプリ リソースのセキュリティを表示する

[![Security Center ダッシュボード](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

計算とアプリ リソースの状態を表示するには、Security Center の左側のウィンドウで **[計算とアプリ]** を選択します。 次のタブを使用できます。

* **概要**: すべての計算とアプリ リソースに関する推奨事項と、それらの現在のセキュリティ状態を一覧表示します 

* [**VM およびサーバー**](#vms-and-computers): VM およびコンピューターに関する推奨事項と、それぞれの現在のセキュリティ状態を一覧表示します

* [**VM スケール セット**](#vmscale-sets): スケール セットに関する推奨事項を一覧表示します 

* [**クラウド サービス**](#cloud-services): Security Center によって監視されている Web および worker ロールに関する推奨事項を一覧表示します

* [**App Service**](#app-services): App Service 環境に関する推奨事項と、それぞれの現在のセキュリティ状態を一覧表示します

* [**コンテナー**](#containers): コンテナーに関する推奨事項と、その構成のセキュリティ評価を一覧表示します

* **計算リソース**: Service Fabric クラスターやイベント ハブなど、計算リソースに関する推奨事項を一覧表示します

### <a name="whats-in-each-tab"></a>各タブの内容

各タブには複数のセクションがあり、各セクションでドリルダウンすることで、表示されている項目に関する追加の詳細を表示できます。

各タブには、監視対象の環境内の関連リソースに関する推奨事項も表示されます。 最初の列には推奨事項が一覧表示され、2 番目の列には影響を受けるリソースの合計数が表示され、3 番目の列には問題の重大度が表示されます。

各推奨事項にはいくつかのアクションが示されており、選択して実行できます。 たとえば、 **[システムの更新プログラムがありません]** を選択すると、パッチが適用されていない VM とコンピューター、および不足している更新プログラムの重大度が一覧表示されます。

> [!NOTE]
> セキュリティに関する推奨事項は、 **[推奨事項]** ページにあるものと同じですが、ここでは選択した特定のリソースの種類に合わせてフィルタリングされています。 推奨事項の解決方法の詳細については、[Azure Security Center でのセキュリティに関する推奨事項の実装](security-center-recommendations.md)に関するページを参照してください。
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VM およびサーバー
[VM およびコンピューター] セクションには、VM とコンピューターに関するすべてのセキュリティ推奨事項の概要が表示されます。 4 種類のマシンが含まれます。

![Azure 以外のコンピューター](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Azure 以外のコンピューター。

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![Azure クラシック VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure クラシック VM。

![ワークスペースから確認できる VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 表示されているサブスクリプションに属しているワークスペースからのみ確認できる VM。 たとえば、このサブスクリプションのワークスペースの管理下にある他のサブスクリプションの VM や、Operations Manager ダイレクト エージェントがインストールされた VM のうちリソース ID のないものが該当します。

各推奨事項の下に表示されるアイコンにより、注意が必要な VM とコンピューター、その推奨事項の種類をすばやく把握できます。 **リソースの種類**や**重大度**によって一覧を検索するフィルターを使用することもできます。

各 VM のセキュリティの推奨事項をドリルダウンするには、[VM] をクリックします。
ここには VM またはコンピューターのセキュリティに関する詳細が表示されます。 一番下には、推奨されるアクションと、各問題の重大度が表示されます。

[![クラウド サービス](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>仮想マシン スケール セット
Security Center では、スケール セットがあるかどうかの検出を自動的に行い、それらに Log Analytics エージェントをインストールすることを推奨しています。

Log Analytics エージェントをインストールするには: 

1. 推奨事項 **[仮想マシン スケール セットに監視エージェントをインストールする]** を選択します。 監視されていないスケール セットの一覧が表示されます。

1. 異常なスケール セットを選択します。 既存の挿入済みのワークスペースを使用して監視エージェントをインストールするか、または新しいものを作成するという指示に従います。 ワークスペースの[価格レベル](security-center-pricing.md)が設定されていない場合は設定します。

   ![MMS のインストール](./media/security-center-virtual-machine-recommendations/install-mms.png)

新しいスケール セットを設定して自動的に Log Analytics エージェントをインストールするには:
1. Azure Policy に移動して、 **[定義]** をクリックします。

1. **Windows 仮想マシン スケール セット用の Log Analytics エージェントのデプロイ** ポリシーを検索してクリックします。

1. **[割り当て]** をクリックします。

1. **[スコープ]** と **[Log Analytics ワークスペース]** を設定して **[割り当て]** をクリックします。

Log Analytics エージェントをインストールするように既存のスケール セットをすべて設定する場合は、Azure Policy 上で **[修復]** に移動し、既存のポリシーを既存のスケール セットに適用します。





### <a name="cloud-services"></a><a name="cloud-services"></a>クラウド サービス
クラウド サービスについては、オペレーティング システムのバージョンが最新でないときに、推奨事項が作成されます。

![クラウド サービス](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

推奨事項があるシナリオでは、推奨事項の手順に従ってオペレーティング システムを更新してください。 更新プログラムが利用できる場合は、アラートが表示されます (問題の重大度により、赤またはオレンジになります)。 この推奨事項に関する完全な説明を確認する場合は、 **[説明]** 列の **[OS バージョンの更新]** をクリックしてください。






### <a name="app-services"></a><a name="app-services"></a>App Service
App Service 情報を表示するには、Security Center の Standard 価格レベルを使用し、サブスクリプションで App Service を有効にする必要があります。 この機能を有効にする方法については、「[Azure Security Center で App Service を保護する](security-center-app-services.md)」を参照してください。

**[App Service]** には、App Service 環境の一覧と、Security Center によって実行された評価に基づく正常性についての概要が表示されます。

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

次の 3 種類のアプリケーション サービスが表示されます。

![App Services 環境](./media/security-center-virtual-machine-recommendations/ase.png) App Services 環境

![Web アプリケーション](./media/security-center-virtual-machine-recommendations/web-app.png) Web アプリケーション

![関数アプリケーション](./media/security-center-virtual-machine-recommendations/function-app.png) 関数アプリケーション

Web アプリケーションを選択すると、次の 3 つのタブがある概要ビューが開きます。

   - **推奨事項**: Security Center によって実行され、失敗した評価に基づいて表示されます。
   - **評価に合格しました**: Security Center によって実行され、合格した評価の一覧。
   - **利用できない評価**: エラーのため、または推奨事項が特定のアプリ サービスに関連していないために実行に失敗した評価の一覧

   **[推奨事項]** には、選択した Web アプリケーションの推奨事項と各推奨事項の重大度の一覧が表示されます。

   ![App Services に関する推奨事項](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

推奨事項を選択すると、推奨事項の説明、正常でないリソース、正常なリソース、およびスキャンされていないリソースの一覧が表示されます。

   - **[評価に合格しました]** 列には、合格した評価の一覧が表示されます。 これらの評価の重大度は常に緑色です。

   - 一覧から合格した評価を選択すると、評価の説明、正常でないリソース、正常なリソース、およびスキャンされていないリソースの一覧が表示されます。 正常でないリソース用のタブがありますが、評価に合格しているため、その一覧は常に空です。





### <a name="containers"></a><a name="containers"></a>コンテナー

環境に応じて **[コンテナー]** タブを開くと、次の 3 種類のリソースのいずれかが表示されます。

![コンテナー ホスト](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) コンテナーホスト - Docker を実行している VM 

![Kubernetes サービス](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes Service (AKS) クラスター。 [Security Center の AKS バンドルについての詳細情報](azure-kubernetes-service-integration.md)

![コンテナー レジストリ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry (ACR) のレジストリ。 [Security Center の ACR バンドルについての詳細情報](azure-container-registry-integration.md)

コンテナーのセキュリティ機能を使用する方法については、「[コンテナーのセキュリティを監視する](monitor-container-security.md)」を参照してください。



[![[コンテナー] タブ](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

リスト内の特定のリソースに関する推奨事項を表示するには、そのリソースをクリックします。

#### <a name="visibility-into-container-registries"></a>コンテナー レジストリの可視性

たとえば、上の画面に示されている一覧から、asc-demo ACR レジストリをクリックすると、次の詳細ページが表示されます。

[![特定の ACR レジストリに関する推奨事項](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>IaaS Linux マシンでホストされているコンテナーの可視性

Docker を実行している VM のいずれかをクリックすると、詳細ページに、Docker のバージョンやホストで実行されているイメージの数など、コンピューターのコンテナーに関連する情報が表示されます。

![Docker を実行している VM の推奨事項](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Docker の CIS ベンチマークに基づくセキュリティに関する推奨事項

Security Center は、Docker 構成をスキャンし、評価されたすべての失敗したルールの一覧を提供することによって、構成の誤りに対する可視性を提供します。 これらの問題を迅速に解決して時間を節約するのに役立つガイドラインが Security Center から提供されます。 Security Center は、Docker 構成を継続的に評価し、それらの最新の状態をユーザーに提供します。

![[コンテナー] タブ](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>次のステップ
その他の Azure リソースの種類に適用される推奨事項の詳細については、次の記事をご覧ください。

* [Azure Security Center のセキュリティに関する詳細な参照リスト](recommendations-reference.md)
* [Azure Security Center での ID とアクセスの監視](security-center-identity-access.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)