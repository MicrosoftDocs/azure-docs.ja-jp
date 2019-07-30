---
title: Azure Backup での VM へのファイルの復元
description: Azure VM でバックアップおよび Recovery Services を使用して、ファイル レベルの復元を実行する方法について説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 553f19a2cb621698cb5b47e56d7cb30d529cc243
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467054"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Azure の仮想マシンにファイルを復元する
Azure Backup では、geo 冗長 Recovery コンテナーに保存される復旧ポイントが作成されます。 復旧ポイントから復元するときは、VM 全体または個々のファイルを復元することができます。 この記事では、個々のファイルを復元する方法について詳しく説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 復旧ポイントをリストして選択する
> * VM に復旧ポイントを接続する
> * 復旧ポイントからファイルを復元する

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.18 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 


## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Azure Backup で保護されている Linux VM が必要です。 誤って削除したファイルの復旧プロセスをシミュレートするには、Web サーバーからページを削除します。 Web サーバーが実行されており、Azure Backup で保護されている Linux VM が必要な場合は、「[Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md)」 (CLI による Azure での仮想マシンのバックアップ) を参照してください。


## <a name="backup-overview"></a>Backup の概要
Azure でバックアップが開始されると、VM のバックアップ拡張機能により、特定の時点のスナップショットが取得されます。 バックアップ拡張機能は、最初のバックアップが要求されたときに VM にインストールされます。 Azure Backup は、バックアップが行われるときに VM が実行されていない場合に、基になるストレージのスナップショットも取得します。

既定では、Azure Backup はファイル システム整合性バックアップを取得します。 Azure Backup がスナップショットを取得すると、データは Recovery Services コンテナーに転送されます。 効率を最大に高めるために、Azure Backup は前回のバックアップ以降に変更されたデータ ブロックを特定し、そのデータのみを転送します。

データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。


## <a name="delete-a-file-from-a-vm"></a>VM からファイルを削除する
ファイルを誤って削除または変更した場合は、復旧ポイントから個々のファイルを復元できます。 このプロセスでは、復旧ポイントでバックアップされたファイルを参照し、必要なファイルのみを復元できます。 この例では、ファイル レベルの復旧プロセスを示すために Web サーバーからファイルを削除します。

1. VM に接続するには、次のように、[az vm show](/cli/azure/vm?view=azure-cli-latest#az-vm-show) で VM の IP アドレスを取得します。

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Web サイトが現在動作していることを確認するには、Web ブラウザーを開いて VM のパブリック IP アドレスを入力します。 Web ブラウザーのウィンドウは開いたままにしておきます。

    ![NGINX の既定の Web ページ](./media/tutorial-restore-files/nginx-working.png)

3. SSH で VM に接続します。 *publicIpAddress* は、前述のコマンドで取得したパブリック IP アドレスに置き換えます。

    ```bash
    ssh publicIpAddress
    ```

4. 次のように、Web サーバーから既定のページ ( */var/www/html/index.nginx-debian.html*) を削除します。

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Web ブラウザーで、Web ページを最新の情報に更新します。 次の例に示すように、Web サイトにはページが読み込まれなくなります。

    ![NGINX Web サイトに既定のページが読み込まれなくなった状態](./media/tutorial-restore-files/nginx-broken.png)

6. 次のように、VM への SSH セッションを閉じます。

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>ファイルの回復スクリプトを生成する
ファイルを復元するために、Azure Backup は、ローカル ドライブとして復旧ポイントを接続する VM で実行するスクリプトを提供します。 このローカル ドライブを参照し、VM 自体にファイルを復元してから、復旧ポイントを切断できます。 Azure Backup は、スケジュールとリテンション期間に割り当てられたポリシーに基づいて、データを引き続きバックアップします。

1. VM の復旧ポイントをリストする場合は、[az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) を使用します。 この例では、*myRecoveryServicesVault* で保護されている *myVM* という名前の VM の最新の復旧ポイントを選択します。

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. VM に復旧ポイントを接続またはマウントするスクリプトを取得する場合は、[az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp) を使用します。 次の例では、*myRecoveryServicesVault* で保護されている *myVM* という名前の VM のスクリプトを取得します。

    *myRecoveryPointName* は、前述のコマンドで取得した復旧ポイントの名前に置き換えます。

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    次の例のように、スクリプトがダウンロードされ、パスワードが表示されます。

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. VM にスクリプトを転送するには、セキュア コピー (SCP) を使用します。 ダウンロードしたスクリプトの名前を指定して、*publicIpAddress* を VM のパブリック IP アドレスに置き換えます。 次のように、SCP コマンドの末尾には必ず `:` を含めるようにしてください。

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>VM にファイルを復元する
VM に回復スクリプトをコピーしたら、復旧ポイントを接続してファイルを復元できます。

1. SSH で VM に接続します。 *publicIpAddress* は、次のように、使用している VM のパブリック IP アドレスに置き換えます。

    ```bash
    ssh publicIpAddress
    ```

2. スクリプトを正しく実行できるように、**chmod** を使用して実行アクセス許可を追加します。 次のように、独自のスクリプトの名前を入力します。

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. 復旧ポイントをマウントするには、スクリプトを実行します。 次のように、独自のスクリプトの名前を入力します。

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    スクリプトが実行されると、復旧ポイントにアクセスするためのパスワードの入力を求められます。 回復スクリプトを生成した前述の [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp) コマンドからの出力に示されているパスワードを入力します。

    スクリプトからの出力で復旧ポイントのパスを確認できます。 次の出力例では、 */home/azureuser/myVM-20170919213536/Volume1* で復旧ポイントがマウントされていることが示されています。

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. **cp** を使用して、NGINX の既定の Web ページをマウントされた復旧ポイントから元のファイルの場所にコピーして戻します。 */home/azureuser/myVM-20170919213536/Volume1* マウント ポイントは、次のように、独自の場所に置き換えます。

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. Web ブラウザーで、Web ページを最新の情報に更新します。 次の例に示すように、これで Web サイトに再びページが正しく読み込まれるようになりました。

    ![NGINX Web サイトに正しく読み込まれるようになった状態](./media/tutorial-restore-files/nginx-restored.png)

7. 次のように、VM への SSH セッションを閉じます。

    ```bash
    exit
    ```

8. [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-unmount-rp) を使用して、VM から復旧ポイントのマウントを解除します。 次の例では、*myRecoveryServicesVault* の *myVM* という名前の VM から復旧ポイントのマウントを解除します。

    *myRecoveryPointName* は、前述のコマンドで取得した復旧ポイントの名前に置き換えます。
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>次の手順
このチュートリアルでは、復旧ポイントを VM に接続し、Web サーバーのファイルを復元しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * 復旧ポイントをリストして選択する
> * VM に復旧ポイントを接続する
> * 復旧ポイントからファイルを復元する

次のチュートリアルに進み、Windows Server を Azure にバックアップする方法を学習してください。

> [!div class="nextstepaction"]
> [Windows Server の Azure へのバックアップ](tutorial-backup-windows-server-to-azure.md)

