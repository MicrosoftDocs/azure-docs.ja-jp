---
title: Azure ExpressRoute を Oracle Cloud Infrastructure と接続する | Microsoft Docs
description: Azure ExpressRoute を Oracle Cloud Infrastructure (OCI) FastConnect と接続して、クロスクラウド Oracle アプリケーション ソリューションを使用できるようにします
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707631"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Azure と Oracle Cloud Infrastructure 間の直接相互接続をセットアップする  

[統合されたマルチクラウド エクスペリエンス](oracle-oci-overview.md) (プレビュー) を作成するために、Microsoft と Oracle では、[ExpressRoute](../../../expressroute/expressroute-introduction.md) と [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) を介して　Azure と Oracle Cloud Infrastructure (OCI) 間の直接相互接続を提供しています。 ExpressRoute と FastConnect の相互接続により、お客様は、短い待機時間、高いスループット、2 つのクラウド間の専用直接接続を体験することができます。

> [!IMPORTANT]
> Microsoft Azure と OCI 間の接続はプレビュー段階です。 Azure と OCI の間で低待機時間の接続を有効にするには、この機能について Azure サブスクリプションと OCI テナントを最初にホワイトリストに登録する必要があります。

次の図は、相互接続の概要を示しています。

![クロスクラウド ネットワーク接続](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>前提条件

* Azure と OCI 間の接続を確立するには、アクティブな Azure サブスクリプションとアクティブな OCI テナントが必要です。

* 接続は、Azure ExpressRoute のピアリング場所が OCI FastConnect のピアリング場所に近接しているか、同じ場所にある場合にのみ可能です。 「[プレビューの制限事項](oracle-oci-overview.md#preview-limitations)」を参照してください。

* このプレビュー機能について、Azure サブスクリプションをホワイトリストに登録する必要があります。 サブスクリプションでこの機能を有効にするには、Microsoft の担当者にお問い合わせください。

* この機能について、OCI テナントをホワイトリストに登録する必要があります。 詳細については、Oracle の担当者にお問い合わせください。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>ExpressRoute と FastConnect 間の直接接続を構成する

1. お使いの Azure サブスクリプションでリソース グループの下に、標準 ExpressRoute 回路を作成します。 
    * ExpressRoute の作成時に、サービス プロバイダーとして **Oracle Cloud FastConnect** を選択します。 ExpressRoute 回路を作成するには、[手順の説明](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)を参照してください。
    * Azure ExpressRoute 回路では細かい帯域幅のオプションが提供されていますが、FastConnect では 1、2、5、または 10 Gbps がサポートされます。 したがって、ExpressRoute で、これらの一致する帯域幅のオプションの中からいずれかを選択することをお勧めします。

    ![ExpressRoute 回線を作成する](media/configure-azure-oci-networking/exr-create-new.png)
1. ExpressRoute の**サービス キー**を書き留めてください。 FastConnect 回路を構成しているときに、このキーを指定する必要があります。

    ![ExpressRoute のサービス キー](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > ExpressRoute 回路がプロビジョニングされるとすぐに ( **[プロバイダーの状態]** が **[未プロビジョニング]** であっても)、ExpressRoute の料金を請求されます。

1. お使いの Azure 仮想ネットワークまたは OCI 仮想クラウド ネットワークの IP アドレス スペースと重複しない、それぞれ /30 の 2 つのプライベート IP アドレス空間を見つけます。 最初の IP アドレス空間をプライマリ アドレス空間と呼び、2 番目の IP アドレス空間をセカンダリ アドレス空間と呼びます。 これらのアドレスを書き留めておいてください。FastConnect 回路を構成するときに必要になります。
1. 動的ルーティング ゲートウェイ (DRG) を作成します。 これは、FastConnect 回路を作成するときに必要になります。 詳細については、[動的ルーティング ゲートウェイ](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)のドキュメントをご覧ください。
1. Oracle テナントに FastConnect 回路を作成します。 詳細については、[Oracle のドキュメント](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)をご覧ください。
  
    * FastConnect の構成で、**Microsoft Azure: ExpressRoute** をプロバイダーとして選択します。
    * 前の手順でプロビジョニングした動的ルーティング ゲートウェイを選択します。
    * プロビジョニングする帯域幅を選択します。 最適なパフォーマンスのためには、この帯域幅は、ExpressRoute 回路を作成するときに選択した帯域幅と一致している必要があります。
    * **[Provider Service Key]\(プロバイダーのサービス キー\)** に、ExpressRoute のサービス キーを貼り付けます。
    * **[Primary BGP IP Address]\(プライマリ BGP IP アドレス\)** に、前の手順で見つけた最初の /30 プライベート IP アドレス空間を使用し、 **[Secondary BGP IP Address]\(セカンダリ BGP IP アドレス\)** に、2 番目の /30 プライベート IP アドレス空間を使用します。
        * Oracle BGP IP アドレス (プライマリとセカンダリ) の 2 つの範囲のうちの 1 番目に使用可能なアドレスと 2 番目のアドレスを (FastConnect の観点から) 顧客の BGP IP アドレスに割り当てます。 1 番目に使用可能な IP アドレスは、/30 アドレス空間内の 2 番目の IP アドレスです (最初の IP アドレスは、Microsoft によって予約済みです)。
    * **Create** をクリックしてください。
1. ルート テーブルを使用して、動的ルーティング ゲートウェイを経由する Oracle テナントでの FastConnect と仮想クラウド ネットワークのリンクを完了します。
1. Azure に移動し、ExpressRoute 回線の **[プロバイダーの状態]** が **[プロビジョニング済み]** に変わっており、 **[Azure プライベート]** の種類のピアリングがプロビジョニングされていることを確認してください。 これは、次の手順の前提条件です。

    ![ExpressRoute のプロバイダーの状態](media/configure-azure-oci-networking/exr-provider-status.png)
1. **Azure プライベート** ピアリングをクリックします。 FastConnect 回線をセットアップするときに入力した情報に基づいてピアリングの詳細が自動的に構成されていることが分かります。

    ![プライベート ピアリングの設定](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>仮想ネットワークを ExpressRoute に接続する

1. まだ作成していない場合は、仮想ネットワークと仮想ネットワーク ゲートウェイを作成します。 詳細については、[手順の説明](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)をご覧ください。
1. [Terraform スクリプト](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)を実行するか PowerShell コマンドを実行して [ExpressRoute FastPath を構成](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)し、仮想ネットワーク ゲートウェイと ExpressRoute 回線の間の接続をセットアップします。

ネットワークの構成が完了したら、Azure portal の ExpressRoute プライベート ピアリング ブレードで **[ARP レコードを取得する]** および **[ルート テーブルを取得する]** をクリックして、構成の有効性を検証できます。

## <a name="automation"></a>Automation

Microsoft では、ネットワーク相互接続の自動デプロイを有効にするために Terraform スクリプトを作成しました。 Terraform スクリプトには Azure サブスクリプションでの適切なアクセス許可が必要なため、実行する前に Azure で認証する必要があります。 認証は、[Azure Active Directory サービス プリンシパル](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を使用するか、Azure CLI を使用して実行できます。 詳細については、[Terraform のドキュメント](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)を参照してください。

相互接続をデプロイするための Terraform スクリプトと関連ドキュメントは、こちらの [GitHub リポジトリ](https://aka.ms/azureociinterconnecttf)にあります。

## <a name="monitoring"></a>監視

両方のクラウドにエージェントをインストールすると、Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) を活用して、エンドツーエンド ネットワークのパフォーマンスを監視できます。 NPM は、ネットワークの問題をすぐに特定して、それらを排除するのに役立ちます。

## <a name="delete-the-interconnect-link"></a>相互接続リンクを削除する

相互接続を削除するには、指定された特定の順序で以下の手順を実行する必要があります。 そうしないと、ExpressRoute 回線が "失敗した状態" になります。

1. ExpressRoute 接続を削除します。 接続のページで **[削除]** アイコンをクリックして接続を削除します。 詳細については、[ExpressRoute のドキュメント](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)を参照してください。
1. Oracle Cloud Console から Oracle FastConnect を削除します。
1. Oracle FastConnect 回線が削除されたら、Azure ExpressRoute 回線を削除できます。

この時点で、削除とプロビジョニング解除のプロセスが完了します。

## <a name="next-steps"></a>次の手順

* OCI と Azure 間のクロスクラウド接続の詳細については、[Oracle のドキュメント](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)をご覧ください。
* [Terraform スクリプト](https://aka.ms/azureociinterconnecttf)を使って、Azure で対象の Oracle アプリケーションのインフラストラクチャをデプロイし、ネットワークの相互接続を構成します。 