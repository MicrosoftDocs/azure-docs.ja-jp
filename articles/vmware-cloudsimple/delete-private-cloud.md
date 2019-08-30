---
title: CloudSimple プライベート クラウドによる Azure VMware ソリューションの削除
description: CloudSimple プライベート クラウドを削除する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972807"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドの削除

CloudSimple は、プライベート クラウドを削除する柔軟性を提供します。  プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 プライベート クラウドを削除すると、すべてのクラスターが削除されます。

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを削除すると、プライベート クラウド全体が削除されます。  プライベート クラウドのすべてのコンポーネントが削除されます。  データを保持する場合は、オンプレミスのストレージまたは Azure ストレージにデータをバックアップしていることを確認してください。

プライベート クラウドのコンポーネントには、次のものがあります。

* CloudSimple ノード
* 仮想マシン
* VLAN またはサブネット
* プライベート クラウドに格納されているすべてのユーザー データ
* VLAN またはサブネットへのすべてのファイアウォール規則の添付

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="delete-a-private-cloud"></a>プライベート クラウドの削除

1. [CloudSimple ポータルにアクセスします](access-cloudsimple-portal.md)。

2. **[リソース]** ページを開きます。

3. 削除するプライベート クラウドをクリックします

4. [概要] ページで **[削除]** をクリックします。

    ![プライベート クラウドを削除する](media/delete-private-cloud.png)

5. 確認ページで、プライベート クラウドの名前を入力し、 **[削除]** をクリックします。 

    ![プライベート クラウドを削除する - 確認](media/delete-private-cloud-confirm.png)

プライベート クラウドが削除対象としてマークされます。  削除プロセスが 3 時間後に開始され、プライベート クラウドが削除されます。

> [!CAUTION]
> プライベート クラウドを削除した後にノードを削除する必要があります。  ノードの従量制課金は、サブスクリプションからノードが削除されるまで続行されます。

## <a name="next-steps"></a>次の手順

* [ノードを削除する](delete-nodes.md)
