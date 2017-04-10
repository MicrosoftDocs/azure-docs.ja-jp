---
title: "StorSimple ストレージ アカウントの管理 | Microsoft Docs"
description: "StorSimple Manager の [構成] ページを使用して StorSimple Virtual Array に関連付けられたストレージ アカウントの追加、編集、削除、およびセキュリティ キーのローテーションを行う方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fb6459c7-a4b1-4b69-a000-72edd42e9478
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/29/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a2419943ab8591e397ad55bd400042436fdf0373


---
# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>StorSimple Manager サービスを使用した StorSimple Virtual Array のストレージ アカウントの管理
## <a name="overview"></a>概要
**[構成]** ページには、StorSimple Manager サービスで作成できるグローバル サービス パラメーターが表示されます。 これらのパラメーターはこのサービスに接続されているすべてのデバイスに適用でき、次のものが含まれます。

* ストレージ アカウント 
* アクセス制御レコード 

このチュートリアルでは、 **[構成]** ページを使用して StorSimple Virtual Array 用のストレージ アカウントを追加、編集、または削除する方法について説明します。 このチュートリアルの情報は、2016 年 3 月 GA リリースのソフトウェアを実行する StorSimple Virtual Array にのみ適用されます。

 ![[構成] ページ](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

ストレージ アカウントには、クラウド サービス プロバイダーを使用してストレージ アカウントにアクセスするためにデバイスで使用される資格情報が含まれます。 Microsoft Azure ストレージ アカウントの場合、これらはアカウント名やプライマリ アクセス キーなどの資格情報です。 

**[構成]** ページには、課金サブスクリプションに対して作成されるすべてのストレージ アカウントが、次の情報を含む表形式で表示されます。

* **[名前]** - アカウントの作成時に割り当てられた一意の名前。
* **[SSL が有効]** - SSL が有効で、デバイスとクラウドの間の通信がセキュア チャネル経由かどうか。

**[構成]** ページで実行できるストレージ アカウント関連の最も一般的なタスクは、次のとおりです。

* ストレージ アカウントの追加 
* ストレージ アカウントの編集 
* ストレージ アカウントの削除 

## <a name="types-of-storage-accounts"></a>ストレージ アカウントの種類
StorSimple デバイスで使用できるストレージ アカウントには、次の 3 つの種類があります。

* **自動生成されたストレージ アカウント** - 名前のとおり、このストレージ アカウントは、サービスが最初に作成されるときに自動的に生成されます。 このストレージ アカウントの作成方法の詳細については、「 [新しいサービスを作成する](storsimple-ova-manage-service.md#create-a-service)」を参照してください。 
* **サービス サブスクリプションのストレージ アカウント** - サービスと同じサブスクリプションに関連付けられている Azure ストレージ アカウントです。 このストレージ アカウントの作成方法の詳細については、 [Azure ストレージ アカウント](../storage/storage-create-storage-account.md)に関するページを参照してください。 
* **サービス サブスクリプションの外部のストレージ アカウント** - これらは、サービスに関連付けられていない、サービスが作成される前に存在していた可能性のある Azure ストレージ アカウントです。

各 StorSimple Virtual Array は、関連付けられたストレージ アカウントにコンテナーを作成します (プレフィックス hcs が付きます)。 このコンテナーには、デバイスのすべてのクラウド データが含まれます。 Azure Storage サービスからアクセスすることでこのコンテナーを削除しないでください。削除するとデータが失われます。

## <a name="add-a-storage-account"></a>ストレージ アカウントの追加
ストレージ アカウントを StorSimple Manager のサービス構成に追加するには、一意の表示名と、ストレージ アカウントにリンクされているアクセス資格情報を指定します。 SSL (Secure Sockets Layer) モードを有効にして、デバイスとクラウド間のネットワーク通信用にセキュリティで保護されたチャネルを作成することもできます。

特定のクラウド サービス プロバイダーに対して複数のアカウントを作成できます。 ストレージ アカウントの保存中、サービスは、クラウド サービス プロバイダーとの通信を試みます。 この時点で、資格情報と指定したアクセス マテリアルが認証されます。 ストレージ アカウントは、認証が成功した場合にのみ作成されます。 認証に失敗した場合は、適切なエラー メッセージが表示されます。

Azure ポータルで作成された Resource Manager のストレージ アカウントは StorSimple でもサポートされます。 Resource Manager のストレージ アカウントは、ドロップダウン リストに選択対象として表示されません。Azure クラシック ポータルで作成されたストレージ アカウントだけが表示されます。 次に説明するストレージ アカウントの追加手順に従って、Resource Manager のストレージ アカウントを追加する必要があります。

Azure クラシック ストレージ アカウントを追加する手順の詳細を次に示します。

[!INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>ストレージ アカウントの編集
デバイスで使用されるストレージ アカウントを編集できます。 現在使用中のストレージ アカウントを編集する場合、変更できるフィールドは、ストレージ アカウントのアクセス キーと SSL モードです。 新しいストレージ アクセス キーを指定したり、 **[SSL モードを有効にする]** の選択を変更したりした後、更新した設定を保存します。

#### <a name="to-edit-a-storage-account"></a>ストレージ アカウントを編集するには
1. サービスのランディング ページでサービスを選択し、サービス名をダブルクリックします。**[構成]** をクリックします。
2. **[ストレージ アカウントの追加/編集]**をクリックします。
3. **[ストレージ アカウントの追加/編集]** ダイアログ ボックスで、次の操作を行います。
   
   1. **[ストレージ アカウント]**ボックスの一覧で、変更する既存のアカウントを選択します。 
   2. 必要に応じて、 **[SSL モードを有効にする]** の設定を変更できます。
   3. ストレージ アカウント アクセス キーを再生成できます。 詳細については、「[ストレージ アカウント キーの再生成](../storage/storage-create-storage-account.md#manage-your-storage-access-keys)」をご覧ください。 新しいストレージ アカウント キーを指定します。 Azure ストレージ アカウントの場合、これはプライマリ アクセス キーです。 
   4. チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-manage-storage-accounts/checkicon.png) をクリックして、設定を保存します。 **[構成]** ページの設定が更新されます。 
   5. ページ下部の **[保存]** をクリックして、新しく更新した設定を保存します。 
      
      ![ストレージ アカウントの編集](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)

## <a name="delete-a-storage-account"></a>ストレージ アカウントの削除
> [!IMPORTANT]
> ストレージ アカウントは、使用されていない場合にのみ削除できます。 ストレージ アカウントが使用中の場合は、通知されます。
> 
> 

#### <a name="to-delete-a-storage-account"></a>ストレージ アカウントを削除するには
1. StorSimple Manager サービスのランディング ページでサービスを選択し、サービス名をダブルクリックします。**[構成]** をクリックします。
2. ストレージ アカウントの表形式のリストで、削除するアカウントにマウス ポインターを合わせます。
3. そのストレージ アカウントの右端の列に削除アイコン (**[x]**) が表示されます。 資格情報を削除するために、 **[x]** アイコンをクリックします。
4. 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして削除操作を続行します。 表形式のリストが更新され、変更が反映されます。
5. ページ下部の **[保存]** をクリックして、新しく更新した設定を保存します。

## <a name="next-steps"></a>次のステップ
* [StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法を確認します。




<!--HONumber=Nov16_HO3-->


