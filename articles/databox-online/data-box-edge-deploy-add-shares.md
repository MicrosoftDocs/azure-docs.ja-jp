---
title: Edge を使用してデータを転送する | Microsoft Docs
description: Edge デバイスで共有を追加して接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e902f0c9465f65f31f6e1a5cadc7b6b30cda1a27
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403682"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>チュートリアル:Azure Data Box Edge を使用してデータを転送する

このチュートリアルでは、お客様の Data Box Edge デバイスに共有を追加して接続する方法について説明します。 共有を追加したら、Data Box Edge を使用して Azure にデータを転送できます。

この手順の所要時間は約 10 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 共有の追加
> * 共有に接続する

 
## <a name="prerequisites"></a>前提条件

共有を Data Box Edge に追加する前に、以下を確認してください。

- [Azure Data Box Edge のインストール](data-box-edge-deploy-install.md)に関するページで説明されているとおり、自分の物理デバイスをインストールしたこと。

- [Azure Data Box Edge の接続、設定、アクティブ化](data-box-edge-deploy-connect-setup-activate.md)に関するページで説明されているとおり、物理デバイスをアクティブ化したこと。


## <a name="add-a-share"></a>共有の追加

共有を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) で Data Box Edge リソースを選択し、**[概要]** に移動します。 デバイスがオンラインになっている必要があります。

   ![デバイスがオンライン](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. デバイスのコマンド バーで **[+ 共有の追加]** を選択します。

   ![共有の追加](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. **[共有の追加]** ウィンドウで、次の手順を行います。

    a. **[名前]** ボックスで、一意の名前をお客様の共有に指定します。  
    共有名に使用できるのは、小文字、数字、ハイフンのみです。 長さは 3 文字から 63 文字で、先頭は文字または数字である必要があります。 ハイフンの前後には文字または数字が必要です。
    
    b. 共有の **[種類]** を選択します。  
    種類には **SMB** (既定値) または **NFS** を選択することができます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。  
    お客様が SMB 共有と NFS 共有のどちらを選択するかに応じて、残りのオプションは若干異なります。 

    c. 共有を配置するストレージ アカウントを指定します。 

    
    d. **[ストレージ サービス]** ドロップダウン リストで、**[ブロック BLOB]**、**[ページ BLOB]**、**[ファイル]** のいずれかを選択します。  
    お客様が選択するサービスの種類は、Azure で使用したいデータの形式によって変わります。 この例では、データをブロック BLOB として Azure に格納したいため、**[ブロック BLOB]** を選択します。 **ページ BLOB** を選択する場合は、お客様のデータが 512 バイトでアラインされるようにします。 たとえば、VHDX は常に 512 バイトでアラインされています。

    e. 新しい BLOB コンテナーを作成するか、ドロップダウン リストから既存の BLOB コンテナーを選択します。 BLOB コンテナーを作成する場合は、コンテナー名を入力します。 コンテナーがまだ存在しない場合は、新しく作成された共有の名前が付いたものがストレージ アカウントに作成されます。
   
    f. お客様が SMB 共有と NFS 共有のどちらを作成したかに応じて、次の手順のいずれかを実行します。 
     
    - **SMB 共有**: **[All privilege local user]\(すべての権限を持つローカル ユーザー\)** で、**[新規作成]** または **[既存のものを使用]** を選択します。 新しいローカル ユーザーを作成する場合は、ユーザー名とパスワードを入力し、パスワードを確認入力します。 この操作を行うと、ローカル ユーザーにアクセス許可が割り当てられます。 ここで割り当てたアクセス許可は、エクスプローラーを使用して変更できます。

        この共有データに対して **[読み取り操作のみを許可する]** チェック ボックスをオンにすると、読み取り専用ユーザーを指定することができます。

        ![SMB 共有を追加する](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 共有**: 共有へのアクセスが許可されたクライアントの IP アドレスを入力します。

        ![NFS 共有を追加する](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. **[作成]** を選択して共有を作成します。
    
    共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、**[共有]** タイルは更新され、新しい共有が反映されます。
    

## <a name="connect-to-the-share"></a>共有に接続する

これで、前の手順でお客様が作成した 1 つ以上の共有に接続できるようになりました。 お客様が SMB 共有と NFS 共有のどちらを作成したかに応じて、手順が異なる場合があります。

### <a name="connect-to-an-smb-share"></a>SMB 共有に接続する

お客様の Data Box Edge デバイスに接続されたお客様の Windows Server クライアントで、コマンドを入力して SMB 共有に接続します。


1. コマンド ウィンドウで次を入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. 共有のパスワードの入力を求められた場合は、それを入力します。  
   このコマンドのサンプル出力を次に示します。

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. キーボードの Windows キーを押しながら R キーを押します。

4. **実行**ウィンドウで、`\\<device IP address>` を指定し、**[OK]** を選択します。  
   エクスプローラーが開きます。 これで、お客様が作成した共有をフォルダーとして確認できます。 エクスプローラーで共有 (フォルダー) をダブルクリックすると、内容が表示されます。
 
    ![SMB 共有に接続する](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    データが生成され、デバイスからクラウドにデータがプッシュされると、これらの共有にデータが書き込まれます。

### <a name="connect-to-an-nfs-share"></a>NFS 共有に接続する

お客様の Data Box Edge デバイスに接続されたお客様の Linux クライアントで、次の手順を実行します。

1. クライアントに NFSv4 クライアントがインストール済みであることを確認します。 NFS クライアントをインストールするには、次のコマンドを使用します。

   `sudo apt-get install nfs-common`

    詳細については、[NFSv4 クライアントのインストール](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)に関するページを参照してください。

2. NFS クライアントがインストールされた後、次のコマンドを使用して、お客様が作成した NFS 共有をお客様の Data Box Edge デバイスにマウントします。

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > 共有をマウントする際に `sync` オプションを使用すると、大きなファイルの転送速度が向上します。
    > 共有をマウントする前に、ローカル コンピューター上のマウント ポイントとして機能するディレクトリが既に作成されていることを確認します。 これらのディレクトリに、ファイルやサブフォルダーを含めることはできません。

    NFS 経由で Edge デバイス上の共有に接続する方法の例を次に示します。 デバイスの IP アドレスは `10.10.10.60` です。 共有 `mylinuxshare2` は ubuntuVM にマウントされています。 共有マウント ポイントは `/home/databoxubuntuhost/edge` です。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> このリリースには、次の注意事項が適用されます。
> - 共有にファイルを作成した後に、ファイル名を変更することはできません。 
> - 共有からファイルを削除しても、ストレージ アカウントのエントリは削除されません。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Box Edge に関する次のトピックについて説明しました。

> [!div class="checklist"]
> * 共有の追加
> * 共有への接続

Data Box Edge を使用してお客様のデータを変換する方法について学習するために、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Edge を使用してデータを変換する](./data-box-edge-deploy-configure-compute.md)


