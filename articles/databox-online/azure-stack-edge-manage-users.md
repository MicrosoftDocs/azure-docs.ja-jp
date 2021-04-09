---
title: Azure Stack Edge Pro FPGA のユーザー管理 | Microsoft Docs
description: Azure portal を使用して Azure Stack Edge Pro のユーザーを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 27ca190f3bad7f75175e5206d48e13dae1f5687e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913328"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro-fpga"></a>Azure portal を使用して Azure Stack Edge Pro FPGA のユーザーを管理する

この記事では、Azure Stack Edge Pro FPGA デバイスのユーザーを管理する方法について説明します。 Azure Stack Edge Pro の管理は、Azure portal またはローカル Web UI を通じて行えます。 Azure portal を使用してユーザーを追加、変更、または削除します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ユーザーの追加
> * ユーザーの変更
> * ユーザーの削除

## <a name="about-users"></a>ユーザーについて

ユーザーには、読み取りアクセス権だけを指定することも、すべての特権を指定することもできます。 名前が示すとおり、読み取り専用ユーザーは、共有データを表示することだけができます。 すべての特権を持つユーザーは、共有データの読み取り、これらの共有への書き込み、および共有データの変更または削除を行うことができます。

 - **すべての特権を持つユーザー** - フル アクセス権を持つローカル ユーザー。
 - **読み取り専用ユーザー** - 読み取り専用アクセス権を持つローカル ユーザー。 これらのユーザーは、読み取り専用の操作を許可する共有に関連付けられます。

ユーザーのアクセス許可は、まず、共有の作成中にユーザーを作成するときに定義します。 共有レベルのアクセス許可の変更は現在サポートされていません。

## <a name="add-a-user"></a>ユーザーの追加

ユーザーを追加するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[ユーザー]** に移動します。 コマンド バーの **[+ ユーザーの追加]** を選択します。

    ![[ユーザーの追加] の選択](media/azure-stack-edge-manage-users/add-user-1.png)

2. 追加するユーザーのユーザー名とパスワードを指定します。 パスワードを確認し、 **[追加]** を選択します。

    ![ユーザー名とパスワードの指定](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 以下のユーザーはシステムで予約されているため、使用できません。Administrator、EdgeUser、EdgeSupport、HcsSetupUser、WDAGUtilityAccount、CLIUSR、DefaultAccount、Guest。  

3. ユーザーの作成が開始され完了すると、通知が表示されます。 ユーザーが作成されたら、コマンド バーの **[最新の情報に更新]** を選択して、更新されたユーザーの一覧を表示します。


## <a name="modify-user"></a>ユーザーの変更

ユーザーの作成後、ユーザーに関連付けられているパスワードを変更することができます。 ユーザーの一覧から選択します。 新しいパスワードを入力して確認します。 変更を保存します。
 
![ユーザーの変更](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>ユーザーの削除

ユーザーを削除するには、Azure portal で次の手順を実行します。


1. Azure portal で Azure Stack Edge リソースに移動し、 **[ユーザー]** に移動します。

    ![削除するユーザーの選択](media/azure-stack-edge-manage-users/delete-user-1.png)

2. ユーザーの一覧でユーザーを選択し、 **[削除]** を選択します。  

   ![[削除] を選択する](media/azure-stack-edge-manage-users/delete-user-2.png)

3. メッセージが表示されたら、削除を確定します。 

   ![削除の確定](media/azure-stack-edge-manage-users/delete-user-3.png)

ユーザーの一覧が更新され、ユーザーの削除が反映されます。

![更新されたユーザーの一覧](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>次のステップ

- [帯域幅を管理する](azure-stack-edge-manage-bandwidth-schedules.md)方法について学習します。
