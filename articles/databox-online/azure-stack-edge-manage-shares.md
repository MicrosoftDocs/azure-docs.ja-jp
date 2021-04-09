---
title: Azure Stack Edge Pro - FPGA の共有管理 | Microsoft Docs
description: Azure portal を使用して Azure Stack Edge Pro - FPGA の共有を管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 7fbb5ca43d2877e2e14914b71df7cc1bcdf27f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898379"
---
# <a name="use-the-azure-portal-to-manage-shares-on-azure-stack-edge-pro-fpga"></a>Azure portal を使用して Azure Stack Edge Pro FPGA の共有を管理する 

この記事では、Azure Stack Edge Pro FPGA デバイスの共有を管理する方法について説明します。 Azure Stack Edge Pro FPGA デバイスは、Azure portal またはローカル Web UI を使用して管理できます。 Azure portal を使用し、共有に関連付けられているストレージ アカウントの共有を追加、削除、更新したり、ストレージ キーを同期したりすることができます。

## <a name="about-shares"></a>共有について

データを Azure に転送するには、Azure Stack Edge Pro FPGA で共有を作成する必要があります。 Azure Stack Edge Pro デバイスで追加する共有は、ローカル共有か、クラウドにデータをプッシュする共有になります。

 - **ローカル共有**:デバイス上でデータをローカル処理するとき、ローカル共有を使用します。
 - **共有**:デバイス データがクラウドのご利用のストレージ アカウントに自動的にプッシュされるようにする場合は、クラウド共有を使用します。 共有には、すべてのクラウド機能 (**更新**、**ストレージ キーの同期** など) が適用されます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * 共有の追加
> * 共有を削除する
> * 共有の更新
> * ストレージ キーの同期

## <a name="add-a-share"></a>共有の追加

共有を作成するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\)** に移動します。 **[共有]** に移動し、コマンド バーの **[+ 共有の追加]** を選択します。

    ![[共有の追加] を選択する](media/azure-stack-edge-manage-shares/add-share-1.png)

2. **[共有の追加]** で共有設定を指定します。 共有の一意の名前を指定します。
    
    共有名には、数字、英小文字、ハイフンのみを使用できます。 共有名の長さは 3 から 63 文字で、先頭は英字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。

3. 共有の **[種類]** を選択します。 種類には **SMB** (既定値) または **NFS** を選択することができます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。 SMB 共有と NFS 共有のどちらを選択するかに応じて、表示されるオプションの一部が変わります。

4. 共有を配置する **ストレージ アカウント** を指定します。 コンテナーがまだ存在しない場合は、ストレージ アカウントに共有名を持つコンテナーが作成されます。 コンテナーが既に存在する場合は、既存のコンテナーが使用されます。

5. ドロップダウン リストのブロック BLOB、ページ BLOB、またはファイルから **[ストレージ サービス]** を選択します。 選択されるサービスの種類は、Azure に存在するデータの形式によって変わります。 たとえば、このインスタンスでは、ブロック BLOB として Azure にデータを配置するため、 **[ブロック BLOB]** を選択します。 **[ページ BLOB]** を選択する場合は、データに 512 バイトが確実に割り当てられているようにする必要があります。 常に 512 バイトが割り当てられる VHD または VHDX には **[ページ BLOB]** を使用します。

   > [!IMPORTANT]
   > Azure Stack Edge Pro または Data Box Gateway デバイスで Azure Storage アカウントを使用する場合、その Azure Storage アカウントに不変ポリシーが設定されていないことを確認してください。 詳細については、「[BLOB ストレージの不変ポリシーを設定および管理する](../storage/blobs/storage-blob-immutability-policies-manage.md)」を参照してください。

6. この手順は、SMB 共有と NFS 共有のどちらを作成するかに応じて変わります。
   - **SMB 共有を作成する場合** - **[すべての権限を持つローカル ユーザー]** フィールドで、 **[新規作成]** または **[既存のものを使用]** を選択します。 新しいローカル ユーザーを作成する場合は、**ユーザー名**、**パスワード** を指定し、パスワードの確認を入力します。 これで、ローカル ユーザーにアクセス許可が割り当てられます。 ここで割り当てたアクセス許可は、ファイル エクスプローラーを使用して変更できます。

      ![SMB 共有を追加する](media/azure-stack-edge-manage-shares/add-smb-share.png)

        この共有データに対して [読み取り操作のみを許可する] をオンにすると、読み取り専用ユーザーを指定することができます。
   - **NFS 共有を作成する場合** - 共有へのアクセスが **許可されたクライアントの IP アドレス** を指定する必要があります。

      ![NFS 共有を追加する](media/azure-stack-edge-manage-shares/add-nfs-share.png)

7. Edge コンピューティング モジュールから共有に簡単にアクセスするには、ローカル マウント ポイントを使用します。 **[Edge コンピューティングで共有を使用する]** を選択すると、共有が作成された後、自動的にマウントされます。 このオプションが選択されている場合、Edge モジュールでもローカル マウント ポイントでコンピューティングを使用できます。

8. **[作成]** をクリックして共有を作成します。 共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、 **[共有]** ブレードは更新され、新しい共有が反映されます。

## <a name="add-a-local-share"></a>ローカル共有を追加する

1. Azure portal で Azure Stack Edge リソースに移動し、 **[クラウド ストレージ ゲートウェイ] > [共有]** の順に移動します。 コマンド バーの **+ [共有の追加]** を選択します。

   ![[共有の追加] を選択する 2](media/azure-stack-edge-manage-shares/add-local-share-1.png)

2. **[共有の追加]** で共有設定を指定します。 共有の一意の名前を指定します。
    
    共有名には、数字、英小文字、ハイフンのみを使用できます。 共有名の長さは 3 から 63 文字で、先頭は英字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。

3. 共有の **[種類]** を選択します。 種類には **SMB** (既定値) または **NFS** を選択することができます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。 SMB 共有と NFS 共有のどちらを選択するかに応じて、表示されるオプションの一部が変わります。

4. Edge コンピューティング モジュールから共有に簡単にアクセスするには、ローカル マウント ポイントを使用します。 Edge モジュールのコンピューティングでローカル マウント ポイントを使用できるように、 **[Edge コンピューティングで共有を使用する]** を選択します。

5. **[Edge ローカル共有として構成]** を選択します。 ローカル共有のデータはデバイス上にローカルで保持されます。 このデータはローカルで処理できます。

6. **[すべての権限を持つローカル ユーザー]** フィールドで、 **[新規作成]** または **[既存のものを使用]** を選択します。

7. **［作成］** を選択します 

   ![ローカル共有の作成](media/azure-stack-edge-manage-shares/add-local-share-2.png)

    共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、 **[共有]** ブレードは更新され、新しい共有が反映されます。

   ![[共有] ブレードの更新の表示](media/azure-stack-edge-manage-shares/add-local-share-3.png)
    


## <a name="mount-a-share"></a>共有のマウント

Azure Stack Edge Pro デバイス上でコンピューティングを構成する前に共有を作成した場合、その共有をマウントする必要があります。 共有をマウントするには、次の手順を行います。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[クラウド ストレージ ゲートウェイ] > [共有]** の順に移動します。 共有の一覧からマウントする共有を選択します。 **[コンピューティングに使用]** 列で、選択した共有の状態に **無効** と表示されます。

   ![共有を選択する 3](media/azure-stack-edge-manage-shares/select-share-mount.png)

2. **[マウント]** を選択します。

   ![[マウント] の選択](media/azure-stack-edge-manage-shares/select-mount.png)

3. 確認を求められたら、 **[はい]** を選択します。 これにより、共有がマウントされます。

   ![マウントを確認する](media/azure-stack-edge-manage-shares/confirm-mount.png)

4. 共有がマウントされたら、共有の一覧に移動します。 **[コンピューティングに使用]** 列の共有の状態が **有効** と表示されていることを確認できます。

   ![マウントされた共有](media/azure-stack-edge-manage-shares/share-mounted.png)

5. もう一度共有を選択し、共有に使用するローカル マウント ポイントを表示します。 Edge コンピューティング モジュールでは、共有にこのローカル マウント ポイントを使用します。

   ![共有用のローカル マウント ポイント](media/azure-stack-edge-manage-shares/share-mountpoint.png) 

## <a name="unmount-a-share"></a>共有のマウントの解除

共有のマウントを解除するには、Azure portal で次の手順を行います。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\) > [共有]** に移動します。

   ![共有を選択する 4](media/azure-stack-edge-manage-shares/select-share-unmount.png)

2. 共有の一覧で、マウントを解除する共有を選択します。 マウントを解除する共有がどのモジュールからも使用されていないことを確認する必要があります。 共有がモジュールによって使用されている場合、該当するモジュールで問題が発生します。 **[マウント解除]** を選択します。

   ![[マウント解除] を選択する](media/azure-stack-edge-manage-shares/select-unmount.png)

3. 確認を求められたら、 **[はい]** を選択します。 これにより、共有のマウントが解除されます。

   ![マウント解除を確認する](media/azure-stack-edge-manage-shares/confirm-unmount.png)

4. 共有のマウントが解除されたら、共有の一覧に移動します。 **[コンピューティングに使用]** 列の共有の状態に **無効** と表示されることを確認できます。

   ![マウントが解除された共有](media/azure-stack-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>共有を削除する

共有を削除するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\) > [共有]** に移動します。 共有の一覧で、削除したい共有を選択してクリックします。

   ![共有を選択する 5](media/azure-stack-edge-manage-shares/delete-share-1.png)

2. **[削除]** を選択します。

   ![[削除] の選択](media/azure-stack-edge-manage-shares/delete-share-2.png)

3. 確認を求められたら、 **[はい]** をクリックします。

   ![削除の確定](media/azure-stack-edge-manage-shares/delete-share-3.png)

共有の一覧が更新され、削除が反映されます。

## <a name="refresh-shares"></a>共有の更新

更新機能を使用すると、共有の内容を更新できます。 共有を更新すると、前回の更新後にクラウドに追加された BLOB とファイルを含むすべての Azure オブジェクトを見つけるために、検索が開始されます。 追加ファイルがダウンロードされ、デバイス上の共有の内容が更新されます。

> [!IMPORTANT]
>
> - ローカル共有は更新できません。
> - アクセス許可とアクセス制御リスト (ACL) は、更新操作の間で保持されません。 

共有を更新するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\) > [共有]** に移動します。 更新したい共有を選択してクリックします。

   ![共有を選択する 6](media/azure-stack-edge-manage-shares/refresh-share-1.png)

2. **[最新のデータに更新]** を選択します。

   ![更新を選択する](media/azure-stack-edge-manage-shares/refresh-share-2.png)
 
3. 確認を求められたら、 **[はい]** を選択します。 オンプレミスの共有の内容を更新するジョブが開始されます。

   ![更新を確認する](media/azure-stack-edge-manage-shares/refresh-share-3.png)

4. 更新の進行中は、コンテキスト メニューで更新オプションが淡色表示になります。 更新ジョブの状態を表示するには、ジョブの通知をクリックします。

5. 更新の時間は、Azure コンテナー内のファイルの数と、デバイス上のファイルの数によって異なります。 更新が正常に完了すると、共有のタイムスタンプが更新されます。 更新が部分的に失敗しても、操作は成功したと見なされ、タイムスタンプが更新されます。 更新エラー ログも更新されます。

   ![更新されたタイムスタンプ](media/azure-stack-edge-manage-shares/refresh-share-4.png)
 
失敗がある場合は、アラートが発生します。 アラートには、問題を解決するための推奨事項と原因が詳しく記載されています。 アラートには、更新または削除が失敗したファイルなど、失敗の完全なまとめが記載されているファイルへのリンクもあります。

## <a name="sync-storage-keys"></a>ストレージ キーの同期

ストレージ アカウント キーのローテーションが行われたら、ストレージ アクセス キーを同期する必要があります。 同期すると、ストレージ アカウントの最新のキーをデバイスで取得することができます。

ストレージ アクセス キーを同期するには、Azure portal で次の手順を実行してください。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\) > [共有]** に移動します。 共有の一覧で、同期する必要があるストレージ アカウントに関連付けられている共有を選択してクリックします。

    ![ストレージ アカウントに関連付けられている共有を選択する](media/azure-stack-edge-manage-shares/sync-storage-key-1.png)

2. **[ストレージ キーの同期]** をクリックします。 ページ下部の **[はい]** をクリックします。

     ![[ストレージ キーの同期] を選択する](media/azure-stack-edge-manage-shares/sync-storage-key-2.png)

3. 同期が完了したら、ダイアログを閉じます。

>[!NOTE]
> 指定のストレージ アカウントに対してこれを実行しなければならないのは 1 回だけです。 同じストレージ アカウントに関連付けられているすべての共有に対して、この操作を繰り返す必要はありません。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してユーザーを管理する](azure-stack-edge-manage-users.md)方法について学習します。