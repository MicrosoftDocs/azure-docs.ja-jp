---
title: "Azure-SSIS 統合ランタイムの VNet への参加 | Microsoft Docs"
description: "Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: ddbba4b45c5c7cd8ee69390581cd78b820d19259
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---

# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
次の条件のいずれかが当てはまる場合、Azure-SSIS 統合ランタイム (IR) を Azure 仮想ネットワーク (VNet) に参加させる必要があります。 

- SSIS カタログ データベースのホストとなる SQL Server マネージ インスタンス (プライベート プレビュー) が VNet に属している。
- Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。

 Azure Data Factory バージョン 2 (プレビュー) では、Azure-SSIS 統合ランタイムをクラシック VNet に参加させることができます。 現時点で、Azure Resource Manager の VNet はまだサポートされていません。 ただし、次のセクションで示す方法を使って回避できます。 

SSIS パッケージがパブリック クラウドのデータ ストアだけにアクセスする場合は、VNet に Azure-SSIS IR を参加させる必要はありません。 SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている VNet に Azure-SSIS IR を参加させる必要があります。 SSIS カタログが VNet 内にない Azure SQL Database でホストされている場合は、適切なポートを開く必要があります。 SSIS カタログがクラシック VNet の Azure SQL マネージ インスタンスでホストされている場合は、同じクラシック VNet に、または Azure SQL マネージ インスタンスが存在する VNet との間にクラシック間接続のある別のクラシック VNet に、Azure-SSIS IR を参加させることができます。 以降のセクションではさらに詳しく説明します。  

## <a name="access-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする
SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、Azure-SSIS 統合ランタイムを、オンプレミスのネットワークに接続された VNet に参加させます。 注意すべき重要な点がいくつかあります。 

- オンプレミスのネットワークに接続された既存の VNet がない場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための[クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、その VNet からオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。
- Azure-SSIS 統合ランタイムと同じ場所にオンプレミス ネットワークに接続された既存のクラシック VNet がある場合は、Azure-SSIS 統合ランタイムをそれに参加させることができます。
- Azure-SSIS 統合ランタイムとは異なる場所にオンプレミス ネットワークに接続された既存のクラシック VNet がある場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための[クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、[クラシック間の VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 接続を構成します。
- オンプレミスのネットワークに接続された既存の Azure Resource Manager VNet がある場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための[クラシック VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) を作成します。 次に、[クラシックと Azure Resource Manager の間の VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 接続を構成します。

## <a name="domain-name-services-server"></a>ドメイン ネーム サービス サーバー 
Azure-SSIS 統合ランタイムが参加する VNet で独自のドメイン ネーム サービス (DNS) サーバーを使う必要がある場合は、このガイダンスに従い、VNet 内の Azure-SSIS 統合ランタイムのノードが Azure エンドポイントを解決できるようにします。

Azure-SSIS 統合ランタイムが参加する VNet にネットワーク セキュリティ グループ (NSG) を実装する必要がある場合は、次のポートを受信/送信トラフィックが通過できるようにします。

| ポート | 方向 | トランスポート プロトコル | 目的 | 受信元/送信先 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | 受信 | TCP | Azure サービスはこれらのポートを使って、VNet の Azure-SSIS 統合ランタイムのノードと通信します。 | インターネット | 
| 443 | 送信 | TCP | VNet の Azure-SSIS 統合ランタイムのノードはこのポートを使って、Azure Storage、Event Hub などの Azure サービスにアクセスします。 | INTERNET | 
| 1433<br/>11000 ～ 11999<br/>14000 ～ 14999  | 送信 | TCP | VNet の Azure-SSIS 統合ランタイムのノードはこれらのポートを使って、Azure SQL Database サーバーによってホストされている SSISDB にアクセスします (Azure SQL マネージ インスタンスによってホストされている SSISDB には該当しません)。 | インターネット | 

## <a name="script-to-configure-vnet"></a>VNet を構成するスクリプト 
[この記事](create-azure-ssis-integration-runtime.md)のガイド付き PowerShell スクリプトを使って、VNet の Azure-SSIS 統合ランタイムをプロビジョニングできます。 このスクリプトは、Azure-SSIS 統合ランタイムを VNet に参加させることができるように、VNet のアクセス許可と設定を自動的に構成します。  


## <a name="use-portal-to-configure-vnet"></a>ポータルを使って VNet を構成する
VNet を構成するには、スクリプトを実行するのが最も簡単な方法です。 その VNet を構成するためのアクセス権がない場合、自動構成は失敗します。その VNet の所有者は、次の手順で手動構成できます。

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Azure VNet のリソース ID を調べます。
 
1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[その他のサービス]** をクリックします。 **[仮想ネットワーク (クラシック)]** を選びます。
3. 一覧で目的の**仮想ネットワーク**を選びます。 
4. [仮想ネットワーク (クラシック)] ページで、**[プロパティ]** を選びます。 

    ![クラシック VNet のリソース ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. **[リソース ID]** のコピー ボタンをクリックして、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。
6. 左側のメニューの **[サブネット]** をクリックし、**[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。

    ![VNet で使用可能なアドレスの数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. **MicrosoftAzureBatch** を VNet の**従来の仮想マシン共同作成者**ロールに参加させます。 
    1. 左側メニューの [アクセス制御 (IAM)] をクリックし、ツール バーの **[追加]** をクリックします。
    
        ![アクセス制御 -> 追加](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. **[アクセス許可の追加]** ページで、**[ロール]** の **[従来の仮想マシン共同作成者]** を選びます。 **[選択]** ボックスに「**MicrosoftAzureBatch**」と入力し、検索結果の一覧から **MicrosoftAzureBatch** を選びます。 
    
        ![アクセス許可の追加 - 検索](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. [保存] をクリックして設定を保存し、ページを閉じます。
    
        ![アクセス設定の保存](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 共同作成者の一覧に **MicrosoftAzureBatch** があることを確認します。
    
        ![AzureBatch アクセスの確認](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. VNet が含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。
    1. Azure Portal で、左メニューの **[サブスクリプション]** をクリックします。 
    2. **サブスクリプション**を選択します。 
    3. 左側の **[リソース プロバイダー]** をクリックし、`Microsoft.Batch` が登録済みのプロバイダーであることを確認します。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    `Microsoft.Batch` が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 
         


## <a name="next-steps"></a>次のステップ
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL マネージ インスタンス (プライベート プレビュー) の使い方と、IR を VNet に参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 

