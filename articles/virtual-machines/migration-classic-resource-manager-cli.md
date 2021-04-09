---
title: Azure CLI を使用して Resource Manager に VM を移行する
description: この記事では、Azure CLI を使用した、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について説明します。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 671b27f927c91397d2aacd98cb7b500d8197d1c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669340"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する

> [!IMPORTANT]
> 現在、IaaS VM の約 90% が [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) を使用しています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨について[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

以下の手順では、Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに移行する方法を説明します。 この記事では、[Azure クラシック CLI](/cli/azure/install-classic-cli) が必要です。 Azure CLI は、Azure Resource Manager リソースにのみ適用されるため、この移行には使用できません。

> [!NOTE]
> ここで説明するすべての操作がべき等です。 サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。 これによりプラットフォームでアクションが再試行されます。
> 
> 

<br>
移行プロセス中に実行する必要のある手順を順番に示すフローチャートを以下に示します。

![Screenshot that shows the migration steps](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>手順 1:移行を準備する
ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

* [サポートされていない構成または機能の一覧](migration-classic-resource-manager-overview.md)をご確認ください。 サポートされていない構成または機能を使用する仮想マシンがある場合、機能または構成のサポートが発表されるまで待つことをお勧めします。 または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
* インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。 または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。

> [!IMPORTANT]
> 現在、Application Gateway は、クラシックから Resource Manager への移行には対応していません。 Application Gateway が設定された従来の仮想ネットワークを移行するには、準備操作を実行してネットワークを移動する前に、まずそのゲートウェイを削除する必要があります。 移行が完了した後、Azure Resource Manager でゲートウェイに再接続します。 
>
>別のサブスクリプションの ExpressRoute 回線に接続する ExpressRoute ゲートウェイは自動移行できません。 そのような場合は、ExpressRoute ゲートウェイを削除し、仮想ネットワークを移行してゲートウェイを再作成します。 詳細については、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../expressroute/expressroute-migration-classic-resource-manager.md)」を参照してください。
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>手順 2:ご自分のサブスクリプションを設定し、プロバイダーを登録する
移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。 [Azure CLI をインストール](/cli/azure/install-classic-cli)し、[サブスクリプションを選択](/cli/azure/authenticate-azure-cli)します。

アカウントにサインインします。

```azurecli
azure login
```

次のコマンドを使用して Azure サブスクリプションを選択します。

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> 登録は 1 回限りの手順ですが、移行を試みる前に実行する必要があります。 登録を行わないと、次のエラー メッセージが表示されます 
> 
> *BadRequest: 移行の対象サブスクリプションが登録されていません。* 
> 
> 

移行リソース プロバイダーに登録するには、次のコマンドを使用します。 次のコマンドは、場合によってはタイムアウトになるため注意してください。ただし、登録は成功します。

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

登録が完了するまで 5 分間お待ちください。 承認の状態を確認するには、次のコマンドを使用します。 RegistrationState が `Registered` であることを確認してから続行してください。

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

次に、CLI を `asm` モードに切り替えます。

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>手順 3:現在のデプロイまたは VNET の Azure リージョンで Azure Resource Manager 仮想マシンの vCPU 数が十分にあることを確認する
この手順では、`arm` モードに切り替える必要があります。 これを行うには、次のコマンドを使用します。

```azurecli
azure config mode arm
```

Azure Resource Manager での現在の vCPU 数は、次の CLI コマンドを使用して確認できます。 vCPU クォータの詳細については、「[制限と Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)」を参照してください。

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

確認が終わったら、 `asm` モードに切り替えることができます。

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>手順 4:オプション 1 - クラウド サービス内の仮想マシンを移行する
次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。 クラウド サービスの VM が仮想ネットワーク内にある場合、または Web/worker ロールを持つ場合は、エラー メッセージが返されます。

```azurecli
azure service list
```

次のコマンドを実行して、詳細出力からクラウド サービスのデプロイ名を取得します。 ほとんどの場合、デプロイ名はクラウド サービス名と同じです。

```azurecli
azure service show <serviceName> -vv
```

最初に、次のコマンドを使用して、クラウド サービスを移行できるかどうかを検証します。

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

移行に合わせてクラウド サービスの仮想マシンを準備します。 2 つのオプションから選択できます。

プラットフォームで作成した仮想ネットワークに VM を移行する場合は、次のコマンドを使用します。

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Resource Manager デプロイ モデルの既存の仮想ネットワークに移行する場合は、次のコマンドを使用します。

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

準備操作が正常に完了したら、詳細出力で VM の移行状態を調べ、VM が `Prepared` 状態であることを確認します。

```azurecli
azure vm show <vmName> -vv
```

CLI または Azure ポータルを使用して、準備したリソースの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>手順 4:オプション 2 - 仮想ネットワーク内の仮想マシンを移行する
移行する仮想ネットワークを選択します。 仮想ネットワークに Web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

次のコマンドを使用して、サブスクリプションのすべての仮想ネットワークを取得します。

```azurecli
azure network vnet list
```

出力は次のようになります。

![仮想ネットワークの名前全体が強調表示されているコマンド ラインのスクリーン ショット。](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

上の例では、 **"Group classicubuntu16 classicubuntu16"** 全体が **virtualNetworkName** です。

最初に、次のコマンドを使用して、仮想ネットワークを移行できるかどうかを検証します。

```shell
azure network vnet validate-migration <virtualNetworkName>
```

次のコマンドを使用して、移行対象の仮想ネットワークを準備します。

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

CLI または Azure ポータルを使用して、準備した仮想マシンの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>手順 5:ストレージ アカウントを移行する
仮想マシンの移行が完了したら、ストレージ アカウントを移行することをお勧めします。

次のコマンドを使用して、移行対象のストレージ アカウントを準備します

```azurecli
azure storage account prepare-migration <storageAccountName>
```

CLI または Azure ポータルを使用して、準備したストレージ アカウントの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```azurecli
azure storage account abort-migration <storageAccountName>
```

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md)
* [Review most common migration errors](migration-classic-resource-manager-errors.md) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md)
