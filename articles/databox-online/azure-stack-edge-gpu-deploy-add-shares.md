---
title: 'チュートリアル: Azure Stack Edge Pro GPU を使用して共有にデータを転送する | Microsoft Docs'
description: Azure Stack Edge Pro GPU デバイスに共有を追加して接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 26a2be6d2752393362b6b4f9a666dd0b74e64321
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636777"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>チュートリアル:Azure Stack Edge Pro GPU を使用して共有経由でデータを転送する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

このチュートリアルでは、お客様の Azure Stack Edge Pro デバイスに共有を追加して接続する方法について説明します。 共有を追加した後、Azure Stack Edge Pro から Azure にデータを転送できます。

この手順の所要時間は約 10 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 共有の追加
> * 共有に接続する

## <a name="prerequisites"></a>前提条件

共有を Azure Stack Edge Pro に追加する前に、次のことを確認してください。

* [Azure Stack Edge Pro の設置](azure-stack-edge-gpu-deploy-install.md)に関するページで説明されているように、物理デバイスが設置されていること。

* [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関するページの説明に従って物理デバイスがアクティブ化されていること。

## <a name="add-a-share"></a>共有の追加

共有を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) で Azure Stack Edge リソースを選択し、 **[概要]** に移動します。 デバイスがオンラインになっている必要があります。 **[クラウド ストレージ ゲートウェイ]** を選択します。

   ![デバイスがオンライン](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. デバイスのコマンド バーで **[+ 共有の追加]** を選択します。

   ![共有の追加](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. **[共有の追加]** ペインで、次の手順に従います。

    a. **[名前]** ボックスで、一意の名前をお客様の共有に指定します。  
    共有名に使用できるのは、アルファベット、数字、ハイフンのみです。 長さは 3 文字から 63 文字で、先頭は文字または数字である必要があります。 ハイフンの前後には文字または数字が必要です。
    
    b. 共有の **[種類]** を選択します。  
    種類には **SMB** (既定値) または **NFS** を選択することができます。 SMB は Windows クライアントの場合に標準です。また、Linux クライアントの場合は NFS が使用されます。  
    お客様が SMB 共有と NFS 共有のどちらを選択するかに応じて、残りのオプションは若干異なります。 

    c. 共有を配置するストレージ アカウントを指定します。

    d. **[ストレージ サービス]** ドロップダウン リストで、 **[ブロック BLOB]** 、 **[ページ BLOB]** 、 **[ファイル]** のいずれかを選択します。  
    お客様が選択するサービスの種類は、Azure で使用したいデータの形式によって変わります。 この例では、データをブロック BLOB として Azure に格納したいため、 **[ブロック BLOB]** を選択します。 **ページ BLOB** を選択する場合は、お客様のデータが 512 バイトでアラインされるようにします。 たとえば、VHDX は常に 512 バイトでアラインされています。

   > [!IMPORTANT]
   > Azure Stack Edge Pro または Data Box Gateway デバイスで Azure Storage アカウントを使用する場合、その Azure Storage アカウントに不変ポリシーが設定されていないことを確認してください。 詳細については、「[BLOB ストレージの不変ポリシーを設定および管理する](../storage/blobs/storage-blob-immutability-policies-manage.md)」を参照してください。

    e. 新しい BLOB コンテナーを作成するか、ドロップダウン リストから既存の BLOB コンテナーを選択します。 BLOB コンテナーを作成する場合は、コンテナー名を入力します。 コンテナーがまだ存在しない場合は、新しく作成された共有の名前が付いたものがストレージ アカウントに作成されます。
   
    f. お客様が SMB 共有と NFS 共有のどちらを作成したかに応じて、次の手順のいずれかを実行します。 
     
    - **SMB 共有**: **[All privilege local user]\(すべての権限を持つローカル ユーザー\)** で、 **[新規作成]** または **[既存のものを使用]** を選択します。 新しいローカル ユーザーを作成する場合は、ユーザー名とパスワードを入力し、パスワードを確認入力します。 この操作を行うと、ローカル ユーザーにアクセス許可が割り当てられます。 共有レベルのアクセス許可の変更は現在サポートされていません。 この共有データに対して **[読み取り操作のみを許可する]** チェック ボックスをオンにすると、読み取り専用ユーザーを指定することができます。
    
        ![SMB 共有を追加する](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 共有**: 共有へのアクセスが許可されたクライアントの IP アドレスを入力します。

        ![NFS 共有を追加する](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. **[作成]** を選択して共有を作成します。
    
    共有の作成が進行中であることが通知されます。 指定した設定で共有を作成すると、 **[共有]** タイルは更新され、新しい共有が反映されます。
    

## <a name="connect-to-the-share"></a>共有に接続する

これで、前の手順でお客様が作成した 1 つ以上の共有に接続できるようになりました。 お客様が SMB 共有と NFS 共有のどちらを作成したかに応じて、手順が異なる場合があります。

最初の手順は、SMB または NFS 共有を使用しているときにデバイス名を解決できるようにすることです。

### <a name="modify-host-file-for-name-resolution"></a>名前解決のためのホスト ファイルを変更する

次に、デバイスの IP と、デバイスのローカル Web UI で定義したデバイスのフレンドリ名を追加します。

- クライアント上のホスト ファイル、または
- DNS サーバー上のホスト ファイル

> [!IMPORTANT]
> デバイスの名前解決のために、DNS サーバーのホスト ファイルを変更することをお勧めします。

デバイスへの接続に使用している Windows クライアントで、次の手順を実行します。

1. **メモ帳** を管理者として起動し、`C:\Windows\System32\Drivers\etc` にある **hosts** ファイルを開きます。

    ![エクスプローラーの hosts ファイル](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. **hosts** ファイルに次のエントリを追加します。値は、実際のデバイスに合わせて置き換えてください。 

    ```
    <Device IP>   <device friendly name>
    ``` 
    デバイスの IP はローカル Web UI の **[ネットワーク]** から、デバイスのフレンドリ名は **[デバイス]** ページから取得できます。 次の hosts ファイルのスクリーンショットはこのエントリを示しています。

    ![エクスプローラーの hosts ファイル 2](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>SMB 共有に接続する

お客様の Azure Stack Edge Pro デバイスに接続されている Windows Server クライアントで、コマンドを入力して SMB 共有に接続します。


1. コマンド ウィンドウで次を入力します。

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > SMB 共有に接続するには、デバイスの IP アドレスではなく、デバイス名を使用する必要があります。

2. 共有のパスワードの入力を求められた場合は、それを入力します。  
   このコマンドのサンプル出力を次に示します。

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. キーボードの Windows キーを押しながら R キーを押します。

4. **実行** ウィンドウで、`\\<device name>` を指定し、 **[OK]** を選択します。  

    ![Windows の [ファイル名を指定して実行] ダイアログ](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   エクスプローラーが開きます。 これで、お客様が作成した共有をフォルダーとして確認できます。 エクスプローラーで共有 (フォルダー) をダブルクリックすると、内容が表示されます。
 
    ![SMB 共有に接続する](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    データが生成され、デバイスからクラウドにデータがプッシュされると、これらの共有にデータが書き込まれます。

### <a name="connect-to-an-nfs-share"></a>NFS 共有に接続する

お客様の Azure Stack Edge Pro デバイスに接続されている Linux クライアントで、次の手順を実行します。

1. クライアントに NFSv4 クライアントがインストール済みであることを確認します。 NFS クライアントをインストールするには、次のコマンドを使用します。

   `sudo apt-get install nfs-common`

    詳細については、[NFSv4 クライアントのインストール](https://help.ubuntu.com/community/NFSv4Howto)に関するページを参照してください。

2. NFS クライアントがインストールされたら、次のコマンドを使用して、作成した NFS 共有を Azure Stack Edge Pro デバイスにマウントします。

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    ローカル Web UI の **[ネットワーク]** ページからデバイスの IP を取得できます。

    > [!IMPORTANT]
    > 共有をマウントする際に `sync` オプションを使用すると、大きなファイルの転送速度が向上します。
    > 共有をマウントする前に、ローカル コンピューター上のマウント ポイントとして機能するディレクトリが既に作成されていることを確認します。 これらのディレクトリに、ファイルやサブフォルダーを含めることはできません。

    NFS 経由で Azure Stack Edge Pro デバイス上の共有に接続する方法の例を次に示します。 デバイスの IP アドレスは `10.10.10.60` です。 共有 `mylinuxshare2` は ubuntuVM にマウントされています。 共有マウント ポイントは `/home/azurestackedgeubuntuhost/edge` です。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> このリリースには、次の注意事項が適用されます。
> - 共有にファイルを作成した後に、ファイル名を変更することはできません。 
> - 共有からファイルを削除しても、Azure Storage アカウントのエントリは削除されません。
> - `rsync` を使用して NFS 経由でコピーする場合は、`--inplace` フラグを使用します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge Pro に関する次のトピックについて説明しました。

> [!div class="checklist"]
> * 共有の追加
> * 共有への接続

Azure Stack Edge Pro を使用してデータを変換する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro でデータを変換する](./azure-stack-edge-j-series-deploy-configure-compute.md)