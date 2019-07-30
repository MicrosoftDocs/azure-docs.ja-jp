---
title: CloudSimple プライベート クラウドによる Azure VMware ソリューションの作成
description: CloudSimple プライベート クラウドを作成し、VMware のワークロードを運用上の柔軟性と継続性とともにクラウドに拡張する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332159"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドを作成する

プライベート クラウドを作成すると、ネットワーク インフラストラクチャに関するさまざまな一般的ニーズに対応できます。

* **拡大**。 既存のインフラストラクチャのハードウェア更新ポイントに到達した場合、プライベート クラウドを使用することで、新たなハードウェアへの投資を必要とせずに拡張することができます。

* **高速拡張**。 一時的または予定外の容量が必要になった場合、プライベート クラウドを使用することで、遅延なしで追加の容量を作成できます。

* **保護の強化**。 3 つ以上のノードで構成されるプライベート クラウドを使用すると、自動の冗長性と高可用性の保護が実現します。

* **長期的なインフラストラクチャのニーズ** データセンターがその処理能力に達した場合、またはコストを削減するために再構築が必要な場合、プライベート クラウドを使用することで、エンタープライズ運用との互換性を維持しつつ、データセンターを廃止し、クラウド ベースのソリューションに移行できます。

プライベート クラウドを作成すると、単一の vSphere クラスターと、そのクラスターで作成されるすべての管理 VM を使用できます。

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを作成する前に、ノードをプロビジョニングする必要があります。  ノードのプロビジョニングの詳細については、[Azure での CloudSimple による VMware ソリューション用ノードのプロビジョニング](create-nodes.md)に関する記事を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

[CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

## <a name="create-a-new-private-cloud"></a>新しいプライベート クラウドを作成する

1. **[リソース]** ページで、 **[New Private Cloud]\(新しいプライベート クラウド\)** をクリックします。

    ![プライベート クラウドを作成する - 開始方法](media/create-pc-button.png)

2. プライベート クラウドのリソースをホストする場所を選択します。

3. プライベート クラウド用にプロビジョニングした CS28 または CS36 ノードの種類を選択します。 後者のオプションには、コンピューティングとメモリの最大容量が含まれています。

4. プライベート クラウドのノードの数を選択します。 最大で、[プロビジョニング済みの](create-nodes.md)使用可能ノード数を選択できます。

    ![プライベート クラウドを作成する - 基本設定](media/create-private-cloud-basic-info.png)

5. **[次へ: 詳細オプション]** をクリックします。

6. vSphere/vSAN サブネットの CIDR 範囲を入力します。 CIDR 範囲がオンプレミスのサブネット、他の Azure サブネット (仮想ネットワーク)、またはゲートウェイのサブネットのいずれとも重複していないことを確認します。  Azure 仮想ネットワークで定義されている CIDR 範囲は使用しないでください。
    
    **CIDR 範囲オプション:** /24、/23、/22、または/21。 /24 CIDR 範囲は最大で 9 つのノードをサポートし、/23 CIDR 範囲は最大で 41 のノードをサポートし、/22 および /21 CIDR 範囲は最大で 64 のノード (プライベート クラウドのノードの最大数) をサポートします。

    > [!CAUTION]
    > vSphere/vSAN CIDR 範囲の IP アドレスは、プライベート クラウド インフラストラクチャで使用するために予約されています。  どの仮想マシンでもこの範囲の IP アドレスは使用しないでください。

7. **[次へ: 確認と作成]** を選択します。

8. 設定を確認します。 設定を変更する必要がある場合は、 **[前へ]** をクリックします。

9. **Create** をクリックしてください。

[作成] をクリックすると、プライベート クラウドのプロビジョニングが開始されます。  CloudSimple ポータルの [[タスク]](https://docs.azure.cloudsimple.com/activity/#tasks) ページから進行状況を監視できます。  プロビジョニングには 30 分から 2 時間かかります。  プロビジョニングが完了すると、電子メールが届きます。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを拡張する](expand-private-cloud.md)