---
title: OpenShift を Azure Stack にデプロイする
description: Azure Stack に OpenShift をデプロイします。
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667235"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Azure Stack で OpenShift Container Platform または OKD をデプロイする

OpenShift は Azure Stack にデプロイできます。 Azure と Azure Stack の間には大きな違いがいくつかあるため、デプロイも機能も若干異なります。

現在のところ、Azure Cloud Provider は Azure Stack で動作しません。 そのため、Azure Stack では、永続的なストレージのためにディスク接続を使用することはできません。 その代わり、NFS、iSCSI、GlusterFS など、他のストレージ オプションを構成できます。代わりに、CNS を有効にし、GlusterFS を使用することで永続的なストレージが可能になります。 CNS が有効になっている場合、3 つの追加ノードがデプロイされ、GlusterFS を使用するためのストレージが追加されます。

Azure Stack で OpenShift Container Platform または OKD をデプロイするには、次のいずれかの方法を使用できます。

- [OpenShift Container Platform のドキュメント](https://docs.openshift.com/container-platform)または [OKD ドキュメント](https://docs.okd.io)の手順を実行するには、事前に必要な Azure インフラストラクチャ コンポーネントを手動でデプロイする必要があります。
- また、OpenShift Container Platform クラスターのデプロイを簡略化する既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-container-platform/)を使用することもできます。
- 既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-origin)を使用して、OKD クラスターのデプロイを簡略化することもできます。

Resource Manager テンプレートを使用している場合、適切なブランチを選択します (azurestack-release-3.x)。 Azure と Azure Stack では API バージョンが異なるため、Azure 向けのテンプレートは機能しません。 現時点では RHEL イメージ参照は azuredeploy.json ファイルに変数としてハード コーディングされており、イメージに一致するように変更する必要があります。

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

どのオプションでも、Red Hat サブスクリプションが必要です。 デプロイ中に、Red Hat Enterprise Linux インスタンスは Red Hat サブスクリプションに登録され、OpenShift Container Platform の資格を含むプール ID に接続されます。
有効な Red Hat Subscription Manager (RHSM) のユーザー名、パスワード、およびプール ID があることを確認してください。 あるいは、ライセンス認証キー、組織 ID、プール ID を使用できます。  これらの情報は、 https://access.redhat.com にサインインして確認できます。

## <a name="azure-stack-prerequisites"></a>Azure Stack の前提条件

OpenShift クラスターをデプロイするには、RHEL イメージ (OpenShift Container Platform) または CentOS イメージ (OKD) を Azure Stack 環境に追加する必要があります。 Azure Stack 管理者に連絡し、これらのイメージを追加してください。 手順は次の場所にあります。

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>OpenShift Container Platform または OKD Resource Manager テンプレートを使用したデプロイ

Resource Manager テンプレートを使用してデプロイするには、パラメーター ファイルを使用して入力パラメーターを指定します。 デプロイをさらにカスタマイズするには、GitHub リポジトリをフォークし、適切な項目を変更します。

一般的なカスタマイズ オプションには以下のような項目がありますが、この限りではありません。

- Bastion VM サイズ (azuredeploy.json 内の変数)
- 名前付け規則 (azuredeploy.json 内の変数)
- OpenShift クラスターの詳細 - ホスト ファイル (deployOpenShift.sh) 経由で変更済み
- RHEL イメージ参照 (azuredeploy.json 内の変数)

Azure CLI を使用したデプロイの手順については、[OpenShift Container Platform](./openshift-container-platform-3x.md) に関するセクションまたは [OKD](./openshift-okd.md) に関するセクションの該当セクションに従ってください。

## <a name="next-steps"></a>次のステップ

- [デプロイ後タスク](./openshift-container-platform-3x-post-deployment.md)
- [Azure での OpenShift デプロイのトラブルシューティング](./openshift-container-platform-3x-troubleshooting.md)