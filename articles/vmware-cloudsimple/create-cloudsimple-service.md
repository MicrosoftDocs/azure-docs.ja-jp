---
title: Azure VMware Solution by CloudSimple - CloudSimple サービスの作成
description: Azure portal で CloudSimple サービスを作成する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f589adf5cbcd04b9642dfe1362fd13d5be1f9aa
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640630"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Azure VMware Solution by CloudSimple サービスの作成

CloudSimple による Azure VMware ソリューションを開始するには、Azure portal で CloudSimple による Azure VMware ソリューション サービスを作成します。

> [!IMPORTANT]
> CloudSimple サービスを作成する前に、Azure サブスクリプションに Microsoft.VMwareCloudSimple リソース プロバイダーを登録する必要があります。 [Azure サブスクリプションで Microsoft.VMwareCloudSimple リソース プロバイダーを有効にする](enable-cloudsimple-service.md)方法の手順を実行します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](http://portal.azure.com) にサインインします。

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
    ![CloudSimple サービスを検索する](media/create-cloudsimple-service-search.png)
3. **CloudSimple サービス**を選択します。
4. 新しいサービスを作成するには、**Add** (追加) をクリックします。
    ![CloudSimple サービスを追加する](media/create-cloudsimple-service-add.png)
5. CloudSimple サービスを作成するサブスクリプションを選択します。
6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. サービスを識別する名前を入力します。
8. サービス ゲートウェイの CIDR を入力します。 オンプレミス サブネット、オンプレミス サブネット、Azure サブネット、計画上の CloudSimple サブネットのいずれとも重複しない /28 サブネットを指定します。 サービスを作成した後は、CIDR を変更できません。

    ![CloudSimple サービスの作成](media/create-cloudsimple-service.png)
9. Click **OK**.

サービスが作成され、サービスの一覧に追加されます。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成する](create-private-cloud.md)方法を学習する
* [プライベート クラウド環境を構成する](quickstart-create-private-cloud.md)方法を学習する
