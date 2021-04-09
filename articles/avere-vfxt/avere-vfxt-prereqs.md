---
title: Avere vFXT の前提条件 - Azure
description: Avere vFXT for Azure でクラスターを作成する前に実行するタスクについて説明します。これには、サブスクリプション、クォータ、ストレージ サービス エンドポイントの処理も含まれます。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008414"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT の作成を準備する

この記事では、Avere vFXT クラスターを作成するための前提条件タスクについて説明します。

## <a name="create-a-new-subscription"></a>新しいサブスクリプションを作成する

最初に、新しい Azure サブスクリプションを作成します。 Avere vFXT プロジェクトごとに個別のサブスクリプションを使用することで、経費の追跡とクリーンアップを簡素化し、クラスターのワークフローをプロビジョニングするときに他のプロジェクトがクォータまたはリソースの調整の影響を受けないようにします。

Azure portal で新しい Azure サブスクリプションを作成するには:

1. [[サブスクリプション] ブレード](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動します
1. 上部にある **[+ 追加]** ボタンをクリックします
1. 求められた場合はサインインします
1. オファーを選択し、手順に従って新しいサブスクリプションを作成します

## <a name="configure-subscription-owner-permissions"></a>サブスクリプション所有者のアクセス許可を構成する

サブスクリプションの所有者アクセス許可を持つユーザーが、vFXT クラスターを作成する必要があります。 クラスターの作成には、所有者がソフトウェアのサービス使用条件に同意し、ネットワークとストレージ リソースに対する変更を承認する必要があります。

所有者以外が Avere vFXT for Azure クラスターを作成できるようにするための回避策がいくつかあります。 これらのシナリオでは、リソースの制限と、作成者への追加の Azure ロールの割り当てが行われます。 すべての場合で、サブスクリプションの所有者が、事前に [Avere vFXT ソフトウェアの条件に同意](#accept-software-terms)している必要もあります。

| シナリオ | 制限 | Avere vFXT クラスターを作成するために必要なアクセス ロール |
|----------|--------|-------|
| リソース グループ管理者が vFXT を作成する | 仮想ネットワーク、クラスター コントローラー、およびクラスター ノードが、リソース グループ内に作成されている必要がある。 | [ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)および[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール。両方とも、スコープはターゲット リソース グループに指定されている。 |
| 既存の外部仮想ネットワークを使用する | クラスター コントローラーとクラスター ノードは vFXT のリソース グループ内に作成されるが、別のリソース グループにある既存の仮想ネットワークが使用される。 | (1) [ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)および[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール。スコープは vFXT リソース グループに指定されている (2) [仮想マシン共同作成者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)、および [Avere 共同作成者](../role-based-access-control/built-in-roles.md#avere-contributor) ロール。スコープは仮想ネットワークのリソース グループに指定されている |
| クラスター作成者のカスタム ロール | リソース配置の制限はありません。 この方法では、所有者以外のユーザーに重要な特権が与えられます。 | サブスクリプション所有者は、[この記事](avere-vfxt-non-owner.md)で説明されているように、Azure のカスタム ロールを作成します。 |

## <a name="quota-for-the-vfxt-cluster"></a>vFXT クラスターのクォータ

次の Azure コンポーネントに対する十分なクォータがあることを確認します。 必要な場合は、[クォータの増量を要求](../azure-portal/supportability/resource-manager-core-quotas-request.md)します。

> [!NOTE]
> ここで示す仮想マシンと SSD コンポーネントは、vFXT クラスター自体のためのものです。 自分の計算ファーム用に使用する VM と SSD にも追加のクォータが必要であることに注意してください。
>
> ワークフローを実行するリージョンでクォータが有効になっていることを確認します。

|Azure のコンポーネント|Quota|
|----------|-----------|
|仮想マシン|3 つ以上の E32s_v3 (クラスター ノードごとに 1 つ) |
|Premium SSD ストレージ|200 GB の OS スペース、およびノードあたり 1 から 4 TB のキャッシュ |
|ストレージ アカウント (オプション) |v2|
|データ バックエンド ストレージ (オプション) |1 つの新しい LRS BLOB コンテナー |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>ソフトウェアの条件に同意する

> [!TIP]
> サブスクリプションの所有者が Avere vFXT クラスターを作成する場合は、この手順をスキップします。

クラスターの作成時に、Avere vFXT ソフトウェアのサービスの条件に同意する必要があります。 サブスクリプションの所有者でない場合は、前もってサブスクリプションの所有者に条件に同意してもらいます。

このステップは、サブスクリプションごとに 1 回だけ実行する必要があります。

事前にソフトウェアの条件に同意するには:

1. Azure portal で Cloud Shell を開くか、または <https://shell.azure.com> にアクセスします。 サブスクリプション ID でサインインします。

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. 次のコマンドを実行して、サービスの条件に同意し、Avere vFXT for Azure ソフトウェア イメージへのプログラム アクセスを有効にします。

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>仮想ネットワークにストレージ サービス エンドポイントを作成する (必要な場合)

[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)では、仮想ネットワークの外部で Azure BLOB トラフィックをルーティングするのではなく、このトラフィックをローカルで保持します。 これは、バックエンドのデータ ストレージに Azure BLOB を使用する Avere vFXT for Azure クラスターに推奨されます。

クラスターを作成する間に新しい仮想ネットワークを作成する場合は、エンドポイントが自動的に作成されます。 既存の仮想ネットワークを指定する場合、クラスターの作成中に新しい BLOB ストレージ コンテナーを作成したい場合は、その仮想ネットワークに Microsoft ストレージ サービス エンドポイントが含まれている必要があります。<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* クラスター作成の一環として新しい仮想ネットワークを作成する場合は、この手順をスキップしてください。
>* クラスターの作成中に BLOB ストレージを作成しない場合、エンドポイントは省略可能です。 その場合、Azure BLOB を使用する必要が生じても、後でサービス エンドポイントを作成できます。

Azure portal からストレージ サービス エンドポイントを作成します。

1. ポータルで、仮想ネットワークの一覧を開きます。
1. クラスター用の仮想ネットワークを選択します。
1. 左側のメニューにある **[サービス エンドポイント]** をクリックします。
1. 上部にある **[追加]** をクリックします。
1. サービス ``Microsoft.Storage`` を選択します。
1. クラスターのサブネットを選択します。
1. 下部にある **[追加]** をクリックします。

   ![サービス エンドポイントを作成する手順に関する注釈を示す Azure portal のスクリーンショット](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>次のステップ

これらの前提条件を完了したら、クラスターを作成できます。 手順については、「[Deploy the vFXT cluster](avere-vfxt-deploy.md)」(vFXT クラスターをデプロイする) をご覧ください。