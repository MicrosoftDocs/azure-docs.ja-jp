---
title: CloudSimple プライベート クラウドによる Azure VMware ソリューションの拡張
description: 既存または新規のクラスターに容量を追加するために、既存の CloudSimple プライベート クラウドを拡張する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332288"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドの拡張

CloudSimple は、プライベート クラウドを動的に拡張する柔軟性を提供します。 小規模な構成から出発し、より大きな容量が必要になったら拡張することができます。 または、現在のニーズに基づいてプライベート クラウドを作成し、利用の増加に合わせて拡張することができます。

プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。  プライベート クラウドを拡張するときは、既存のクラスターにノードを追加するか、新しいクラスターを作成します。 既存のクラスターを拡張するには、追加するノードが既存のノードと同じ種類 (SKU) である必要があります。 新しいクラスターを作成する場合は、ノードの種類が違っていても構いません。 プライベート クラウドの制限の詳細については、「[CloudSimple プライベート クラウドの概要](cloudsimple-private-cloud.md)」の制限に関する節を参照してください。

プライベート クラウドは、vCenter 上の既定の**データセンター**を使用して作成されます。  各データセンターは、最上位レベルの管理エンティティとして機能します。  新しいクラスターの場合、CloudSimple は、既存のデータセンターに追加するか、新しいデータセンターを作成するかの選択肢を提供します。

新規クラスター構成の一環として、CloudSimple は VMware インフラストラクチャを構成します。  設定には、vSAN ディスク グループ用のストレージ設定、VMware High Availability、および Distributed Resource Scheduler (DRS) が含まれます。

プライベート クラウドは複数回拡張できます。 拡張は、全体的なノードの制限を超えない場合にのみ実行できます。 プライベート クラウドを拡張するたびに、既存のクラスターに追加するか、新しいクラスターを作成します。

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを拡張する前に、ノードをプロビジョニングする必要があります。  ノードのプロビジョニングの詳細については、[Azure での CloudSimple による VMware ソリューション用ノードのプロビジョニング](create-nodes.md)に関する記事を参照してください。  新しいクラスターを作成するには、同じ SKU の使用可能なノードが少なくとも 3 つ必要です。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="expand-a-private-cloud"></a>プライベート クラウドを拡張する

1. [CloudSimple ポータルにアクセスします](access-cloudsimple-portal.md)。

2. **[リソース]** ページを開き、拡張するプライベート クラウドを選択します。

3. 概要セクションで、 **[Expand]\(拡張\)** をクリックします。

    ![プライベート クラウドを拡張する](media/resources-expand-private-cloud.png)

4. 既存のクラスターを拡張するか、新しい vSphere クラスターを作成するか選択します。 変更を加えると、ページ上の要約情報が更新されます。

    * 既存のクラスターを拡張するには、 **[Expand existing cluster]\(既存のクラスターの拡張\)** を選択します。 拡張するクラスターを選択し、追加するノードの数を入力します。 各クラスターは、最大 16 個のノードを持つことができます。
    * 新しいクラスターを追加するには、 **[新しいクラスターの作成]** をクリックします。 クラスターの名前を入力します。 既存のデータセンターを選択するか、名前を入力して新しいデータセンターを作成します。 ノードの種類を選択します。 新しい vSphere クラスターを作成するときは異なるノードの種類を選択できますが、既存の vSphere クラスターを拡張するときは選択できません。 ノードの数を選択します。 新しいクラスターのそれぞれに、少なくとも 3 つのノードが必要です。

    ![プライベート クラウドの拡張 - ノードの追加](media/resources-expand-private-cloud-add-nodes.png)

5. **[送信]** をクリックしてプライベート クラウドを拡張します。

## <a name="next-steps"></a>次の手順

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する