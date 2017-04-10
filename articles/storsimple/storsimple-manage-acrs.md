---
title: "StorSimple でのアクセス制御レコードの管理 | Microsoft Docs"
description: "ACR (アクセス制御レコード) を使用して、StorSimple デバイス上のボリュームに接続できるホストを判別する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984


---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>StorSimple Manager サービスを使用してアクセス制御レコードを管理する
## <a name="overview"></a>Overview
ACR (アクセス制御レコード) を使用すると、StorSimple デバイス上のボリュームに接続できるホストを指定できます。 ACR は特定のボリュームに設定され、ホストの iSCSI 修飾名 (IQN) を含みます。 ホストがボリュームに接続しようとすると、デバイスは、そのボリュームに関連付けられている ACR を確認し、該当する IQN 名を探します。一致するものがある場合は、接続が確立されます。 **[構成]** ページのアクセス制御レコード セクションには、すべてのアクセス制御レコードとそれに対応するホストの IQN が表示されます。

このチュートリアルでは、次の一般的な ACR に関連するタスクについて説明します。

* アクセス制御レコードの追加 
* アクセス制御レコードの編集 
* アクセス制御レコードの削除 

> [!IMPORTANT]
> * ボリュームに ACR を割り当てる際は、そのボリュームが複数の非クラスター化ホストによって同時にアクセスされることのないように注意してください。ボリュームの破損を招く可能性があります。 
> * ボリュームから ACR を削除するときは、対応するホストがボリュームにアクセスしていないことを確認してください。読み取り/書き込みが、削除によって中断される可能性があります。
> 
> 

## <a name="add-an-access-control-record"></a>アクセス制御レコードの追加
StorSimple Manager サービスの **[構成]** ページを使用して ACR を追加します。 通常は、1 つの ACR を 1 つのボリュームに関連付けます。

次のステップを実行して、ACR を追加します。

#### <a name="to-add-an-access-control-record"></a>アクセス制御レコードを追加するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
2. **[アクセス制御レコード]** の表形式の一覧で、ACR の **[名前]** を指定します。
3. **[iSCSI イニシエーターの名前]**で Windows ホストの IQN 名を指定します。 Windows Server ホストの IQN を取得するには、次のようにします。
   
   * Windows ホストで、Microsoft iSCSI イニシエーターを起動します。
   * **[iSCSI イニシエーターのプロパティ]** ウィンドウの **[構成]** タブで、**[イニシエーター名]** フィールドの文字列を選択してコピーします。
   * この文字列を、Azure クラシック ポータルの ACR テーブルにある **[iSCSI イニシエーター名]** フィールドに貼り付けます。
4. **[保存]** をクリックして、新しく作成された ACR を保存します。 表形式の一覧が更新され、この追加が反映されます。

## <a name="edit-an-access-control-record"></a>アクセス制御レコードの編集
ACR を編集するには、Azure クラシック ポータルの **[構成]** ページを使用します。 

> [!NOTE]
> 編集できるのは、現在使用されていない ACR のみです。 現在使用されているボリュームに関連付けられている ACR を編集するには、まず、ボリュームをオフラインにします。
> 
> 

次のステップを実行して、ACR を編集します。

#### <a name="to-edit-an-access-control-record"></a>アクセス制御レコードを編集するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
2. アクセス制御レコードの表形式の一覧で、変更する ACR にマウス ポインターを置きます。
3. ACR の新しい名前や IQN を指定します。
4. **[保存]** をクリックして、変更した ACR を保存します。 表形式の一覧が更新され、この変更が反映されます。

## <a name="delete-an-access-control-record"></a>アクセス制御レコードの削除
ACR を削除するには、Azure クラシック ポータルの **[構成]** ページを使用します。 

> [!NOTE]
> 削除できるのは、現在使用されていない ACR のみです。 現在使用されているボリュームに関連付けられている ACR を削除するには、まず、ボリュームをオフラインにします。
> 
> 

次のステップを実行して、アクセス制御レコードを削除します。

#### <a name="to-delete-an-access-control-record"></a>アクセス制御レコードを削除するには
1. サービスのランディング ページで、サービスを選択し、サービス名をダブルクリックしてから **[構成]** タブをクリックします。
2. ACR (アクセス制御レコード) の表形式の一覧で、削除する ACR にマウス ポインターを置きます。
3. 対象の ACR の右端の列に削除アイコン (**[x]**) が表示されます。 **[x]** アイコンをクリックして、ACR を削除します。
4. 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして削除を続行します。 表形式の一覧が更新され、削除が反映されます。

## <a name="next-steps"></a>次のステップ
* [StorSimple ボリュームの管理の詳細](storsimple-manage-volumes.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理の詳細](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


