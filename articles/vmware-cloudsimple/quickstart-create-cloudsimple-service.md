---
title: CloudSimple による Azure VMware ソリューションのクイック スタート - サービスの作成
description: CloudSimple サービスの作成方法、ノードの購入方法、ノードの予約法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574547"
---
# <a name="quickstart---create-cloudsimple-service"></a>クイックスタート - CloudSimple サービスを作成する

始めに、Azure ポータルで CloudSimple によって Azure VMware ソリューションを作成します。

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。  このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。  CloudSimple サービスが使用可能な各 Azure 領域に、CloudSimple サービスを追加します。  このサービスでは、Azure VMware Solution by CloudSimple のエッジ ネットワークが定義されます。  このエッジ ネットワークは、VPN、ExpressRoute、およびプライベート クラウドへのインターネット接続を含むサービスに使用されます。

CloudSimple サービスを追加するには、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 これは、オンプレミス ネットワークのアドレス空間や Azure の仮想ネットワーク アドレス空間と重複できません。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Microsoft.VMwareCloudSimple リソース プロバイダーを有効にします。

CloudSimple サービスのリソース プロバイダーを有効にするには、次の手順に従います。

1. **[すべてのサービス]** を選択します。
2. **サブスクリプション**を検索して選択します。

    ![[サブスクリプション] を選択する](media/cloudsimple-service-select-subscriptions.png)

3. CloudSimple サービスを有効にするサブスクリプションを選択します。
4. サブスクリプションの **[リソース プロバイダー]** をクリックします。
5. **Microsoft.VMwareCloudSimple** を使用してリソース プロバイダーをフィルタ処理します。
6. **[Microsoft.VMwareCloudSimple]** リソース プロバイダーを選択し、 **[登録]** をクリックします。

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

## <a name="purchase-nodes"></a>ノードの購入

CloudSimple プライベート クラウド環境に従量課金制の容量をセットアップするには、まず、Azure portal 内のノードをプロビジョニングします。

1. **[すべてのサービス]** を選択します。
2. **CloudSimple ノード** を検索します。

    ![CloudSimple ノードを検索します。](media/create-cloudsimple-node-search.png)

3. **CloudSimple ノード** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![CloudSimple ノードを追加します。](media/create-cloudsimple-node-add.png)

5. CloudSimple ノードを購入するサブスクリプションを選択します。
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
* [CloudSimple サービス](cloudsimple-service.md)について説明します。
