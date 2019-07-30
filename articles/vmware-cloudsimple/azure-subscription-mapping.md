---
title: CloudSimple によって Azure サブスクリプションを Azure VMware ソリューションのリソース プールにマップする
description: CloudSimple によって Azure VMware ソリューションのリソース プールを Azure サブスクリプションにマップする方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332215"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>プライベート クラウドのリソース プールを Azure サブスクリプションにマップする

Azure サブスクリプションのマッピングにより、プライベート クラウドの vCenter のリソース プールを Azure サブスクリプションにマップできます。 CloudSimple サービスを作成したサブスクリプションのみをマップできます。  Azure portal から VMware 仮想マシンを作成することにより、マップ済みリソース プールに仮想マシンをデプロイします。  CloudSimple ポータルで、プライベート クラウドの Azure サブスクリプションを表示および管理できます。

1 つのサブスクリプションを、プライベート クラウドの複数の vCenter リソース プールにマップできます。  各プライベート クラウドのリソース プールをマップする必要があります。  Azure portal から VMware 仮想マシンを作成するために使用できるのは、マップ済みのリソース プールのみです。

> [!IMPORTANT]
> リソース プールをマップすると、子リソース プールもすべてマップされます。 子リソース プールが既にマップされている場合、親リソース プールはマップできません。

## <a name="before-you-begin"></a>開始する前に

この記事は、サブスクリプションに CloudSimple サービスとプライベート クラウドがあることを前提としています。  CloudSimple サービスを作成する方法については、「[クイック スタート - サービスの作成](quickstart-create-cloudsimple-service.md)」をご覧ください。  プライベート クラウドを作成する必要がある場合は、「[クイック スタート - プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)」をご覧ください。

vCenter クラスター (ルート リソース プール) をサブスクリプションにマップできます。  新しいリソース プールを作成する場合は、VMware ドキュメント サイトの[リソース プールの作成](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html)に関する記事をご覧ください。

## <a name="default-resource-group"></a>既定のリソース グループ

Azure portal から新しい CloudSimple 仮想マシンを作成すると、リソース グループを選択できます。  マップ済みリソース プール内のプライベート クラウド vCenter で作成された仮想マシンは Azure portal に表示されます。  検出された仮想マシンは、既定の Azure リソース グループに配置されます。  既定のリソース グループの名前は変更できます。

## <a name="map-azure-subscription"></a>Azure サブスクリプションをマップする

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[リソース]** ページを開き、マップするプライベート クラウドを選択します。

3. **[Azure subscriptions mapping]\(Azure サブスクリプション マッピング\)** を選択します。

4. **[Edit Azure subscription mapping]\(Azure サブスクリプション マッピングの編集\)** をクリックします。

5. 使用可能なリソース プールをマップするには、左側でそれらを選択し、右向きの矢印をクリックします。

6. マッピングを削除するには、右側でそれらを選択し、左向きの矢印をクリックします。

    ![Azure サブスクリプション](media/resources-azure-mapping.png)

7. Click **OK**.

## <a name="change-default-resource-group-name"></a>既定のリソース グループ名を変更する

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[リソース]** ページを開き、マップするプライベート クラウドを選択します。

3. **[Azure subscriptions mapping]\(Azure サブスクリプション マッピング\)** を選択します。

4. [Azure resource group name]\(Azure リソース グループ名\) の下の **[編集]** をクリックします。

    ![リソース グループ名を編集する](media/resources-edit-resource-group-name.png)

5. リソース グループの新しい名前を入力し、 **[送信]** をクリックします。

    ![新しいリソース グループ名を入力する](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>次の手順

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [CloudSimple 仮想マシン](cloudsimple-virtual-machines.md)の詳細を確認する