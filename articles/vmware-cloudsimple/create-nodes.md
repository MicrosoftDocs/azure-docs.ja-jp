---
title: CloudSimple による VMware ソリューション用のノードをプロビジョニングする - Azure
description: CloudSimple 展開時に VMWare にノードを追加する方法について説明します
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5806198968d98fea4c5cbf8731358ca4041f0935
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972883"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>CloudSimple による Azure VMware ソリューション用のノードをプロビジョニングする

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

* [プライベート クラウドの作成](create-private-cloud.md)
