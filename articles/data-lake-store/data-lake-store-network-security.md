---
title: Azure Data Lake Storage Gen1 のネットワーク セキュリティ | Microsoft Docs
description: Azure Data Lake Storage Gen1 における仮想ネットワーク統合のしくみについて説明します
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108713"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の仮想ネットワーク統合

この記事では、Azure Data Lake Storage Gen1 の仮想ネットワーク統合について紹介します。 仮想ネットワーク統合を使用すると、特定の仮想ネットワークとサブネットからのトラフィックのみを受け入れるようにアカウントを構成できます。 

この機能は、外部の脅威から Data Lake Storage アカウントを保護するのに役立ちます。

Data Lake Storage Gen1 の仮想ネットワーク統合では、仮想ネットワークと Azure Active Directory (Azure AD) との間で仮想ネットワーク サービス エンドポイント セキュリティを利用して、アクセス トークン内に追加のセキュリティ要求が生成されます。 これらの要求は、ご利用の Data Lake Storage Gen1 アカウントに対して仮想ネットワークを認証し、アクセスを許可するために使用されます。

> [!NOTE]
> これらの機能を使用することに関して追加料金はかかりません。 ご利用のアカウントは、Data Lake Storage Gen1 の標準料金で課金されます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)に関するページを参照してください。 お客様が使用する他のすべての Azure サービスについては、[価格](https://azure.microsoft.com/pricing/#product-picker)に関するページを参照してください。

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の仮想ネットワーク統合のシナリオ

Data Lake Storage Gen1 仮想ネットワーク統合を使用すると、特定の仮想ネットワークとサブネットから Data Lake Storage Gen1 アカウントへのアクセスを制限できます。 指定された仮想ネットワーク サブネットにアカウントがロックされた後は、Azure の他の仮想ネットワークや VM はアクセスが許可されません。 Data Lake Storage Gen1 仮想ネットワーク統合では、機能上、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)と同じシナリオが可能になります。 以下のセクションでは、いくつかの主な違いについて詳しく説明します。 

![Data Lake Storage Gen1 仮想ネットワーク統合のシナリオ図](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> オンプレミス ネットワークからのアクセスを許可するために、仮想ネットワーク規則に加えて、既存の IP ファイアウォール規則も使用できます。 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Data Lake Storage Gen1 仮想ネットワーク統合による最適なルーティング

仮想ネットワーク サービス エンドポイントの主な利点は、ご利用の仮想ネットワークからの[最適なルーティング](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits)です。 Data Lake Storage Gen1 アカウントに対して同じルートの最適化を実行できます。 次に示す、仮想ネットワークから Data Lake Storage Gen1 アカウントへの[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)を使用します。

**Data Lake Storage パブリック IP アドレス** - ターゲット Data Lake Storage Gen1 アカウントのパブリック IP アドレスを使用します。 Data Lake Storage Gen1 アカウントの IP アドレスを特定するには、アカウントの [DNS 名を解決](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)します。 アドレスごとに個別のエントリを作成してください。

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>顧客の仮想ネットワークからのデータ流出

仮想ネットワークからのアクセスに関して Data Lake Storage アカウントを保護することに加え、承認されていないアカウントへの流出を確実に防ぐことについても関心がおありかもしれません。

仮想ネットワークでファイアウォール ソリューションを使用し、宛先アカウントの URL に基づいてアウトバウンド トラフィックをフィルター処理します。 承認された Data Lake Storage Gen1 アカウントにのみアクセスできるようにします。

考えられる選択肢としては、以下のようなものがあります。
- [Azure Firewall](../firewall/overview.md): 仮想ネットワークに [Azure ファイアウォールをデプロイして構成](../firewall/tutorial-firewall-deploy-portal.md)します。 アウトバウンドの Data Lake Storage トラフィックを保護し、承認されている既知のアカウントの URL にロックダウンします。
- [ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) ファイアウォール: 特定の商用ファイアウォール ベンダーのみ、管理者によって利用が許可されている場合があります。 Azure Marketplace で入手できるネットワーク仮想アプライアンス ファイアウォール ソリューションを使用して、同じ機能を実行します。

> [!NOTE]
> データ パス内でファイアウォールを使用すると、データ パスのホップ数が増えます。 これにより、エンドツーエンドのデータ交換におけるネットワーク パフォーマンスが影響を受ける場合があります。 スループットの可用性と接続の待ち時間に影響する可能性があります。 

## <a name="limitations"></a>制限事項

- Data Lake Storage Gen1 仮想ネットワーク統合のサポートが利用可能になる前に作成された HDInsight クラスターは、この新機能をサポートするために作成し直す必要があります。
 
- 新しい HDInsight クラスターを作成し、仮想ネットワーク統合が有効な Data Lake Storage Gen1 アカウントを選択すると、プロセスは失敗します。 最初に、仮想ネットワーク規則を無効にします。 または、Data Lake Storage アカウントの **[ファイアウォールと仮想ネットワーク]** ブレードで、**[Allow access from all networks and services]\(すべてのネットワークとサービスからのアクセスを許可する\)** を選択します。 その後、最終的に仮想ネットワーク規則を再度有効にするか、**[Allow access from all networks and services]\(すべてのネットワークとサービスからのアクセスを許可する\)** を選択解除する前に、HDInsight クラスターを作成します。 詳細については、「[例外](#exceptions)」セクションを参照してください。

- Data Lake Storage Gen1 仮想ネットワーク統合は、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に対応していません。
  
- 仮想ネットワークが有効な Data Lake Storage Gen1 アカウントのファイルとフォルダーのデータにポータルからアクセスすることはできません。 この制限には、仮想ネットワーク内の VM からのアクセスや、データ エクスプローラーの使用などのアクティビティが含まれます。 アカウント管理アクティビティは、引き続きご利用いただけます。 仮想ネットワークが有効な Data Lake Storage アカウントのファイルとフォルダーのデータには、ポータル以外のすべてのリソース経由でアクセスできます。 これらのリソースには、SDK アクセスや PowerShell スクリプトのほか、アクセス元がポータルでないその他の Azure サービスが該当します。 

## <a name="configuration"></a>構成

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>手順 1: Azure AD サービス エンドポイントを使用するための仮想ネットワークを構成する

1.  Azure portal に移動し、ご利用の Azure アカウントにサインインします。
 
2.  ご利用のサブスクリプションに[新しい仮想ネットワークを作成](../virtual-network/quick-create-portal.md)します。 または、既存の仮想ネットワークに移動できます。 仮想ネットワークは、Data Lake Storage Gen 1 アカウントと同じリージョンに属している必要があります。
 
3.  **[仮想ネットワーク]** ブレードで、**[サービス エンドポイント]** を選択します。
 
4.  **[追加]** を選択して、新しいサービス エンドポイントを追加します。

    ![仮想ネットワーク サービス エンドポイントを追加する](media/data-lake-store-network-security/config-vnet-1.png)

5.  エンドポイントのサービスとして **[Microsoft.AzureActiveDirectory]** を選択します。

     ![[Microsoft.AzureActiveDirectory] サービス エンドポイントを選択する](media/data-lake-store-network-security/config-vnet-2.png)

6.  接続を許可したいサブネットを選択します。 **[追加]** を選択します。

    ![サブネットを選択する](media/data-lake-store-network-security/config-vnet-3.png)

7.  サービス エンドポイントが追加されるまでに最大 15 分かかることがあります。 追加された後、これはリストに表示されます。 これが表示されていること、およびすべての詳細が構成済みであることを確認します。
 
    ![サービス エンドポイントの追加に成功](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>手順 2: Data Lake Storage Gen1 アカウントに対して許可する仮想ネットワークまたはサブネットを設定する

1.  仮想ネットワークを構成した後、ご利用のサブスクリプションに[新しい Azure Data Lake Storage Gen1 アカウントを作成](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)します。 または、既存の Data Lake Storage Gen1 アカウントに移動できます。 Data Lake Storage Gen1 アカウントは、仮想ネットワークと同じリージョンに属している必要があります。
 
2.  **[ファイアウォールと仮想ネットワーク]** を選択します。

    > [!NOTE]
    > **[ファイアウォールと仮想ネットワーク]** が設定に表示されない場合は、ポータルからログオフします。 ブラウザーを閉じた後、ブラウザーのキャッシュをクリアします。 マシンを再起動して再試行します。

       ![仮想ネットワーク規則を Data Lake Storage アカウントに追加する](media/data-lake-store-network-security/config-adls-1.png)

3.  **[選択されたネットワーク]** を選択します。
 
4.  **[既存の仮想ネットワークを追加]** を選びます。

    ![[既存の仮想ネットワークを追加]](media/data-lake-store-network-security/config-adls-2.png)

5.  接続を許可する仮想ネットワークとサブネットを選択します。 **[追加]** を選択します。

    ![仮想ネットワークとサブネットを選択する](media/data-lake-store-network-security/config-adls-3.png)

6.  仮想ネットワークとサブネットがリストに正しく表示されていることを確認します。 **[保存]** を選択します。

    ![新しい規則を保存する](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > 保存後、設定が反映されるまでに最大 5 分かかることがあります。

7.  (省略可能) **[ファイアウォールと仮想ネットワーク]** ページの **[ファイアウォール]** セクションで、特定の IP アドレスからの接続を許可できます。 

## <a name="exceptions"></a>例外
選択した仮想ネットワーク外の Azure サービスと VM からの接続を有効にすることができます。 **[ファイアウォールと仮想ネットワーク]** ブレードの **[例外]** 領域で、次の 2 つのオプションから選択します。
 
- **[すべての Azure サービスによるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]**。 このオプションを使用すると、Azure Data Factory、Azure Event Hubs、およびすべての Azure VM などの Azure サービスが Data Lake Storage アカウントと通信できます。

- **[Azure Data Lake Analytics によるこの Data Lake Storage Gen1 アカウントへのアクセスを許可します]**。 このオプションを使用すると、この Data Lake Storage アカウントへの Data Lake Analytics の接続が許可されます。 

  ![ファイアウォールと仮想ネットワークの例外](media/data-lake-store-network-security/firewall-exceptions.png)

これらの例外はオフにしておくことをお勧めします。 ご利用の仮想ネットワークの外部にあるこれらの他のサービスから接続を行う必要がある場合にのみ、これらをオンにしてください。