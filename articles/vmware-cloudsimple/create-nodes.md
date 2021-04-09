---
title: VMware Solution by CloudSimple 用のノードをプロビジョニングする - Azure
description: CloudSimple のデプロイと共に、Azure portal でノードを VMWare に追加する方法について説明します。 プライベート クラウド環境には、従量課金の容量を設定することができます。
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140736"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple 用のノードをプロビジョニングする

Azure portal における VM のプロビジョニング 次に、CloudSimple プライベート クラウド環境に従量課金の容量をセットアップできます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドにノードを追加する

1. **[すべてのサービス]** を選択します。
2. **CloudSimple ノード** を検索します。

   ![CloudSimple ノードを検索します。](media/create-cloudsimple-node-search.png)

3. **CloudSimple ノード** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![CloudSimple ノードを追加します。](media/create-cloudsimple-node-add.png)

5. CloudSimple ノードをプロビジョニングするサブスクリプションを選択します。
6. ノードのリソース グループを選択します。 リソース グループを新規に追加するには、**[Create New]** (新規作成) をクリックします。
7. ノードを識別するためにプレフィックスを入力します。
8. ノード リソースの場所を選択します。
9. ノード リソースをホスティングする専用の場所を選択します。
10. [ノードの種類](cloudsimple-node.md)を選択します。
11. プロビジョニングするノードの数を選択します。
12. **[確認および作成]** を選択します。
13. 設定を確認します。 設定を変更素ウルには、[**前へ**] をクリックします。
14. **［作成］** を選択します

## <a name="next-steps"></a>次のステップ

* [プライベート クラウドの作成](create-private-cloud.md)
