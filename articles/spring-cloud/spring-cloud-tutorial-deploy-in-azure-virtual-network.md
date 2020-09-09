---
title: チュートリアル - 仮想ネットワークに Azure Spring Cloud をデプロイする
description: 仮想ネットワークに Azure Spring Cloud をデプロイします (v-net インジェクション)。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: a7905ae0fdbd797d9b544cb71f44b82af1295246
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688455"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>チュートリアル:Azure 仮想ネットワークに Azure Spring Cloud をデプロイする (VNet インジェクション)

このチュートリアルでは、Azure Spring Cloud サービス インスタンスを仮想ネットワークにデプロイする方法について説明します。 これは、VNet インジェクションとも呼ばれます。  

デプロイでは次のことが可能です。

* 企業ネットワークでの Azure Spring Cloud アプリとサービス ランタイムのインターネットからの分離
* オンプレミスのデータ センター内のシステムや、他の仮想ネットワーク内の Azure サービスとの Azure Spring Cloud の対話
* Azure Spring Cloud の送受信ネットワーク通信を制御するための顧客への権限付与

## <a name="prerequisites"></a>前提条件
[Azure portal でのリソース プロバイダーの登録](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)に関する記事の手順に従って、または次の az CLI コマンドを実行することによって、Azure Spring Cloud リソース プロバイダー `Microsoft.AppPlatform` を登録する必要があります。

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>仮想ネットワークの要件
Azure Spring Cloud サービス インスタンスのデプロイ先となる仮想ネットワークは、以下の要件を満たす必要があります。

* **[場所]** :仮想ネットワークは、Azure Spring Cloud サービス インスタンスと同じ場所に存在する必要があります。
* **サブスクリプション**:仮想ネットワークは、Azure Spring Cloud サービス インスタンスと同じサブスクリプションに存在する必要があります。
* **サブネット**:仮想ネットワークには、Azure Spring Cloud サービス インスタンス専用の 2 つのサブネットが含まれている必要があります。 
    * サービス ランタイム用に 1 つ
    * Spring Boot マイクロサービス アプリケーション用に 1 つ。 
    * これらのサブネットと Azure Spring Cloud サービス インスタンスの間には一対一のリレーションシップがあります。 複数のサービス インスタンスを 1 つのサブネットで共有することはできません。 デプロイするサービス インスタンスごとに新しいサブネットを使用する必要があります。
* **[アドレス空間]** : サービス ランタイム サブネットに対して /28 までの CIDR ブロック 1 つ、および Spring Boot マイクロサービス アプリケーション サブネットに対して /24 までの別の CIDR ブロック。
* **[ルート テーブル]** :サブネットに既存のルート テーブルを関連付けることはできません。

次の手順では、Azure Spring Cloud のインスタンスを含むように仮想ネットワークを設定する方法について説明します。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
Azure Spring Cloud サービス インスタンスをホストする仮想ネットワークが既にある場合は、手順 1、2、3 をスキップします。 手順 4 から開始して、仮想ネットワークのサブネットを準備することができます。

1. Azure portal メニューから **[リソースの作成]** を選択します。 Azure Marketplace で、 **[ネットワーク]**  >  **[仮想ネットワーク]** を選択します。

1. **[仮想ネットワークの作成]** ダイアログで、次の情報を入力または選択します。

    |設定          |値                                             |
    |-----------------|--------------------------------------------------|
    |サブスクリプション     |サブスクリプションを選択します。                         |
    |Resource group   |リソース グループを選択するか、新しく作成します。  |
    |名前             |「*azure-spring-cloud-vnet*」と入力します                   |
    |場所         |**[米国東部]** を選択します。                                |

1. **[次へ: IP アドレス >]** をクリックします。 
 
1. [IPv4 アドレス空間] に、「10.1.0.0/16」と入力します。

1. **[サブネットの追加]** を選択し、 **[サブネット名]** に「*service-runtime-subnet*」、 **[サブネットのアドレス範囲]** に「10.1.0.0/24」と入力します。 **[追加]** をクリックします。

1. **[サブネットの追加]** を再び選択し、 **[サブネット名]** に「*apps-subnet*」、 **[サブネットのアドレス範囲]** に「10.1.1.0/24」と入力します。  **[追加]** をクリックします。

1. **[Review + create]\(レビュー + 作成\)** をクリックします。 残りの部分は既定値のままにし、 **[作成]** をクリックします。

## <a name="grant-service-permission-to-the-virtual-network"></a>仮想ネットワークにサービス アクセス許可を付与する

前に作成した仮想ネットワーク *azure-spring-cloud-vnet* を選択します。

1. **[アクセス制御 (IAM)]** を選択したら、 **[追加] > [ロール割り当ての追加]** を選択します。

    ![V-net のアクセス制御](./media/spring-cloud-v-net-injection/access-control.png)

2. **[ロールの割り当ての追加]** ダイアログで、次の情報を入力または選択します。

    |設定  |値                                             |
    |---------|--------------------------------------------------|
    |Role     |**[所有者]** を選択します                                  |
    |Select   |「*Azure Spring Cloud リソース プロバイダー*」と入力します      |

    次に、[*Azure Spring Cloud リソース プロバイダー*] を選択し、 **[保存]** をクリックします。

    ![Azure Spring Cloud リソース プロバイダーを V-net に付与する](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

これは、次の az CLI コマンドを実行して実現することもできます

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>仮想ネットワークに Azure Spring Cloud サービス インスタンスをデプロイする

1. https://ms.portal.azure.com を使用して Azure portal を開きます。

1. 上部の検索ボックスから **Azure Spring Cloud** を検索し、結果から **[Azure Spring Cloud]** を選択します。

1. **[Azure Spring Cloud]** ページで **[+ 追加]** を選択します。

1. Azure Spring Cloud の **[作成]** ページで、フォームに入力します。 

1. 仮想ネットワークと同じリソース グループおよびリージョンを選択します。

1. **[サービスの詳細]** の下にある **[名前]** で、[*azure-spring-cloud-vnet*] を選択します。

1. **[ネットワーク]** タブを選択し、次のように選択します。

    |設定                                |値                                             |
    |---------------------------------------|--------------------------------------------------|
    |Deploy in your own virtual network (自分の仮想ネットワーク内にデプロイする)     |**[はい]** を選択します                                    |
    |仮想ネットワーク                        |[*azure-spring-cloud-vnet*] を選択します                  |
    |Service runtime subnet (サービス ランタイム サブネット)                 |[*service-runtime-subnet*] を選択します                   |
    |Spring Boot microservice apps subnet (Spring Boot マイクロサービス アプリ サブネット)   |[*apps-subnet*] を選択します                              |

    ![作成の [ネットワーク] タブ](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. **[確認と作成]** をクリックします。

1. 仕様を確認し、 **[作成]** をクリックします。

デプロイ後、Azure Spring Cloud サービス インスタンスのネットワーク リソースをホストするために、サブスクリプションに 2 つの追加のリソース グループが作成されます。  **[ホーム]** に移動し、上部のメニュー項目から **[リソース グループ]** を選択して、次の新しいリソース グループを検索します。

*azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* という名前のリソース グループには、サービス インスタンスのサービス ランタイム用のネットワーク リソースが含まれています。

  ![サービス ランタイム](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

*azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* という名前のリソース グループには、サービス インスタンスの Spring Boot マイクロサービス アプリケーション用のネットワーク リソースが含まれています。

  ![アプリのリソース グループ](./media/spring-cloud-v-net-injection/apps-resource-group.png)

これらのネットワーク リソースは、上記で作成した仮想ネットワークに接続されています。

  ![接続されているデバイスがある V-net](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > リソース グループは、Azure Spring Cloud サービスによって完全に管理されています。 内部のリソースを手動で削除または変更しないでください。

## <a name="next-steps"></a>次のステップ

[VNet 内の Azure Spring Cloud にアプリケーションをデプロイする](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>関連項目

- [VNET での Azure Spring Cloud のトラブルシューティング](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [VNET での Azure Spring Cloud の実行に関するお客様の責任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)