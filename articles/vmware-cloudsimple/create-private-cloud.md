---
title: Azure VMware Solution by CloudSimple - CloudSimple プライベート クラウドの作成
description: CloudSimple プライベート クラウドを作成し、VMware のワークロードを運用上の柔軟性と継続性とともにクラウドに拡張する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896279"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドを作成する

プライベート クラウドは、ESXi ホスト、vCenter、vSAN、NSX をサポートする分離 VMware スタックです。 プライベート クラウドは CloudSimple ポータル経由で管理されます。 独自の管理ドメインに独自の vCenter サーバーがあります。 スタックは専用ノードと分離ベア メタル ハードウェア ノードで実行されます。

プライベート クラウドを作成すると、ネットワーク インフラストラクチャに関するさまざまな一般的ニーズに対応できます。

* **拡大**。 既存のインフラストラクチャのハードウェア更新ポイントに到達した場合、プライベート クラウドを使用することで、新たなハードウェアへの投資を必要とせずに拡張することができます。

* **高速拡張**。 一時的または予定外の容量が必要になった場合、プライベート クラウドを使用することで、遅延なしで追加の容量を作成できます。

* **保護の強化**。 3 つ以上のノードで構成されるプライベート クラウドを使用すると、自動の冗長性と高可用性の保護が実現します。

* **長期的なインフラストラクチャのニーズ** データセンターがその処理能力に達した場合、またはコストを削減するために再構築が必要な場合、プライベート クラウドを使用することで、エンタープライズ運用との互換性を維持しつつ、データセンターを廃止し、クラウド ベースのソリューションに移行できます。

プライベート クラウドを作成すると、単一の vSphere クラスターと、そのクラスターで作成されるすべての管理 VM を使用できます。

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを作成する前に、ノードをプロビジョニングする必要があります。 ノードのプロビジョニングの詳細については、「[Azure VMware Solution by CloudSimple 用のノードをプロビジョニングする](create-nodes.md)」を参照してください。

プライベート クラウドの vSphere/vSAN サブネットに対する CIDR 範囲を割り当てます。 プライベート クラウドは、vCenter サーバーによって管理される、分離された VMware スタック環境 (ESXi ホスト、vCenter、vSAN、および NSX) として作成されます。 管理コンポーネントは、vSphere/vSAN サブネットの CIDR に対して選択されたネットワーク内にデプロイされます。 ネットワーク CIDR の範囲は、デプロイの間に異なる複数のサブネットに分割されます。 vSphere/vSAN サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。 vSphere/vSAN サブネットの詳細については、「VLAN とサブネットの概要」を参照してください。

* vSphere/vSAN サブネットの CIDR 範囲の最小プレフィックス: /24
* vSphere/vSAN サブネットの CIDR 範囲の最大プレフィックス: /21


## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

[CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

## <a name="create-a-new-private-cloud"></a>新しいプライベート クラウドを作成する

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
3. プライベート クラウドを作成する CloudSimple サービスを選択します。
4. **[概要]** から、 **[Create Private Cloud]\(プライベート クラウドの作成\)** をクリックして CloudSimple ポータルの新しいブラウザー タブを開きます。 メッセージが表示されたら、Azure サインインの資格情報でサインインします。

    ![Azure からプライベート クラウドを作成する](media/create-private-cloud-from-azure.png)

5. CloudSimple ポータルで、プライベート クラウドの名前を指定します。
6. プライベート クラウドの **場所** を選択します。
7. Azure 上にプロビジョンしたものと一貫性のある **ノードの種類** を選択します。
8. **[ノード数]** を指定します。  プライベート クラウドを作成するには、少なくとも 3 つのノードが必要です。

    ![プライベート クラウドを作成する - 基本情報](media/create-private-cloud-basic-info.png)

9. **[次へ: 詳細オプション]** をクリックします。
10. vSphere/vSAN サブネットの CIDR 範囲を入力します。 CIDR 範囲がオンプレミスのサブネット、他の Azure サブネット (仮想ネットワーク)、またはゲートウェイのサブネットのいずれとも重複していないことを確認します。

    **CIDR 範囲オプション:** /24、/23、/22、または /21。 /24 CIDR 範囲は最大で 9 つのノードをサポートし、/23 CIDR 範囲は最大で 41 のノードをサポートし、/22 および /21 CIDR 範囲は最大で 64 のノード (プライベート クラウドのノードの最大数) をサポートします。

    > [!IMPORTANT]
    > vSphere/vSAN CIDR 範囲の IP アドレスは、プライベート クラウド インフラストラクチャで使用するために予約されています。  どの仮想マシンでもこの範囲の IP アドレスは使用しないでください。

11. **[次へ: 確認と作成]** を選択します。
12. 設定を確認します。 設定を変更する必要がある場合は、 **[前へ]** をクリックします。
13. **Create** をクリックしてください。

プライベート クラウドのプロビジョニング プロセスが開始されます。 プライベート クラウドのプロビジョニングには、最大で 2 時間かかる場合があります。

既存のプライベート クラウドを拡張する方法については、「[プライベート クラウドを拡張する](expand-private-cloud.md)」を参照してください。
