---
title: Azure Synapse ワークスペースからセキュリティで保護されたストレージ アカウントに接続する
description: この記事では、Azure Synapse ワークスペースからセキュリティで保護されたストレージ アカウントに接続する方法を説明します
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674316"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Synapse ワークスペースからセキュリティで保護された Azure ストレージ アカウントに接続する

この記事では、Azure Synapse ワークスペースからセキュリティで保護された Azure ストレージ アカウントに接続する方法を説明します。 ワークスペースを作成するときに、Azure ストレージ アカウントを Synapse ワークスペースにリンクすることができます。 ワークスペースを作成した後で、さらに多くのストレージ アカウントをリンクすることができます。


## <a name="secured-azure-storage-accounts"></a>セキュリティで保護された Azure ストレージ アカウント
Azure Storage の階層型セキュリティ モデルを使用すると、ストレージ アカウントへのアクセスをセキュリティで保護し、制御できます。 IP ファイアウォール規則を構成して、選択したパブリック IP アドレス範囲からのトラフィックに、ストレージ アカウントへのアクセスを許可することができます。 また、ネットワーク規則を構成して、選択した仮想ネットワークからのトラフィックに、ストレージ アカウントへのアクセスを許可することができます。 選択した IP アドレス範囲からのアクセスを許可する IP ファイアウォール規則と、同じストレージ アカウント上の選択した仮想ネットワークからのアクセスを許可するネットワーク規則を組み合わせることができます。 これらの規則は、ストレージ アカウントのパブリック エンドポイントに適用されます。 ワークスペースで作成されたマネージド プライベート エンドポイントからストレージ アカウントへのトラフィックを許可するアクセス規則は必要ありません。 ストレージ ファイアウォール規則は、既存のストレージ アカウントに、または新しいストレージ アカウントの作成時にそれらに適用できます。 ストレージ アカウントのセキュリティ保護の詳細については、[こちら](../../storage/common/storage-network-security.md)を参照してください。

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse ワークスペースと仮想ネットワーク
Synapse ワークスペースを作成するときに、マネージド仮想ネットワークとそれの関連付けを有効にすることができます。 ワークスペースの作成時にマネージド仮想ネットワークを有効にしない場合、そのワークスペースは、マネージド仮想ネットワークが関連付けられていない他の Synapse ワークスペースと共に、共有仮想ネットワークに配置されます。 ワークスペースの作成時にマネージド仮想ネットワークを有効にした場合、ワークスペースは、Azure Synapse によって管理される専用の仮想ネットワークに関連付けられます。 これらの仮想ネットワークは、お客様のサブスクリプションには作成されません。 このため、上で説明したネットワーク規則を使用して、これらの仮想ネットワークからのトラフィックに、セキュリティで保護されたストレージ アカウントへのアクセスを許可することはできません。  

## <a name="access-a-secured-storage-account"></a>セキュリティで保護されたストレージ アカウントにアクセスする
Synapse は、ネットワーク規則に含めることのできないネットワークから動作します。 ワークスペースからセキュリティで保護されたストレージ アカウントへのアクセスを有効にするには、以下のことを行う必要があります。

* マネージド仮想ネットワークが関連付けられた Azure Synapse ワークスペースを作成し、そこからセキュリティで保護されたストレージ アカウントへのマネージド プライベート エンドポイントを作成します
* Azure Synapse ワークスペースに、セキュリティで保護されたストレージ アカウントへの、信頼された Azure サービスとしてのアクセスを許可します。 その後、Azure Synapse は、信頼されたサービスとして、強力な認証を使用してストレージ アカウントに安全に接続します。   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>マネージド仮想ネットワークで Synapse ワークスペースを作成し、ストレージ アカウントへのマネージド プライベート エンドポイントを作成します
[こちらの手順](./synapse-workspace-managed-vnet.md)に従って、マネージド仮想ネットワークが関連付けられた Synapse ワークスペースを作成できます。 マネージド仮想ネットワークが関連付けられたワークスペースを作成したら、[こちら](./how-to-create-managed-private-endpoints.md)の手順に従って、セキュリティで保護されたストレージ アカウントへのプライベート エンドポイントを作成できます。 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Azure Synapse ワークスペースに、セキュリティで保護されたストレージ アカウントへの、信頼された Azure サービスとしてのアクセスを許可します
専用 SQL プールやサーバーレス SQL プールなどの分析機能では、マネージド仮想ネットワークにデプロイされないマルチテナント インフラストラクチャが使用されます。 これらの機能からのトラフィックがセキュリティで保護されたストレージ アカウントにアクセスするには、以下の手順に従って、ワークスペースのシステム割り当てマネージド ID に基づいて、ストレージ アカウントへのアクセスを構成する必要があります。

Azure portal で、セキュリティで保護されたストレージ アカウントに移動します。 左側のナビゲーション ペインで **[ネットワーク]** を選択します。 **[リソース インスタンス]** セクションで、 **[リソースの種類]** として *Microsoft.Synapse/workspaces* を選択し、 **[インスタンス名]** にワークスペース名を入力します。 **[保存]** を選択します。

![ストレージ アカウントのネットワーク構成。](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

これで、ワークスペースからセキュリティで保護されたストレージ アカウントにアクセスできるようになります。


## <a name="next-steps"></a>次のステップ

[マネージド ワークスペースの仮想ネットワーク](./synapse-workspace-managed-vnet.md)の詳細を参照してください。

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を参照してください。