---
title: "StorSimple Virtual Array のアクセス制御レコードの管理 | Microsoft Docs"
description: "ACR (アクセス制御レコード) を管理して、StorSimple Virtual Array 上のボリュームに接続できるホストを判別する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 11252938-5b97-4178-8c37-f58eaa3d00b1
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f45d8e2b848e930a790e7bf38c2b09fbdaaf2fab
ms.openlocfilehash: 30b6aedd9b4b7913f6862b34582f3ae96c9eae5f
ms.lasthandoff: 03/01/2017


---
# <a name="use-storsimple-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple Manager を使用して StorSimple Virtual Array のアクセス制御レコードを管理する

## <a name="overview"></a>概要
ACR (アクセス制御レコード) を使用すると、StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスとも呼ばれる) 上のボリュームに接続できるホストを指定できます。 ACR は特定のボリュームに設定され、ホストの iSCSI 修飾名 (IQN) を含みます。 ホストがボリュームに接続しようとすると、デバイスは、そのボリュームに関連付けられている ACR を確認し、該当する IQN 名を探します。一致するものがある場合は、接続が確立されます。 **[構成]** ページの**アクセス制御レコード** セクションには、すべてのアクセス制御レコードとそれに対応するホストの IQN が表示されます。

このチュートリアルでは、次の一般的な ACR に関連するタスクについて説明します。

* IQN の取得
* アクセス制御レコードの追加 
* アクセス制御レコードの編集 
* アクセス制御レコードの削除 

> [!IMPORTANT]
> * ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。 
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。
> 
> 

## <a name="get-the-iqn"></a>IQN の取得
Windows Server 2012 を実行する Windows ホストの IQN を取得するには、次の手順を実行します。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>ACR の追加
StorSimple Manager サービスの **[構成]** ページを使用して ACR を追加します。 通常は、1 つの ACR を&1; つのボリュームに関連付けます。

ボリュームへの ACR の関連付けについては、 [ボリュームの追加](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)に関するセクションをご覧ください。

> [!IMPORTANT]
> ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。
> 
> 

次のステップを実行して、ACR を追加します。

#### <a name="to-add-an-acr"></a>ACR を追加するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
   
    ![[構成] タブ](./media/storsimple-ova-manage-acrs/acr1.png)
2. **[アクセス制御レコード]** の表形式の一覧で、ACR の **[名前]** を指定します。
3. **[iSCSI イニシエーターの名前]**で Windows ホストの IQN 名を指定します。 
4. ページ下部の **[保存]** をクリックし、新規に作成した ACR を保存します。 次の確認メッセージが表示されます。
   
    ![確認メッセージ](./media/storsimple-ova-manage-acrs/acr2.png)
5. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-manage-acrs/check-icon.png)に関するセクションをご覧ください。 表形式の一覧が更新され、この追加が反映されます。

## <a name="edit-an-acr"></a>ACR の編集
ACR を編集するには、Azure クラシック ポータルの **[構成]** ページを使用します。 

> [!NOTE]
> 編集できるのは、現在使用されていない ACR のみです。 現在使用されているボリュームに関連付けられている ACR を編集するには、まず、ボリュームをオフラインにします。
> 
> 

次のステップを実行して、ACR を編集します。

#### <a name="to-edit-an-acr"></a>ACR を編集するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
2. アクセス制御レコードの表形式の一覧で、変更する ACR にマウス ポインターを置きます。
3. ACR の新しい名前や IQN を指定します。
4. ページ下部の **[保存]** をクリックし、変更した ACR を保存します。 確認メッセージが表示されます。 
5. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-manage-acrs/check-icon.png)に関するセクションをご覧ください。 表形式の一覧が更新され、この変更が反映されます。

## <a name="delete-an-access-control-record"></a>アクセス制御レコードの削除
ACR を削除するには、Azure クラシック ポータルの **[構成]** ページを使用します。 

> [!NOTE]
> * 削除できるのは、現在使用されていない ACR のみです。 現在使用されているボリュームに関連付けられている ACR を削除するには、まず、ボリュームをオフラインにします。
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。
> 
> 

次のステップを実行して、アクセス制御レコードを削除します。

#### <a name="to-delete-an-access-control-record"></a>アクセス制御レコードを削除するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
2. ACR (アクセス制御レコード) の表形式の一覧で、削除する ACR にマウス ポインターを置きます。
3. 対象の ACR の右端の列に削除アイコン (**[x]**) が表示されます。 **[x]** アイコンをクリックして、ACR を削除します。 次の確認メッセージが表示されます。
   
    ![確認メッセージ](./media/storsimple-ova-manage-acrs/acr3.png)
4. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-manage-acrs/check-icon.png)に関するセクションをご覧ください。 表形式の一覧が更新され、削除が反映されます。

## <a name="next-steps"></a>次のステップ
* [ボリュームの追加と ACR の構成](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)に関する詳細を確認します。


