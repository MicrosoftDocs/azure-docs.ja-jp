---
title: 仮想ネットワークの分離とセキュリティの概要
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を Azure Machine Learning と共に使用して、ワークスペース リソースとコンピューティング環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 1c3d9b286a8262efa126ba9c661c50dd88e78b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573474"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>仮想ネットワークの分離とプライバシーの概要

この記事では、仮想ネットワーク (VNet) を使用して Azure Machine Learning のネットワーク通信をセキュリティで保護する方法について説明します。 この記事では、完全な仮想ネットワークを構成する方法を示すシナリオ例を使用します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートからなるシリーズのパート 1 です。 まずは、この概要に関する記事を読み、概念を理解しておくことを強くお勧めします。 

このシリーズの他の記事は次のとおりです。

**1.VNet の概要** > [2.ワークスペースをセキュリティで保護する](how-to-secure-workspace-vnet.md) > [3.トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md) > [4.推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md) > [5.Studio の機能を有効にする](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>前提条件

この記事では、次のトピックについて理解していることを前提としています。
+ [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)
+ [IP ネットワーク](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md)
+ [ネットワーク ファイアウォール](../firewall/overview.md)
## <a name="example-scenario"></a>シナリオ例

このセクションでは、プライベート IP アドレスを使用して Azure Machine Learning の通信をセキュリティで保護するために、一般的なネットワーク シナリオがどのように設定されるかについて説明します。

次の表では、VNet ありと VNet なしのネットワークの両方で、サービスがどのように Azure Machine Learning ネットワークの異なる部分にアクセスするかを比較しています。

| シナリオ | ワークスペース | 関連付けられているリソース | トレーニングのコンピューティング環境 | 推論のコンピューティング環境 |
|-|-|-|-|-|-|
|**仮想ネットワークなし**| パブリック IP | パブリック IP | パブリック IP | パブリック IP |
|**仮想ネットワーク内のリソースをセキュリティで保護**| プライベート IP (プライベート エンドポイント) | パブリック IP (サービス エンドポイント) <br> **または** <br> プライベート IP (プライベート エンドポイント) | プライベート IP | プライベート IP  | 

* **ワークスペース** - VNet からプライベート エンドポイントを作成して、ワークスペース上の Private Link に接続します。 プライベート エンドポイントは、複数のプライベート IP アドレスを使用してワークスペースを VNet に接続します。
* **関連するリソース** - サービス エンドポイントまたはプライベート エンドポイントを使用して、Azure storage、Azure Key Vault、Azure Container Service などのワークスペース リソースに接続します。
    * **サービス エンドポイント** は、仮想ネットワークの ID を Azure サービスに提供します。 ご利用の仮想ネットワークでサービス エンドポイントを有効にしたら、仮想ネットワーク規則を追加して、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。 サービス エンドポイントは、パブリック IP アドレスを使用します。
    * **プライベート エンドポイント** は、Azure Private Link を使用するサービスに安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。
* **トレーニングのコンピューティング アクセス** - プライベート IP アドレスを使用して、Azure Machine Learning コンピューティング インスタンスや Azure Machine Learning コンピューティング クラスターのようなトレーニングのコンピューティング ターゲットに安全に接続します。 
* **推論のコンピューティング アクセス** - プライベート IP アドレスを使用して、Azure Kubernetes Services (AKS) コンピューティング クラスターにアクセスします。


次の 5 つのセクションでは、前述のネットワーク シナリオをセキュリティで保護する方法について説明します。 ネットワークをセキュリティで保護するには、次のことを行う必要があります。

1. [**ワークスペースと関連するリソース**](#secure-the-workspace-and-associated-resources)をセキュリティで保護します。
1. [**トレーニング環境**](#secure-the-training-environment)をセキュリティで保護します。
1. [**推論環境**](#secure-the-inferencing-environment)をセキュリティで保護します。
1. 必要に応じて、[**Studio の機能を有効にします**](#optional-enable-studio-functionality)。
1. [**ファイアウォール設定**](#configure-firewall-settings)を構成します。
1. [DNS 名前解決](#custom-dns)を構成します。
## <a name="secure-the-workspace-and-associated-resources"></a>ワークスペースと関連するリソースをセキュリティで保護する

ワークスペースと関連するリソースをセキュリティで保護するには、次の手順に従います。 これらの手順により、サービスが仮想ネットワークで通信できるようになります。

1. [Private Link が有効なワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)を作成し、VNet とワークスペース間の通信を有効にします。
1. __サービス エンドポイント__ または __プライベート エンドポイント__ の "_どちらか_" を使用して、次のサービスを仮想ネットワークに追加します。 また、信頼された Microsoft サービスによるこれらのサービスへのアクセスを許可する必要があります。
    
    | サービス | エンドポイント情報 | 信頼できる情報を許可する |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [サービス エンドポイント](../key-vault/general/overview-vnet-service-endpoints.md)</br>[プライベート エンドポイント](../key-vault/general/private-link-service.md) | [信頼された Microsoft サービスを許可して、このファイアウォールをバイパスする](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure Storage アカウント__ | [サービス エンドポイント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)</br>[プライベート エンドポイント](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) | [信頼された Azure サービスにアクセスを許可する](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [サービス エンドポイント](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)</br>[プライベート エンドポイント](../container-registry/container-registry-private-link.md) | [信頼されたサービスを許可する](../container-registry/allow-access-trusted-services.md) |


![ワークスペースと関連するリソースが、VNet 内のサービス エンドポイントまたはプライベート エンドポイントを介して相互に通信する方法を示すアーキテクチャ図](./media/how-to-network-security-overview/secure-workspace-resources.png)

これらの手順を完了する方法の詳細については、[Azure Machine Learning ワークスペースのセキュリティ保護](how-to-secure-workspace-vnet.md)に関するページを参照してください。 

### <a name="limitations"></a>制限事項

仮想ネットワーク内のワークスペースと関連するリソースをセキュリティで保護するには、次の制限があります。
- Azure Government リージョンまたは Azure China 21Vianet リージョンでは、プライベート リンクで Azure Machine Learning ワークスペースを使用することはできません。
- すべてのリソースは同じ VNet の背後に配置する必要があります。 ただし、同じ VNet 内の複数のサブネットは許容されます。

## <a name="secure-the-training-environment"></a>トレーニング環境をセキュリティで保護する

このセクションでは、Azure Machine Learning でトレーニング環境をセキュリティで保護する方法について説明します。 また、Azure Machine Learning でトレーニング ジョブがどのように完了するかについても説明するので、ネットワーク構成がどのように連携するかを理解できます。

トレーニング環境をセキュリティで保護するには、次の手順に従います。

1. トレーニング ジョブを実行するために、Azure Machine Learning [コンピューティング インスタンスとコンピューター クラスターを仮想ネットワークに](how-to-secure-training-vnet.md#compute-instance)作成します。
1. [Azure Batch サービスからの受信通信を許可](how-to-secure-training-vnet.md#mlcports)して、Batch サービスがコンピューティング リソースにジョブを送信できるようにします。 

![マネージド コンピューティング クラスターとインスタンスをセキュリティで保護する方法を示すアーキテクチャ図](./media/how-to-network-security-overview/secure-training-environment.png)

これらの手順を完了する方法の詳細については、[トレーニング環境のセキュリティ保護](how-to-secure-training-vnet.md)に関するページを参照してください。 

### <a name="example-training-job-submission"></a>トレーニング ジョブの送信例 

このセクションでは、トレーニング ジョブを送信するために、Azure Machine Learning によってサービス間でどのように通信が安全に行われるかについて説明します。 これは、すべての構成が連携して通信をセキュリティで保護するしくみを示しています。

1. クライアントは、サービス エンドポイントまたはプライベート エンドポイントで保護されているストレージ アカウントに、トレーニング スクリプトとトレーニング データをアップロードします。

1. プライベート エンドポイントを使用して、クライアントから Azure Machine Learning ワークスペースにトレーニング ジョブが送信されます。

1. Azure Batch サービスはワークスペースからジョブを受信し、コンピューティング リソースを使用してプロビジョニングされたパブリック ロード バランサーを介して、トレーニング ジョブがコンピューティング環境に送信されます。 

1. コンピューティング リソースはジョブを受信し、トレーニングが開始されます。 コンピューティング リソースは、セキュリティで保護されたストレージ アカウントにアクセスしてトレーニング ファイルをダウンロードし、出力をアップロードします。

### <a name="limitations"></a>制限事項

- Azure コンピューティング インスタンスと Azure コンピューティング クラスターは、ワークスペースおよび関連するリソースと同じ VNet、リージョン、およびサブスクリプションにある必要があります。 

## <a name="secure-the-inferencing-environment"></a>推論環境をセキュリティで保護する

このセクションでは、推論環境をセキュリティで保護するために使用できるオプションについて説明します。 大規模な運用環境のデプロイには、Azure Kubernetes Services (AKS) クラスターを使用することをお勧めします。

仮想ネットワークでの AKS クラスターには、次の 2 つのオプションがあります。

- 既定の AKS クラスターを VNet にデプロイまたはアタッチします。
- プライベート AKS クラスターを VNet にアタッチします。

**既定の AKS クラスター** には、パブリック IP アドレスを持つコントロール プレーンがあります。 デプロイ時に既定の AKS クラスターを VNet に追加することもできますし、クラスターの作成後にそれをアタッチすることもできます。

**プライベート AKS クラスター** には、プライベート IP を介してのみアクセスできるコントロール プレーンがあります。 プライベート AKS クラスターは、クラスターの作成後にアタッチする必要があります。

既定のクラスターおよびプライベート クラスターを追加する方法の詳細については、[推論環境のセキュリティ保護](how-to-secure-inferencing-vnet.md)に関するページを参照してください。 

次のネットワーク図は、仮想ネットワークにアタッチされたプライベート AKS クラスターのある、セキュリティで保護された Azure Machine Learning ワークスペースを示しています。

![プライベート AKS クラスターを仮想ネットワークにアタッチする方法を示すアーキテクチャ図。 AKS コントロール プレーンは顧客 VNet の外側に配置されます](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>制限事項
- AKS クラスターは、ワークスペースおよび関連するリソースと同じ VNet に属している必要があります。 

## <a name="optional-enable-public-access"></a>省略可能:パブリック アクセスを有効にする

プライベート エンドポイントを使用して VNet の背後にあるワークスペースをセキュリティで保護しながら、パブリック インターネット経由のアクセスを許可することができます。 初期構成は、[ワークスペースと関連するリソースをセキュリティで保護する](#secure-the-workspace-and-associated-resources)場合と同じです。 

プライベート リンクを使用してワークスペースをセキュリティで保護した後、[パブリック アクセスを有効](how-to-configure-private-link.md#enable-public-access)にします。 その後、パブリック インターネットと VNet の両方からこのワークスペースにアクセスできます。

### <a name="limitations"></a>制限事項

- パブリック インターネット経由で Azure Machine Learning スタジオを使用する場合、デザイナーなどの一部の機能から自分のデータにアクセスできないことがあります。 この問題は、データが VNet の背後でセキュリティ保護されているサービスに格納されている場合に発生します。 たとえば、Azure Storage アカウントです。
## <a name="optional-enable-studio-functionality"></a>省略可能: Studio の機能を有効にする

[ワークスペースをセキュリティで保護する](#secure-the-workspace-and-associated-resources) > [トレーニング環境をセキュリティで保護する](#secure-the-training-environment) > [推論環境をセキュリティで保護する](#secure-the-inferencing-environment) > **Studio の機能を有効にする** > [ファイアウォール設定を構成する](#configure-firewall-settings)

ストレージが VNet 内にある場合は、まず追加の構成手順を実行して、[Studio](overview-what-is-machine-learning-studio.md) ですべての機能を有効にする必要があります。 既定では、次の機能が無効になっています。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* デザイナーでのモデルのデプロイ
* AutoML 実験の送信
* ラベル付けプロジェクトの開始

VNet 内部ですべての Studio 機能を有効にするには、「[Azure 仮想ネットワークで Azure Machine Learning Studio を使用する](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio)」を参照してください。 Studio は、サービス エンドポイントまたはプライベート エンドポイントを使用するストレージ アカウントをサポートしています。

### <a name="limitations"></a>制限事項

[ML によるデータのラベル付け](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling)は、仮想ネットワークの背後でセキュリティ保護された既定のストレージ アカウントをサポートしていません。 ML によるデータのラベル付けには、既定以外のストレージ アカウントを使用する必要があります。 既定以外のストレージ アカウントは、仮想ネットワークの背後でのセキュリティ保護が可能です。 

## <a name="configure-firewall-settings"></a>ファイアウォール設定を構成する

ファイアウォールを構成して、Azure Machine Learning ワークスペース リソースとパブリック インターネットへのアクセスを制御します。 Azure Firewall をお勧めしますが、他のファイアウォール製品を使用してネットワークを保護することもできます。 ファイアウォール経由の通信を許可する方法について不明な点がある場合は、使用しているファイアウォールのドキュメントを参照してください。

ファイアウォール設定の詳細については、[ファイアウォールの内側でのワークスペースの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="custom-dns"></a>[カスタム DNS]

仮想ネットワークにカスタム DNS ソリューションを使用する必要がある場合は、ワークスペースのホスト レコードを追加する必要があります。

必要なドメイン名と IP アドレスの詳細については、「[カスタム DNS サーバーでワークスペースを使用する方法](how-to-custom-dns.md)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事は、全 5 パートからなる仮想ネットワーク シリーズのパート 1 です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 2: 仮想ネットワークの概要](how-to-secure-workspace-vnet.md)
* [パート 3: トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [パート 4: 推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [パート 5: スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)

名前の解決については、[カスタム DNS](how-to-custom-dns.md) の使用に関する記事も参照してください。