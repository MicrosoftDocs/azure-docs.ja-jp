---
title: Azure VMware Solution by CloudSimple - CloudSimple プライベート クラウドの作成
description: CloudSimple プライベート クラウドを作成し、VMware のワークロードを運用上の柔軟性と継続性とともにクラウドに拡張する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aacdb57c312946a9ec2b17a8d41aa9150efc277d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640969"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドを作成する

プライベート クラウドは、ESXi ホスト、vCenter、vSAN、NSX をサポートする分離 VMware スタックです。 プライベート クラウドは CloudSimple ポータル経由で管理されます。 独自の管理ドメインに独自の vCenter サーバーがあります。 スタックは専用ノードと分離ベア メタル ハードウェア ノードで実行されます。

プライベート クラウドを作成すると、ネットワーク インフラストラクチャに関するさまざまな一般的ニーズに対応できます。

* **拡大**。 既存のインフラストラクチャのハードウェア更新ポイントに到達した場合、プライベート クラウドを使用することで、新たなハードウェアへの投資を必要とせずに拡張することができます。

* **高速拡張**。 一時的または予定外の容量が必要になった場合、プライベート クラウドを使用することで、遅延なしで追加の容量を作成できます。

* **保護の強化**。 3 つ以上のノードで構成されるプライベート クラウドを使用すると、自動の冗長性と高可用性の保護が実現します。

* **長期的なインフラストラクチャのニーズ** データセンターがその処理能力に達した場合、またはコストを削減するために再構築が必要な場合、プライベート クラウドを使用することで、エンタープライズ運用との互換性を維持しつつ、データセンターを廃止し、クラウド ベースのソリューションに移行できます。

プライベート クラウドを作成すると、単一の vSphere クラスターと、そのクラスターで作成されるすべての管理 VM を使用できます。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

[CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

## <a name="create-a-new-private-cloud"></a>新しいプライベート クラウドを作成する

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
3. プライベート クラウドを作成する CloudSimple サービスを選択します。
4. **[概要]** から、 **[Create Private Cloud]\(プライベート クラウドの作成\)** をクリックして CloudSimple ポータルの新しいブラウザー タブを開きます。 メッセージが表示されたら、Azure サインインの資格情報でサインインします。

    ![Azure からプライベート クラウドを作成する](media/create-private-cloud-from-azure.png)

5. CloudSimple ポータルで、プライベート クラウドの名前を指定します。
6. プライベート クラウドの**場所**を選択します。
7. Azure で購入したものと一貫性のある**ノードの種類**を選択します。  [CS28 または CS36 のオプション](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku)を選択できます。 後者のオプションには、コンピューティングとメモリの最大容量が含まれています。
8. **ノード数**を指定します。  プライベート クラウドを作成するには、少なくとも 3 つのノードが必要です。

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
