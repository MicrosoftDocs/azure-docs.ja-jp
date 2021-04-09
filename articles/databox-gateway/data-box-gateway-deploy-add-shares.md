---
title: Azure Data Box Gateway でデータを転送する | Microsoft Docs
description: Azure Data Box Gateway に共有を追加して接続する方法について説明します。その後、Data Box Gateway デバイスで Azure にデータを転送することができます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937204"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>チュートリアル:Azure Data Box Gateway を使用してデータを転送する


## <a name="introduction"></a>はじめに

この記事では、Data Box Gateway で共有を追加して接続する方法について説明します。 共有を追加したら、デバイスから Azure にデータを転送できます。

この手順の所要時間は約 10 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 共有の追加
> * 共有への接続

## <a name="prerequisites"></a>前提条件

共有を Data Box Gateway に追加する前に、以下の点を確認してください。

- [Hyper-V での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-hyperv.md)または [VMware での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-vmware.md)に関するページの説明に従って仮想デバイスをプロビジョニングし、その仮想デバイスに接続されていること。

- [Azure Data Box Gateway の接続とアクティブ化](data-box-gateway-deploy-connect-setup-activate.md)に関するページで取り上げた仮想デバイスがアクティブ化済みであること。

- これで、デバイスで共有を作成し、データを転送する準備が整います。

## <a name="add-a-share"></a>共有の追加

共有を作成するには、次の手順を実行します。

1. **Azure portal** で Data Box Gateway リソースを選択し、[[概要]](https://portal.azure.com/) に移動します。 デバイスがオンラインになっている必要があります。 デバイスのコマンド バーで **[+ 共有の追加]** を選択します。
   
   ![共有の追加](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. **[共有の追加]** で、次の手順を実行します。

    1. 共有の一意の名前を指定します。 共有名には、英小文字、数字、ハイフンのみを使用できます。 共有名の長さは 3 から 63 文字で、先頭は英字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。
    
    2. 共有の **[種類]** を選択します。 種類には SMB (既定値) または NFS を選択できます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。 SMB 共有と NFS 共有のどちらを選択するかに応じて、表示されるオプションの一部が変わります。

    3. 共有を配置するストレージ アカウントを指定します。 コンテナーがまだ存在しない場合は、新しく作成された共有の名前が付いたものがストレージ アカウントに作成されます。 コンテナーが既に存在する場合は、そのコンテナーが使用されます。
       > [!IMPORTANT]
       > Data Box Gateway デバイスで Azure Storage アカウントを使用する場合、そのアカウントに不変ポリシーが設定されていないことを確認してください。 詳細については、「[BLOB ストレージの不変ポリシーを設定および管理する](../storage/blobs/storage-blob-immutability-policies-manage.md)」を参照してください。
    
    4. ブロック BLOB、ページ BLOB、またはファイルから **[ストレージ サービス]** を選択します。 選択されるサービスの種類は、Azure に存在するデータの形式によって変わります。 たとえば、このインスタンスでは、BLOB ブロックとして Azure にデータを配置するため、ブロック BLOB を選択します。 ページ BLOB を選択する場合は、データに 512 バイトが割り当てられていることを確認する必要があります。 たとえば、VHDX は常に 512 バイトでアラインされています。
   
    5. この手順は、SMB 共有と NFS 共有のどちらを作成するかに応じて変わります。
     
    - **SMB 共有** - **[All privilege local user]\(すべての権限を持つローカル ユーザー\)** で、 **[新規作成]** または **[既存のものを使用]** を選択します。 新しいローカル ユーザーを作成する場合は、**ユーザー名** と **パスワード** を入力し、**パスワードを確認入力** します。 この操作を行うと、ローカル ユーザーにアクセス許可が割り当てられます。 共有レベルのアクセス許可の変更は現在サポートされていません。
    
        ![SMB 共有を追加する](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        この共有データに対して **[読み取り操作のみを許可する]** チェック ボックスをオンにすると、読み取り専用ユーザーを指定することができます。
        
    - **NFS 共有** - 共有にアクセスできる許可するクライアントの IP アドレスを指定します。

        ![NFS 共有を追加する](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. **[作成]** を選択して共有を作成します。
    
    共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、 **[共有]** タイルは更新され、新しい共有が反映されます。
    
    ![更新された [共有] タイル](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>共有に接続する

これで、前の手順でお客様が作成した 1 つ以上の共有に接続できるようになりました。 お客様が SMB 共有と NFS 共有のどちらを作成したかに応じて、手順が異なる場合があります。

### <a name="connect-to-an-smb-share"></a>SMB 共有に接続する

お客様の Data Box Gateway に接続されたお客様の Windows Server クライアントで、コマンドを入力して SMB 共有に接続します。


1. コマンド ウィンドウで次を入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    メッセージに従って共有のパスワードを入力します。 このコマンドのサンプル出力を次に示します。

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. キーボードの Windows キーを押しながら R キーを押します。 
3. **実行** ウィンドウで、`\\<device IP address>` を指定し、 **[OK]** を選択します。 エクスプローラーが開きます。 これで、お客様が作成した共有をフォルダーとして確認できます。 エクスプローラーで共有 (フォルダー) をダブルクリックすると、内容が表示されます。
 
    ![SMB 共有に接続する](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    データが生成され、デバイスからクラウドにデータがプッシュされると、これらの共有にデータが書き込まれます。

### <a name="connect-to-an-nfs-share"></a>NFS 共有に接続する

デバイスに接続された Linux クライアントで、次の手順を実行します。

1. クライアントに NFSv4 クライアントがインストール済みであることを確認します。 NFS クライアントをインストールするには、次のコマンドを使用します。

   `sudo apt-get install nfs-common`

    詳細については、[NFSv4 クライアントのインストール](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)に関するページを参照してください。

2. NFS クライアントをインストールした後、次のコマンドを使用して、作成した NFS 共有を Data Box Gateway デバイスにマウントします。

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    マウントを設定する前に、ローカル コンピューターのマウント ポイントとして機能するディレクトリが既に作成されており、ファイルやサブフォルダーが含まれていないことを確認します。

    NFS 経由で Gateway デバイス上の共有に接続する方法の例を次に示します。 仮想デバイスの IP は `10.10.10.60` で、共有 `mylinuxshare2` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/gateway` です。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> このリリースには、次の注意事項が適用されます。
> - 共有にファイルを作成した後に、ファイル名を変更することはできません。
> - 共有からファイルを削除しても、ストレージ アカウントのエントリは削除されません。
> - データのコピーに `rsync` を使用している場合、`rsync -a` オプションはサポートされません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 共有の追加
> * 共有への接続


次のチュートリアルに進み、Data Box Gateway を管理する方法を学習してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Data Box Gateway を管理する](https://aka.ms/dbg-docs)