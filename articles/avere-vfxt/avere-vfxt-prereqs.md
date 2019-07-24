---
title: Avere vFXT の前提条件 - Azure
description: Avere vFXT for Azure の前提条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786431"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT の作成を準備する

この記事では、Avere vFXT クラスターを作成するための前提条件タスクについて説明します。

## <a name="create-a-new-subscription"></a>新しいサブスクリプションを作成する

最初に、新しい Azure サブスクリプションを作成します。 Avere vFXT プロジェクトごとに個別のサブスクリプションを使用することで、プロジェクトのすべてのリソースと経費を簡単に追跡し、プロビジョニング中に他のプロジェクトのリソースが調整される可能性を防ぎ、簡単にクリーンアップできるようにします。  

Azure portal で新しい Azure サブスクリプションを作成するには:

* [[サブスクリプション] ブレード](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動します
* 上部にある **[+ 追加]** ボタンをクリックします
* 求められた場合はサインインします
* オファーを選択し、手順に従って新しいサブスクリプションを作成します

## <a name="configure-subscription-owner-permissions"></a>サブスクリプション所有者のアクセス許可を構成する

サブスクリプションの所有者アクセス許可を持つユーザーが、vFXT クラスターを作成する必要があります。 ソフトウェアのサービス使用条件に同意して他の操作を実行するには、サブスクリプションの所有者アクセス許可が必要になります。 

所有者以外が Avere vFTX for Azure クラスターを作成できるように、回避策のシナリオがいくつかあります。 これらのシナリオでは、リソースの制限と、作成者に対する追加ロールの割り当てが行われます。 また、どちらの場合にも、サブスクリプションの所有者が、事前に [Avere vFXT ソフトウェアの条件に同意](#accept-software-terms)している必要があります。 

| シナリオ | 制限 | Avere vFXT クラスターを作成するために必要なアクセス ロール | 
|----------|--------|-------|
| リソース グループの管理者 | 仮想ネットワーク、クラスター コントローラー、およびクラスター ノードが、リソース グループ内に作成されている必要がある。 | [ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)および[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール。両方とも、スコープはターゲット リソース グループに指定されている | 
| 外部の vnet | クラスター コントローラーとクラスター ノードはリソース グループ内に作成されるが、別のリソース グループにある既存の仮想ネットワークが使用される | (1) [ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)および[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール。スコープは vFXT リソース グループに指定されている (2) [仮想マシン共同作成者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)、および [Avere 共同作成者](../role-based-access-control/built-in-roles.md#avere-contributor) ロール。スコープは VNET リソース グループに指定されている |
 
別の方法として、[こちらの記事](avere-vfxt-non-owner.md)に説明されているように、カスタム ロールベースのアクセス制御 (RBAC) ロールを使用してユーザーに権限を割り当てることもできます。 この方法では、ユーザーに重要なアクセス許可が与えられます。 

## <a name="quota-for-the-vfxt-cluster"></a>vFXT クラスターのクォータ

次の Azure コンポーネントに対する十分なクォータが必要です。 必要な場合は、[クォータの増量を要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)します。

> [!NOTE]
> ここで示す仮想マシンと SSD コンポーネントは、vFXT クラスター自体のためのものです。 自分の計算ファーム用に使用する VM と SSD には、追加のクォータが必要です。  ワークフローを実行するリージョンでクォータが有効になっていることを確認します。

|Azure のコンポーネント|Quota|
|----------|-----------|
|仮想マシン|3 つ以上の E32s_v3|
|Premium SSD ストレージ|200 GB の OS スペース、およびノードあたり 1 から 4 TB のキャッシュ |
|ストレージ アカウント (オプション) |v2|
|データ バックエンド ストレージ (オプション) |1 つの新しい LRS BLOB コンテナー |

## <a name="accept-software-terms"></a>ソフトウェアの条件に同意する

> [!NOTE] 
> サブスクリプションの所有者が Avere vFXT クラスターを作成する場合は、このステップは必要ありません。

クラスターの作成時に、Avere vFXT ソフトウェアのサービスの条件に同意する必要があります。 サブスクリプションの所有者でない場合は、前もってサブスクリプションの所有者に条件に同意してもらいます。 このステップは、サブスクリプションごとに 1 回だけ実行する必要があります。

事前にソフトウェアの条件に同意するには: 

1. Azure portal で Cloud Shell を開くか、または <https://shell.azure.com> にアクセスします。 サブスクリプション ID でサインインします。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 次のコマンドを実行して、サービスの条件に同意し、Avere vFXT for Azure ソフトウェア イメージへのプログラム アクセスを有効にします。 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>仮想ネットワークにストレージ サービス エンドポイントを作成する (必要な場合)

[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)では、仮想ネットワークの外部で Azure BLOB トラフィックをルーティングするのではなく、このトラフィックをローカルで保持します。 これは、バックエンドのデータ ストレージに Azure BLOB を使用する Avere vFXT for Azure クラスターに推奨されます。 

クラスター作成の一環として、既存の VNet を指定し、バックエンド ストレージ用の新しい Azure BLOB コンテナーを作成する場合は、Microsoft ストレージ用の VNet にサービス エンドポイントが必要です。 このエンドポイントは、クラスターを作成する前に配置されている必要があります。配置されていない場合、クラスターの作成が失敗します。 

後でストレージを追加する場合でも、Azure BLOB ストレージを使用する Avere vFXT for Azure クラスターにはストレージ サービス エンドポイントをお勧めします。 

> [!TIP] 
> * クラスター作成の一環として新しい仮想ネットワークを作成する場合は、この手順をスキップしてください。 
> * クラスターの作成中に BLOB ストレージを作成しない場合、この手順は省略可能です。 その場合、Azure BLOB を使用する必要が生じても、後でサービス エンドポイントを作成できます。

Azure portal からストレージ サービス エンドポイントを作成します。 

1. ポータルで、左側にある **[仮想ネットワーク]** をクリックします。
1. クラスターの VNet を選択します。 
1. 左側にある **[サービス エンドポイント]** をクリックします。
1. 上部にある **[追加]** をクリックします。
1. サービスは ``Microsoft.Storage`` のままにして、クラスターのサブネットを選択します。
1. 下部にある **[追加]** をクリックします。

   ![サービス エンドポイントを作成する手順に関する注釈を示す Azure portal のスクリーンショット](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>次のステップ: vFXT クラスターを作成する

以上の前提条件を完了すると、クラスター自体の作成に進むことができます。 手順については、「[Deploy the vFXT cluster](avere-vfxt-deploy.md)」(vFXT クラスターをデプロイする) をご覧ください。