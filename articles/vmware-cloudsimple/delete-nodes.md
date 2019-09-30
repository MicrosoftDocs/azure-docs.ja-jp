---
title: CloudSimple による VMware ソリューションのノードの削除 - Azure
description: CloudSimple デプロイで VMware からノードを削除する方法について学習します
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972829"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>CloudSimple による Azure VMware ソリューションからのノードの削除

CloudSimple ノードは、作成されると従量制で課金されます。  ノードの従量制課金を停止するには、ノードを削除する必要があります。  Azure portal から、使用されていないノードを削除します。

## <a name="before-you-begin"></a>開始する前に

ノードは、次の条件下でのみ削除できます。

* ノードと共に作成されたプライベート クラウドが削除されている。  プライベート クラウドを削除するには、「[CloudSimple プライベート クラウドによる Azure VMware ソリューションの削除](delete-private-cloud.md)」を参照してください。
* プライベート クラウドを縮小することによって、ノードがプライベート クラウドから削除されている。  プライベート クラウドを縮小するには、「[CloudSimple プライベート クラウドによる Azure VMware ソリューションの縮小](shrink-private-cloud.md)」を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="delete-cloudsimple-node"></a>CloudSimple ノードの削除

1. **[すべてのサービス]** を選択します。

2. **CloudSimple ノード** を検索します。

   ![CloudSimple ノードを検索します。](media/create-cloudsimple-node-search.png)

3. **CloudSimple ノード** を選択します。

4. 削除するプライベート クラウドに属していないノードを選択します。  **[PRIVATE CLOUD NAME]\(プライベート クラウド名\)** 列に、ノードが属しているプライベート クラウド名が表示されます。  ノードがプライベート クラウドで使用されていない場合、値は空になります。 

    ![[CloudSimple Nodes]\(CloudSimple ノード\) を選択する](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 削除できるのは、プライベート クラウドの一部ではないノードだけです。

## <a name="next-steps"></a>次の手順

* [プライベート クラウド](cloudsimple-private-cloud.md)について説明します。
