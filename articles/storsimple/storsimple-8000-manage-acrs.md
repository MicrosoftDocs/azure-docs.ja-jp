---
title: StorSimple でのアクセス制御レコードの管理 | Microsoft Docs
description: ACR (アクセス制御レコード) を使用して、StorSimple デバイス上のボリュームに接続できるホストを判別する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949906"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>StorSimple Manager サービスを使用してアクセス制御レコードを管理する

## <a name="overview"></a>概要
ACR (アクセス制御レコード) を使用すると、StorSimple デバイス上のボリュームに接続できるホストを指定できます。 ACR は特定のボリュームに設定され、ホストの iSCSI 修飾名 (IQN) を含みます。 ホストがボリュームに接続しようとすると、デバイスは、そのボリュームに関連付けられている ACR を確認し、該当する IQN 名を探します。一致するものがある場合は、接続が確立されます。 StorSimple デバイス マネージャー サービス ブレードの **[構成]** セクション内のアクセス制御レコードには、すべてのアクセス制御レコードと、それに対応するホストの IQN が表示されます。

このチュートリアルでは、次の一般的な ACR に関連するタスクについて説明します。

* アクセス制御レコードの追加
* アクセス制御レコードの編集
* アクセス制御レコードの削除

> [!IMPORTANT]
> * ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。

## <a name="get-the-iqn"></a>IQN の取得

Windows Server 2012 を実行する Windows ホストの IQN を取得するには、次の手順を実行します。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>アクセス制御レコードの追加
ACR を追加するには、StorSimple デバイス マネージャー サービス ブレードの **[構成]** セクションを使用します。 通常は、1 つの ACR を 1 つのボリュームに関連付けます。

次のステップを実行して、ACR を追加します。

#### <a name="to-add-an-acr"></a>ACR を追加するには

1. StorSimple デバイス マネージャー サービスに移動し、サービス名をダブルクリックした後、 **[構成]** セクションで **[アクセス制御レコード]** をクリックします。
2. **[アクセス制御レコード]** ブレードで、 **[ACR の追加]** をクリックします。

    ![[ACR の追加] をクリックする](./media/storsimple-8000-manage-acrs/createacr1.png)

3. **[ACR の追加]** ブレードで、次の手順を実行します。

    1. ACR の名前を入力します。
    
    2. **[iSCSI イニシエーター名 (IQN)]** で Windows Server ホストの IQN 名を指定します。

    3. **[追加]** をクリックして、ACR を作成します。

        ![[追加] のクリック ACR 2](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  新しく追加した ACR が、表形式の ACR の一覧に表示されます。

    ![[追加] のクリック ACR 3](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>アクセス制御レコードの編集
ACR を編集するには、StorSimple デバイス マネージャー サービス ブレードの **[構成]** セクションを使用します。

> [!NOTE]
> 現在使用されていない ACR のみを編集することをお勧めします。 現在使用されているボリュームに関連付けられている ACR を編集するには、まず、ボリュームをオフラインにします。

次のステップを実行して、ACR を編集します。

#### <a name="to-edit-an-access-control-record"></a>アクセス制御レコードを編集するには
1.  StorSimple デバイス マネージャー サービスに移動し、サービス名をダブルクリックした後、 **[構成]** セクションで **[アクセス制御レコード]** をクリックします。

    ![[アクセス制御レコード] に移動する](./media/storsimple-8000-manage-acrs/createacr1.png)

2. アクセス制御レコードの表形式の一覧で、変更する ACR をクリックして選択します。

    ![アクセス制御レコードの編集](./media/storsimple-8000-manage-acrs/editacr1.png)

3. **[アクセス制御レコードの編集]** ブレードで、もう一方のホストに対応する別の IQN を指定します。

    ![アクセス制御レコードの編集 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. **[保存]** をクリックします。 確認を求められたら、 **[はい]** をクリックします。 

    ![アクセス制御レコードの編集 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. ACR が更新されると通知されます。 また、表形式の一覧が更新され、変更が反映されます。

   
## <a name="delete-an-access-control-record"></a>アクセス制御レコードの削除
ACR を削除するには、StorSimple デバイス マネージャー サービス ブレードの **[構成]** セクションを使用します。

> [!NOTE]
> 削除できるのは、現在使用されていない ACR のみです。 現在使用されているボリュームに関連付けられている ACR を削除するには、まず、ボリュームをオフラインにします。

次のステップを実行して、アクセス制御レコードを削除します。

#### <a name="to-delete-an-access-control-record"></a>アクセス制御レコードを削除するには
1.  StorSimple デバイス マネージャー サービスに移動し、サービス名をダブルクリックした後、 **[構成]** セクションで **[アクセス制御レコード]** をクリックします。

    ![[アクセス制御レコード] に移動する 1b](./media/storsimple-8000-manage-acrs/createacr1.png)

2. アクセス制御レコードの表形式の一覧で、削除する ACR をクリックして選択します。

    ![[アクセス制御レコード] に移動する 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. 右クリックしてコンテキスト メニューを起動し、 **[削除]** を選択します。

    ![[アクセス制御レコード] に移動する 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. 確認を求められたら、情報を確認して **[削除]** をクリックします。

    ![[アクセス制御レコード] に移動する 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. 削除が完了すると通知されます。 表形式の一覧が更新され、削除が反映されます。

    ![[アクセス制御レコード] に移動する 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>次のステップ
* [StorSimple ボリュームの管理の詳細](storsimple-8000-manage-volumes-u2.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細を確認する。

