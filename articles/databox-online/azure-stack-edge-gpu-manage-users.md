---
title: Azure Stack Edge Pro GPU のユーザー管理 | Microsoft Docs
description: Azure portal を使用して Azure Stack Edge Pro GPU のユーザーを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 5aeb4f2d7152120ea92a8df76f980eb3baed6e50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637263"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Azure portal を使用して Azure Stack Edge Pro のユーザーを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro のユーザーを管理する方法について説明します。 Azure Stack Edge Pro の管理は、Azure portal またはローカル Web UI を通じて行うことができます。 Azure portal を使用してユーザーを追加、変更、または削除します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ユーザーの追加
> * ユーザーの変更
> * ユーザーの削除

## <a name="about-users"></a>ユーザーについて

ユーザーには、読み取りアクセス権だけを指定することも、すべての特権を指定することもできます。 読み取り専用ユーザーは、共有データの表示だけが可能です。 すべての特権を持つユーザーは、共有データの読み取り、これらの共有への書き込み、共有データの変更または削除を行うことができます

 - **すべての特権を持つユーザー** - フル アクセス権を持つローカル ユーザー。
 - **読み取り専用ユーザー** - 読み取り専用アクセス権を持つローカル ユーザー。 これらのユーザーは、読み取り専用の操作を許可する共有に関連付けられます。

ユーザーのアクセス許可は、まず、共有の作成中にユーザーを作成するときに定義します。 これらはエクスプローラーを使用して変更できます。


## <a name="add-a-user"></a>ユーザーの追加

ユーザーを追加するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[ユーザー]** に移動します。 コマンド バーの **[+ ユーザーの追加]** を選択します。

    ![[ユーザーの追加] の選択](media/azure-stack-edge-gpu-manage-users/add-user-1.png)

2. 追加するユーザーのユーザー名とパスワードを指定します。 パスワードを確認し、 **[追加]** を選択します。

    ![ユーザー名とパスワードの指定](media/azure-stack-edge-gpu-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 以下のユーザーはシステムで予約されているため、使用できません。Administrator、EdgeUser、EdgeSupport、HcsSetupUser、WDAGUtilityAccount、CLIUSR、DefaultAccount、Guest。  

3. ユーザーの作成が開始され完了すると、通知が表示されます。 ユーザーが作成されたら、コマンド バーの **[最新の情報に更新]** を選択して、更新されたユーザーの一覧を表示します。


## <a name="modify-user"></a>ユーザーの変更

ユーザーの作成後、ユーザーに関連付けられているパスワードを変更することができます。 ユーザーの一覧から選択します。 新しいパスワードを入力して確認します。 変更を保存します。

![ユーザーの変更](media/azure-stack-edge-gpu-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>ユーザーの削除

ユーザーを削除するには、Azure portal で次の手順を実行します。


1. Azure portal で Azure Stack Edge リソースに移動し、 **[ユーザー]** に移動します。

    ![削除するユーザーの選択](media/azure-stack-edge-gpu-manage-users/delete-user-1.png)

2. ユーザーの一覧でユーザーを選択し、 **[削除]** を選択します。 メッセージが表示されたら、削除を確定します。

    ![削除するユーザーの選択 2](media/azure-stack-edge-gpu-manage-users/delete-user-2.png)

ユーザーの一覧が更新され、ユーザーの削除が反映されます。

![更新されたユーザーの一覧](media/azure-stack-edge-gpu-manage-users/delete-user-4.png)

## <a name="next-steps"></a>次のステップ

- [帯域幅を管理する](azure-stack-edge-gpu-manage-bandwidth-schedules.md)方法について学習します。
