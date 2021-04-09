---
title: 仮想ネットワークに Azure Spring Cloud をデプロイする
description: 仮想ネットワークに Azure Spring Cloud をデプロイする (VNet インジェクション)。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 82dcd8c59c55a2866b51fd6dee896ea1298b6cf6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877432"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>仮想ネットワークに Azure Spring Cloud をデプロイする

**この記事の適用対象:** ✔️ Java ✔️ C#

このチュートリアルでは、Azure Spring Cloud インスタンスを仮想ネットワークにデプロイする方法について説明します。 このデプロイは、VNet インジェクションとも呼ばれます。

デプロイでは次のことが可能です。

* 企業ネットワークでの Azure Spring Cloud アプリとサービス ランタイムのインターネットからの分離。
* オンプレミスのデータ センター内のシステムや、他の仮想ネットワーク内の Azure サービスとの Azure Spring Cloud の対話。
* Azure Spring Cloud の送受信ネットワーク通信を制御するための顧客への権限付与。

> [!Note]
> Azure 仮想ネットワークは、新しい Azure Spring Cloud のサービス インスタンスを作成する時点に限り選択できます。 Azure Spring Cloud を作成した後で、別の仮想ネットワークを使用するように変更することはできません。

## <a name="prerequisites"></a>前提条件

[Azure portal でのリソース プロバイダーの登録](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)に関するセクションの手順に従うか、以下の Azure CLI コマンドを実行して、Azure Spring Cloud リソース プロバイダー **Microsoft.AppPlatform** および **Microsoft.ContainerService** を登録します。

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>仮想ネットワークの要件

Azure Spring Cloud インスタンスのデプロイ先となる仮想ネットワークは、以下の要件を満たす必要があります。

* **[場所]** :仮想ネットワークは、Azure Spring Cloud インスタンスと同じ場所に存在する必要があります。
* **サブスクリプション**:仮想ネットワークは、Azure Spring Cloud インスタンスと同じサブスクリプションに存在する必要があります。
* **サブネット**:仮想ネットワークには、Azure Spring Cloud インスタンス専用の 2 つのサブネットが含まれている必要があります。

    * サービス ランタイム用に 1 つ。
    * Spring Boot マイクロサービス アプリケーション用に 1 つ。
    * これらのサブネットと Azure Spring Cloud インスタンスの間には、一対一のリレーションシップがあります。 デプロイするサービス インスタンスごとに新しいサブネットを使用します。 各サブネットには、1 つのサービス インスタンスのみを含めることができます。
* **[アドレス空間]** : サービス ランタイム サブネットと Spring Boot マイクロサービス アプリケーション サブネットの両方に対して最大 */28* までの CIDR ブロック。
* **ルート　テーブル**: 既定では、サブネットに既存のルート　テーブルが関連付けられている必要はありません。 [独自のルート テーブルを使用する](#bring-your-own-route-table)ことができます。

次の手順では、Azure Spring Cloud のインスタンスを含むように仮想ネットワークを設定する方法について説明します。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure Spring Cloud インスタンスをホストする仮想ネットワークが既にある場合は、手順 1、2、および 3 をスキップします。 手順 4 から開始して、仮想ネットワークのサブネットを準備することができます。

1. Azure portal のメニューで、**[リソースの作成]** を選択します。 Azure Marketplace で、 **[ネットワーク]**  >  **[仮想ネットワーク]** の順に選択します。

1. **[仮想ネットワークの作成]** ダイアログ ボックスで、次の情報を入力または選択します。

    |設定          |値                                             |
    |-----------------|--------------------------------------------------|
    |サブスクリプション     |サブスクリプションを選択します。                         |
    |Resource group   |リソース グループを選択するか、新しく作成します。  |
    |名前             |「**azure-spring-cloud-vnet**」と入力します。                 |
    |場所         |**[米国東部]** を選択します。                               |

1. **次へ:[次へ: IP アドレス]** を選択します。

1. [IPv4 アドレス空間] に、「**10.1.0.0/16**」と入力します。

1. **[サブネットの追加]** を選択します。 **[サブネット名]** に「**service-runtime-subnet**」と入力し、 **[サブネットのアドレス範囲]** に「**10.1.0.0/28**」と入力します。 その後、 **[追加]** を選択します。

1. **[サブネットの追加]** を再び選択し、**サブネット名** と **サブネット アドレス範囲** を入力します。 たとえば、「**apps-subnet**」と「**10.1.1.0/28**」と入力します。 その後、 **[追加]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 残りは既定値のままにして、 **[作成]** を選択します。

## <a name="grant-service-permission-to-the-virtual-network"></a>仮想ネットワークにサービス アクセス許可を付与する
仮想ネットワーク上の専用かつ動的なサービス プリンシパルにさらに高度なデプロイやメンテナンスの権限を付与するには、Azure Spring Cloud に仮想ネットワークの **所有者** としてのアクセス許可が必要です。

前に作成した仮想ネットワーク **azure-spring-cloud-vnet** を選択します。

1. **[アクセス制御 (IAM)]** を選択してから、 **[追加]**  >  **[ロール割り当ての追加]** の順に選択します。

    ![[アクセス制御] 画面を示すスクリーンショット。](./media/spring-cloud-v-net-injection/access-control.png)

1. **[ロールの割り当ての追加]** ダイアログ ボックスで、次の情報を入力または選択します。

    |設定  |値                                             |
    |---------|--------------------------------------------------|
    |Role     |**[所有者]** を選択します。                                 |
    |Select   |「**Azure Spring Cloud リソース プロバイダー**」と入力します。   |

    次に、 **[Azure Spring Cloud リソース プロバイダー]** を選択し、 **[保存]** を選択します。

    ![[Azure Spring Cloud リソース プロバイダー] の選択を示すスクリーンショット。](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

この手順は、次の Azure CLI コマンドを実行して行うこともできます。

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Azure Spring Cloud インスタンスのデプロイ

仮想ネットワークに Azure Spring Cloud インスタンスをデプロイするには、次のように操作します。

1. [Azure Portal](https://portal.azure.com)を開きます。

1. 上部の検索ボックスで、**Azure Spring Cloud** を検索します。 その結果から **[Azure Spring Cloud]** を選択します。

1. **[Azure Spring Cloud]** ページで **[+ 追加]** を選択します。

1. Azure Spring Cloud の **[作成]** ページで、フォームに入力します。

1. 仮想ネットワークと同じリソース グループおよびリージョンを選択します。

1. **[サービスの詳細]** の下にある **[名前]** で、[**azure-spring-cloud-vnet**] を選択します。

1. **[ネットワーク]** タブを選択し、以下の値を選択します。

    |設定                                |値                                             |
    |---------------------------------------|--------------------------------------------------|
    |Deploy in your own virtual network (自分の仮想ネットワーク内にデプロイする)     |**[はい]** を選択します。                                   |
    |仮想ネットワーク                        |**[azure-spring-cloud-vnet]** を選択します。               |
    |Service runtime subnet (サービス ランタイム サブネット)                 |**[service-runtime-subnet]** を選択します。                |
    |Spring Boot microservice apps subnet (Spring Boot マイクロサービス アプリ サブネット)   |**[apps-subnet]** を選択します。                           |

    ![Azure Spring Cloud の [作成] ページの [ネットワーク] タブを示すスクリーンショット。](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. **[確認と作成]** を選択します。

1. 指定を確認し、 **[作成]** を選択します。

    ![指定の確認を示すスクリーンショット。](./media/spring-cloud-v-net-injection/verify-specifications.png)

デプロイ後、Azure Spring Cloud インスタンスのネットワーク リソースをホストするために、サブスクリプションに 2 つの追加のリソース グループが作成されます。 **[ホーム]** に移動し、上部のメニュー項目から **[リソース グループ]** を選択して、次の新しいリソース グループを検索します。

**ap-svc-rt_{サービス インスタンス名}_{サービス インスタンスのリージョン}** という名前のリソース グループには、サービス インスタンスのサービス ランタイム用のネットワーク リソースが含まれています。

  ![サービス ランタイムを示すスクリーンショット。](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

**ap-app_{サービス インスタンス名}_{サービス インスタンスのリージョン}** という名前のリソース グループには、サービス インスタンスの Spring Boot マイクロサービス アプリケーション用のネットワーク リソースが含まれています。

  ![アプリのリソース グループを示すスクリーンショット。](./media/spring-cloud-v-net-injection/apps-resource-group.png)

これらのネットワーク リソースは、上の図で作成した仮想ネットワークに接続されています。

  ![デバイスが接続された仮想ネットワークを示すスクリーンショット。](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > リソース グループは、Azure Spring Cloud サービスによって完全に管理されています。 内部のリソースを手動で削除または変更 "*しない*" でください。

## <a name="using-smaller-subnet-ranges"></a>使用するサブネット範囲を小さくする

この表は、使用するサブネット範囲を小さくしていった場合に Azure Spring Cloud がサポートするアプリ インスタンスの最大数を示したものです。

| アプリ サブネットの CIDR | IP 総数 | 使用可能な IP 数 | 最大アプリ インスタンス                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> 1 コアのアプリ: 96 <br/> 2 コアのアプリ: 48<br/>  3 コアのアプリ: 32 <br/> 4 コアのアプリ: 24 </p> |
| /27             | 32        | 24            | <p> 1 コアのアプリ: 228<br/> 2 コアのアプリ: 144<br/>  3 コアのアプリ: 96 <br/>  4 コアのアプリ: 72</p> |
| /26             | 64        | 56            | <p> 1 コアのアプリ: 500<br/> 2 コアのアプリ: 336<br/>  3 コアのアプリ: 224<br/>  4 コアのアプリ: 168</p> |
| /25             | 128       | 120           | <p> 1 コアのアプリ: 500<br> 2 コアのアプリ: 500<br>  3 コアのアプリ: 480<br>  4 コアのアプリ: 360</p> |
| /24             | 256       | 248           | <p> 1 コアのアプリ: 500<br/> 2 コアのアプリ: 500<br/>  3 コアのアプリ: 500<br/>  4 コアのアプリ: 500</p> |

サブネットの場合、5 つの IP アドレスが Azure によって予約されており、Azure Spring Cloud で少なくとも 4 つのアドレスが必要です。 少なくとも 9 個の IP アドレスが必要になるため、/29 と /30 は非運用です。

サービス ランタイムのサブネットの場合、最小サイズは /28 です。 このサイズは、アプリ インスタンスの数には影響しません。

## <a name="bring-your-own-route-table"></a>独自のルート テーブルを使用する

Azure Spring Cloud では、既存のサブネットとルート テーブルの使用がサポートされています。

カスタム サブネットにルート テーブルが含まれていない場合、Azure Spring Cloud はサブネットごとにルート テーブルを作成し、インスタンスのライフサイクルを通してこれらにルールを追加します。 カスタム サブネットにルート テーブルが含まれている場合は、Azure Spring Cloud はインスタンスの操作中に既存のルート テーブルを確認し、操作に応じてルールを追加または更新します。

> [!Warning] 
> カスタム ルールをカスタム ルート テーブルに追加し、更新することができます。 ただし、ルールは Azure Spring Cloud によって追加され、ルールを更新したり削除したりすることはできません。 0\.0.0.0/0 などのルールは、特定のルート テーブルに常に存在し、NVA や他のエグレス ゲートウェイなどのインターネット ゲートウェイのターゲットにマップされている必要があります。 ルールの更新でカスタム ルールのみが変更されている場合は注意が必要です。


### <a name="route-table-requirements"></a>ルート テーブルの要件

カスタム vnet が関連付けられているルート テーブルは、次の要件を満たしている必要があります。

* Azure Spring Cloud サービス インスタンスの作成時のみ、Azure ルート テーブルを vnet に関連付けることができます。 Azure Spring Cloud の作成後は、別のルート テーブルを使用するように変更することはできません。
* マイクロサービス アプリケーション サブネットとサービス ランタイム サブネットは、異なるルート テーブルに関連付けるか、どちらもルート テーブルに関連付けないでおく必要があります。
* インスタンスの作成前にアクセス許可を割り当てる必要があります。 ルート テーブルには、必ず Azure "*Spring Cloud 所有者*" のアクセス許可を付与してください。
* クラスターを作成した後で、関連付けられているルート テーブル リソースを更新することはできません。 ルート テーブル リソースは更新できませんが、カスタム ルールはルート テーブルで変更できます。
* ルーティング規則が競合する可能性があるため、複数のインスタンスでルート テーブルを再利用することはできません。

## <a name="next-steps"></a>次のステップ

[VNet 内の Azure Spring Cloud にアプリケーションをデプロイする](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>関連項目

- [VNET での Azure Spring Cloud のトラブルシューティング](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [VNET での Azure Spring Cloud の実行に関するお客様の責任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
