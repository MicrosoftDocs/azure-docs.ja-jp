---
title: StorSimple Virtual Array のアクセス制御レコードの管理 | Microsoft Docs
description: ACR (アクセス制御レコード) を管理して、StorSimple Virtual Array 上のボリュームに接続できるホストを判別する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507621"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple デバイス マネージャーを使用して StorSimple Virtual Array のアクセス制御レコードを管理する

## <a name="overview"></a>概要

ACR (アクセス制御レコード) を使用すると、StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスとも呼ばれる) 上のボリュームに接続できるホストを指定できます。 ACR は特定のボリュームに設定され、ホストの iSCSI 修飾名 (IQN) を含みます。 ホストがボリュームに接続しようとすると、デバイスは、そのボリュームに関連付けられている ACR を確認し、該当する IQN 名を探します。一致するものがある場合は、接続が確立されます。 デバイス マネージャー サービスの **[構成]** セクション内の **[アクセス制御レコード]** ブレードには、すべてのアクセス制御レコードと、それに対応するホストの IQN が表示されます。

![アクセス制御レコードを管理する](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

このチュートリアルでは、次の一般的な ACR に関連するタスクについて説明します。

* IQN の取得
* アクセス制御レコードの追加
* アクセス制御レコードの編集
* アクセス制御レコードの削除

> [!IMPORTANT]
> 
> * ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。


## <a name="get-the-iqn"></a>IQN の取得

Windows Server 2012 を実行する Windows ホストの IQN を取得するには、次の手順を実行します。

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>ACR の追加

StorSimple デバイス マネージャー サービスの **[構成]** セクション内で **[アクセス制御レコード]** ブレードを使用して、ACR を追加します。 通常は、1 つの ACR を 1 つのボリュームに関連付けます。

ボリュームへの ACR の関連付けについては、 [ボリュームの追加](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)に関するセクションをご覧ください。

> [!IMPORTANT]
> ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。


次のステップを実行して、ACR を追加します。

#### <a name="to-add-an-acr"></a>ACR を追加するには

1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックします。次に、**[構成]** セクションで **[アクセス制御レコード]** をクリックします。
2. **[アクセス制御レコード]** ブレードで、**[追加]** をクリックします。
3. **[ACR の追加]** ブレードで、次の操作を行います。
   
    1. ACR の [**名前**] を入力します。
    
    2. **[iSCSI イニシエーターの名前]** で Windows ホストの IQN 名を指定します。 Windows Server ホストの IQN を取得するには、次のようにします。
   
    3. Windows ホストで、Microsoft iSCSI イニシエーターを起動します。 [iSCSI イニシエーターのプロパティ] ウィンドウの **[構成]** タブで、 **[イニシエーター名]** フィールドの文字列を選択してコピーします。
    この文字列を、**[ACR の追加]** ブレードの **[IQN]** フィールドに貼り付けます。
   
    6. **[追加]** をクリックして ACR を追加します。  
   
        ![アクセス制御レコードの追加](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. 表形式の一覧が更新され、この追加が反映されます。

## <a name="edit-an-acr"></a>ACR の編集

Azure Portal のデバイス マネージャー サービスの **[構成]** セクション内で **[アクセス制御レコード]** ブレードを使用して、ACR を編集します。

> [!NOTE]
> 現在使用されている ACR は変更しないでください。 現在使用されているボリュームに関連付けられている ACR を編集するには、まず、ボリュームをオフラインにします。


次のステップを実行して、ACR を編集します。

#### <a name="to-edit-an-acr"></a>ACR を編集するには

1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックします。次に、**[構成]** セクションで **[アクセス制御レコード]** をクリックします。
2. **[アクセス制御レコード]** ブレードに示されているアクセス制御レコードの表形式の一覧で、変更する ACR をダブルクリックします。
3. **[アクセス制御レコードの編集]** ブレードで、次の操作を行います。
   
    1. ACR の IQN を指定します。
   
    2. ブレードの上部にある **[保存]** をクリックして、変更された ACR を保存します。 次の確認メッセージが表示されます。
   
        ![アクセス制御レコードの編集](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>アクセス制御レコードの削除

Azure Portal の **[構成]** ページを使用して、ACR を削除します。

> [!NOTE]
> 
> * 現在使用されている ACR は削除しないでください。 現在使用されているボリュームに関連付けられている ACR を削除するには、まず、ボリュームをオフラインにします。
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。


次のステップを実行して、アクセス制御レコードを削除します。

#### <a name="to-delete-an-access-control-record"></a>アクセス制御レコードを削除するには

1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックします。次に、**[構成]** セクションで **[アクセス制御レコード]** をクリックします。

2. **[アクセス制御レコード]** ブレードに示されているアクセス制御レコードの表形式の一覧で、削除する ACR をダブルクリックします。

3. [アクセス制御レコードの編集] ブレードで、**[削除]** をクリックします。
   
    ![ACR の削除](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. 確認を求めるメッセージが表示されたら、**[削除]** をクリックして削除を続行します。 表形式の一覧が更新され、削除が反映されます。
   
   ![警告メッセージ](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>次のステップ

* [ボリュームの追加と ACR の構成](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)に関する詳細を確認します。

