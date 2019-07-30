---
title: CloudSimple による Azure VMware ソリューションのクイック スタート - サービスの作成
description: CloudSimple サービスを作成したり、ノードをプロビジョニングしたり、ノードを予約したりする方法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164214"
---
# <a name="quickstart---create-service"></a>クイック スタート - サービスの作成

始めに、Azure ポータルで CloudSimple によって Azure VMware ソリューションを作成します。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>CloudSimple による VMware ソリューション - サービス概要

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。  このサービスを作成すると、ノードをプロビジョニングしたり、ノードを予約したり、プライベート クラウドを作成したりできます。  CloudSimple サービスが使用可能な各 Azure 領域に、CloudSimple サービスを追加します。  サービスは、CloudSimple による Azure VMware ソリューションのエッジ ネットワーク を定義します。  このエッジ ネットワークは、VPN、ExpressRoute、およびプライベート クラウドへのインターネット接続性などのサービスに使用されます。

CloudSimple サービスを追加するには、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 これは、オンプレミス ネットワークのアドレス空間や Azure の仮想ネットワーク アドレス空間と重複できません。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Microsoft.VMwareCloudSimple リソース プロバイダーを有効にします。

CloudSimple サービスのリソース プロバイダーを有効にするには、次の手順に従います。

1. **[すべてのサービス]** を選択します。
2. **サブスクリプション**を検索して選択します。

    ![[サブスクリプション] を選択する](media/cloudsimple-service-select-subscriptions.png)

3. CloudSimple サービスを有効にするサブスクリプションを選択します。
4. サブスクリプションの**リソースプロバイダー**をクリックします。
5. **Microsoft.VMwareCloudSimple** を使用してリソース プロバイダーをフィルタ処理します。
6. **Microsoft.VMwareCloudSimple**リソース プロバイダーを選択して**Register** (登録) をクリックします。

    ![リソース プロバイダーの登録](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。
2. **CloudSimple サービス**を検索します。

    ![CloudSimple サービスを検索します。](media/create-cloudsimple-service-search.png)

3. **CloudSimple サービス**を選択します。
4. 新しいサービスを作成するには、**Add** (追加) をクリックします。

    ![CloudSimple サービスを追加します。](media/create-cloudsimple-service-add.png)

5. CloudSimple サービスを作成するサブスクリプションを選択します。
6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. サービスを識別する名前を入力します。
8. サービス ゲートウェイの CIDR を入力します。 オンプレミス サブネット、オンプレミス サブネット、Azure サブネット、または計画上の CloudSimple サブネットと重複しないサブネットを/28 サブネットに指定します。 サービスを作成した後は、CIDR を変更できません。

    ![CloudSimple サービスの作成](media/create-cloudsimple-service.png)

9. Click **OK**.

サービスが作成され、サービスの一覧に追加されます。

## <a name="provision-nodes"></a>ノードをプロビジョニングする

CloudSimple プライベート クラウド環境に従量課金制の容量をセットアップするには、まず、Azure portal 内のノードをプロビジョニングします。

1. **[すべてのサービス]** を選択します。
2. **CloudSimple ノード** を検索します。

    ![CloudSimple ノードを検索します。](media/create-cloudsimple-node-search.png)

3. **CloudSimple ノード** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![CloudSimple ノードを追加します。](media/create-cloudsimple-node-add.png)

5. CloudSimple ノードをプロビジョニングするサブスクリプションを選択します。
6. ノードのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. ノードを識別するためにプレフィックスを入力します。
8. ノード リソースの場所を選択します。
9. ノード リソースをホスティングする専用の場所を選択します。
10. ノードの種類を選択します。 [CS28 または CS36 のオプション](cloudsimple-node.md)を選択できます。 後者のオプションには、コンピューティングとメモリの最大容量が含まれています。
11. プロビジョニングするノードの数を選択します。
12. **[確認および作成]** を選択します。
13. 設定を確認します。 設定を変更素ウルには、 **[前へ]** をクリックします。
14. **作成** を選択します。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成し、環境を構成します。](quickstart-create-private-cloud.md)
* [CloudSimple サービス](https://docs.azure.cloudsimple.com/cloudsimple-service)について説明します。
