---
title: CloudSimple プライベート クラウドによる Azure VMware ソリューションの縮小
description: CloudSimple プライベート クラウドを縮小する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544689"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドの縮小

CloudSimple は、プライベート クラウドを動的に縮小する柔軟性を提供します。  プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 プライベート クラウドを縮小する場合、既存のクラスターからノードを削除するか、クラスター全体を削除します。 

## <a name="before-you-begin"></a>開始する前に

プライベート クラウドを縮小するには、次の条件を満たす必要があります。  プライベート クラウドの作成時に作成された管理クラスター (最初のクラスター) は削除できません。

* vSphere クラスターには 3 つのノードが必要です。  ノードが 3 つしかないクラスターは縮小できません。
* 使用されているストレージの合計が、クラスターの縮小後の合計容量を超えないようにする必要があります。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="shrink-a-private-cloud"></a>プライベート クラウドを縮小する 

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[リソース]** ページを開きます。

3. 縮小するプライベート クラウドをクリックします。

4. 概要ページで **[縮小]** をクリックします。

    ![プライベート クラウドの縮小](media/shrink-private-cloud.png)

5. 縮小または削除するクラスターを選択します。 

    ![プライベート クラウドの縮小 - クラスターの選択](media/shrink-private-cloud-select-cluster.png)

6. **[Remove one node]\(1 つのノードを削除する\)** または **[Delete the whole cluster]\(クラスター全体を削除する\)** を選択します。 

7. クラスターの容量を確認します。

8. **[送信]** をクリックしてプライベート クラウドを縮小します。

プライベート クラウドの縮小が開始されます。  タスクの進行状況を監視できます。  vSAN で再同期する必要があるデータによっては、縮小プロセスに数時間かかることがあります。

## <a name="next-steps"></a>次の手順

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する