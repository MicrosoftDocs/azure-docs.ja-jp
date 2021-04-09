---
title: チュートリアル - Azure VMware Solution のプライベート クラウドを削除する
description: 不要になった Azure VMware Solution のプライベート クラウドを削除する方法について説明します。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462101"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution のプライベート クラウドを削除する

Azure VMware Solution のプライベート クラウドが不要になった場合は、削除できます。 プライベート クラウドには、分離されたネットワーク ドメイン、専用サーバー ホスト上の 1 つ以上のプロビジョニング済み vSphere クラスター、いくつかの仮想マシン (VM) が含まれます。 プライベート クラウドを削除すると、VM、そのデータ、クラスターがすべて削除されます。 Azure VMware Solution 専用ホストは安全にワイプされ、空きプールに返されます。 プロビジョニングされたネットワーク アドレス空間も削除されます。  

> [!CAUTION]
> プライベート クラウドを削除する操作は、元に戻すことができません。 プライベート クラウドを削除すると、実行中のすべてのワークロードとコンポーネントが中止され、パブリック IP アドレスを含むすべてのプライベート クラウド データと構成設定が破棄されるので、データを復旧することはできなくなります。

## <a name="prerequisites"></a>前提条件

VM とそのデータが後で必要な場合は、プライベート クラウドを削除する前に、必ずデータをバックアップしてください。  VM とそのデータを復旧する方法はありません。


## <a name="delete-the-private-cloud"></a>プライベート クラウドを削除する

1. [Azure portal](https://portal.azure.com) で、Azure VMware Solutions コンソールにアクセスします。

2. 削除するプライベート クラウドを選択します。
 
3. プライベート クラウドの名前を入力し、 **[はい]** を選択します。 

>[!NOTE]
>削除プロセスは数時間で完了します。  
