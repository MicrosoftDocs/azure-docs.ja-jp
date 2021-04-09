---
title: チュートリアル - Azure File Sync を使用して Windows ファイル サーバーを拡張する | Microsoft Docs
description: Azure File Sync を使用して Windows ファイル サーバーを拡張する方法について、開始から完了まで説明します。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329460"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>チュートリアル:Azure File Sync を使用して Windows ファイル サーバーを拡張する

この記事では、Azure File Sync を使用して Windows サーバーのストレージ容量を拡張するための基本的な手順を示します。このチュートリアルでは Azure 仮想マシン (VM) の Windows Server を取り上げますが、通常はお客様のオンプレミス サーバーに対してこのプロセスを実行します。 ご自分の環境に Azure File Sync をデプロイする手順については、「[Azure File Sync のデプロイ](storage-sync-files-deployment-guide.md)」の記事を参照してください。

> [!div class="checklist"]
> * ストレージ同期サービスのデプロイ
> * Azure File Sync で使用する Windows Server の準備
> * Azure File Sync エージェントをインストールする
> * Windows Server をストレージ同期サービスに登録する
> * 同期グループとクラウド エンドポイントを作成する
> * サーバー エンドポイントを作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="prepare-your-environment"></a>環境を準備する

このチュートリアルでは、Azure File Sync をデプロイする前に次の手順を実行する必要があります。

- Azure ストレージ アカウントおよびファイル共有を作成する
- Windows Server 2016 Datacenter VM を設定する
- Azure File Sync 用に Windows Server VM を準備する

### <a name="create-a-folder-and-txt-file"></a>フォルダーと .txt ファイルを作成する

ローカル コンピューターに _FilesToSync_ という名前の新しいフォルダーを作成し、_mytestdoc.txt_ という名前のテキスト ファイルを追加します。 このチュートリアルの後の方で、そのファイルをファイル共有にアップロードします。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>ファイル共有を作成する

Azure ストレージ アカウントをデプロイした後、ファイル共有を作成します。

1. Azure portal で **[リソースに移動]** を選択します。
1. ストレージ アカウントのウィンドウで **[ファイル]** を選択します。

    ![[ファイル] を選択する](./media/storage-sync-files-extend-servers/click-files.png)

1. **+ [ファイル共有]** を選択します。

    ![ファイル共有の追加ボタンを選択する](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. 新しいファイル共有に「_afsfileshare_」と名付けます。 **[クォータ]** に「1」と入力し、 **[作成]** を選択します。 クォータは最大で 5 TiB にすることができますが、このチュートリアルでは 1 GB しか必要ありません。

    ![新しいファイル共有に名前とクォータを指定する](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. 新しいファイル共有を選択します。 ファイル共有の場所で、 **[アップロード]** を選択します。

    ![ファイルをアップロードする](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. お客様が .txt ファイルを作成した _FilesToSync_ フォルダーを参照し、_mytestdoc.txt_、 **[アップロード]** の順に選択します。

    ![ファイル共有を参照する](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

この時点で、Azure ストレージ アカウントと、中に 1 つのファイルが含まれたファイル共有を作成できました。 次に、このチュートリアルでオンプレミス サーバーを表す、Windows Server 2016 Datacenter がある Azure VM をデプロイします。

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>VM をデプロイしてデータ ディスクを接続する

1. Azure portal に移動して、左側のメニューを展開します。 左上隅にある **[リソースの作成]** を選択します。
1. **Azure Marketplace** リソースの一覧の上にある検索ボックスで「**Windows Server 2016 Datacenter**」と検索し、結果の中からそれを選択します。 **[作成]** を選択します。
1. **[基本]** タブに移動します。 **[プロジェクトの詳細]** で、お客様がこのチュートリアルのために作成したリソース グループを選択します。

   ![ポータルのブレードで VM に関する基本情報を入力する](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. **[インスタンスの詳細]** で VM の名前を指定します。 たとえば、_myVM_ を使用します。
1. **[リージョン]** 、 **[可用性オプション]** 、 **[イメージ]** 、 **[サイズ]** は、既定の設定を変更しないでください。
1. **[管理者アカウント]** で、VM の **[ユーザー名]** と **[パスワード]** を指定します。
1. **[受信ポートの規則]** で、 **[選択されたポートを許可する]** を選択してから、ドロップダウン メニューで **[RDP (3389)]** と **[HTTP]** を選択します。

1. VM を作成する前に、データ ディスクを作成する必要があります。

   1. **[Next:Disks]\(次へ: ディスク\)** を選択します。

      ![データ ディスクを追加する](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. **[ディスク]** タブの **[ディスクのオプション]** は、既定値のままにしておきます。
   1. **[データ ディスク]** で **[新しいディスクを作成し接続する]** を選択します。

   1. このチュートリアルで **1 GB** に変更してかまわない **[サイズ (GiB)]** 以外は、既定の設定を使用します。

      ![データ ディスクの詳細](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. **[OK]** を選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

   **[通知]** アイコンを選択すると、**デプロイの進行状況** を見ることができます。 新しい VM の作成が完了するまでに数分かかることがあります。

1. お客様の VM のデプロイが完了したら、 **[リソースに移動]** を選択します。

   ![リソースに移動](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

この時点で、新しい仮想マシンを作成し、データ ディスクを接続できました。 次は VM に接続します。

### <a name="connect-to-your-vm"></a>VM に接続する

1. Azure portal の仮想マシンのプロパティ ページで、 **[接続]** を選択します。

   ![ポータルから Azure VM に接続する](./media/storage-sync-files-extend-servers/connect-vm.png)

1. **[仮想マシンに接続する]** ページで、ポート 3389 を介して **IP アドレス** で接続する既定のオプションをそのまま使用します。 **[RDP ファイルのダウンロード]** を選択します。

   ![RDP ファイルのダウンロード](./media/storage-sync-files-extend-servers/download-rdp.png)

1. ダウンロードした RDP ファイルを開き、プロンプトが表示されたら **[接続]** を選択します。
1. **[Windows セキュリティ]** ウィンドウで、 **[その他]** 、 **[別のアカウントを使用する]** の順に選択します。 ユーザー名として「*localhost\username*」と入力し、お客様が仮想マシン用に作成したパスワードを入力して、 **[OK]** を選択します。

   ![その他](./media/storage-sync-files-extend-servers/local-host2.png)

1. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** を選択して接続を作成します。

### <a name="prepare-the-windows-server"></a>Windows サーバーを準備する

Windows Server 2016 Datacenter サーバーで、[Internet Explorer セキュリティ強化の構成] を無効にします。 この手順は、初回のサーバー登録でのみ必要です。 サーバーの登録後に再び有効にできます。

Windows Server 2016 Datacenter VM で、サーバー マネージャーが自動的に開かれます。  サーバー マネージャーが既定で開かれない場合は、[スタート] メニューでそれを検索します。

1. **サーバー マネージャー** で **[ローカル サーバー]** を選択します。

   ![サーバー マネージャー UI の左側にある [ローカル サーバー]](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. **[プロパティ]** ウィンドウで、 **[IE セキュリティ強化の構成]** リンクを選択します。  

    ![サーバー マネージャー UI の [IE セキュリティ強化の構成] ウィンドウ](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、 **[管理者]** と **[ユーザー]** について **[オフ]** を選択します。

    ![[オフ] が選択された [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウ](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

これで、データ ディスクを VM に追加することができます。

### <a name="add-the-data-disk"></a>データ ディスクを追加する

1. まだ **Windows Server 2016 Datacenter** VM 上で、 **[Files and storage services]\(ファイル サービスおよびストレージ サービス\)**  >  **[ボリューム]**  >  **[ディスク]** の順に選択します。

    ![データ ディスク](media/storage-sync-files-extend-servers/your-disk.png)

1. **Msft Virtual Disk** という名前の 1 GB のディスクを右クリックし、 **[新しいボリューム]** を選択します。
1. ウィザードを完了します。 既定の設定を使用し、割り当てられたドライブ文字をメモします。
1. **［作成］** を選択します
1. **[閉じる]** を選択します。

   この時点で、ディスクをオンラインにし、ボリュームを作成できました。 Windows Server VM でエクスプローラーを開いて、先ほど追加したデータ ディスクがあることを確認します。

1. VM のエクスプローラーで **[この PC]** を展開し、新しいドライブを開きます。 この例では、F: ドライブです。
1. 右クリックし、 **[新規作成]**  >  **[フォルダー]** の順に選択します。 フォルダーに _FilesToSync_ という名前を付けます。
1. **FilesToSync** フォルダーを開きます。
1. 右クリックし、 **[新規作成]**  >  **[テキスト ドキュメント]** の順に選択します。 テキスト ファイルに _MyTestFile_ という名前を付けます。

    ![新しいテキスト ファイルを追加する](media/storage-sync-files-extend-servers/new-file.png)

1. **エクスプローラー** と **サーバー マネージャー** を閉じます。

### <a name="download-the-azure-powershell-module"></a>Azure PowerShell モジュールをダウンロードする

次に、Windows Server 2016 Datacenter VM で、Azure PowerShell モジュールをサーバーにインストールします。

1. VM で管理者特権の PowerShell ウィンドウを開きます。
1. 次のコマンドを実行します。

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > ご利用の NuGet のバージョンが 2.8.5.201 未満である場合、最新バージョンの NuGet をダウンロードしてインストールするように求められます。

   既定では、PowerShell ギャラリーは、PowerShellGet の信頼できるリポジトリとしては構成されません。 PSGallery の初回使用時には、次のプロンプトが表示されます。

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. インストールを続行するには、**Yes** または **Yes to All** と回答します。

`Az` モジュールは、Azure PowerShell コマンドレットのロールアップ モジュールです。 これをインストールすると、利用可能なすべての Azure Resource Manager モジュールがダウンロードされ、コマンドレットを使用できるようになります。

この時点で、チュートリアル用にお客様の環境を設定できました。 これでストレージ同期サービスをデプロイできます。

## <a name="deploy-the-service"></a>サービスをデプロイする

Azure File Sync をデプロイするには、最初に、お客様が選択したサブスクリプションのリソース グループに **ストレージ同期サービス** リソースを配置します。 ストレージ同期サービスは、そのサブスクリプションとリソース グループからアクセス許可を継承します。

1. Azure portal で **[リソースの作成]** を選択し、次に **Azure File Sync** を検索します。
1. 検索結果で、 **[Azure File Sync]** を選択します。
1. **[作成]** を選択して、 **[ストレージ同期のデプロイ]** タブを開きます。

   ![ストレージ同期のデプロイ](media/storage-sync-files-extend-servers/afs-info.png)

   開いたウィンドウに、次の情報を入力します。

   | Value | 説明 |
   | ----- | ----- |
   | **名前** | ストレージ同期サービスの (サブスクリプションごとに) 一意の名前。<br><br>このチュートリアルでは _afssyncservice02_ を使用します。 |
   | **サブスクリプション** | お客様がこのチュートリアルに使用する Azure サブスクリプション。 |
   | **リソース グループ** | ストレージ同期サービスを含むリソース グループ。<br><br>このチュートリアルでは、_afsresgroup101918_ を使用します。 |
   | **場所** | 米国東部 |

1. 完了したら、 **[作成]** を選択して、**ストレージ同期サービス** をデプロイします。
1. **[通知]** タブ、 **[リソースに移動]** の順に選択します。

## <a name="install-the-agent"></a>エージェントをインストールする

Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。

1. **Windows Server 2016 Datacenter** VM で **Internet Explorer** を開きます。
1. [Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?linkid=858257)に移動します。 **[Azure File Sync Agent]\(Azure File Sync エージェント\)** セクションまで下へスクロールして、 **[ダウンロード]** を選択します。

   ![同期エージェントのダウンロード](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. **StorageSyncAgent_V3_WS2016.EXE** のチェック ボックスをオンにし、 **[次へ]** を選択します。

   ![エージェントを選択する](media/storage-sync-files-extend-servers/select-agent.png)

1. **[一度のみ許可]**  >  **[実行]**  >  **[開く]** を順に選択します。
1. まだ PowerShell ウィンドウを閉じていない場合は閉じます。
1. **ストレージ同期エージェントのセットアップ ウィザード** で、既定値を受け入れます。
1. **[インストール]** を選択します。
1. **[完了]** を選択します。

Azure Sync Service をデプロイし、Windows Server 2016 Datacenter VM にエージェントをインストールしました。 次に、VM をストレージ同期サービスに登録する必要があります。

## <a name="register-windows-server"></a>Windows Server を登録する

お客様の Windows サーバーをストレージ同期サービスに登録すると、サーバー (またはクラスター) とストレージ同期サービスの間に信頼関係が確立されます。 サーバーは、1 つのストレージ同期サービスにのみ登録できます。 そのストレージ同期サービスに関連付けられている他のサーバーおよび Azure ファイル共有と同期できます。

Azure File Sync エージェントをインストールした後、サーバー登録 UI が自動的に開かれます。 開かない場合、ファイルの場所 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.` から手動で開くことができます。

1. VM でサーバー登録 UI が開いたら、 **[OK]** を選択します。
1. **[サインイン]** を選択して開始します。
1. お客様の Azure アカウントの資格情報を使用してサインインし、 **[サインイン]** を選択します。
1. 次の情報を指定します。

   ![サーバー登録 UI のスクリーンショット](media/storage-sync-files-extend-servers/signin.png)

   | Value | 説明 |
   | ----- | ----- |
   | **Azure サブスクリプション** | このチュートリアルのストレージ同期サービスが含まれているサブスクリプション。 |
   | **リソース グループ** | ストレージ同期サービスを含むリソース グループ。 このチュートリアルでは、_afsresgroup101918_ を使用します。 |
   | **ストレージ同期サービス** | ストレージ同期サービスの名前。 このチュートリアルでは _afssyncservice02_ を使用します。 |

1. **[登録]** を選択して、サーバーの登録を完了します。
1. 登録プロセスの一環として、追加のサインインを求められます。 サインインして **[次へ]** を選択します。
1. **[OK]** を選択します。

## <a name="create-a-sync-group"></a>同期グループを作成する

同期グループは、一連のファイルの同期トポロジを定義します。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが 1 つ含まれている必要があります。 さらに、同期グループにはサーバー エンドポイントが 1 つ以上含まれている必要があります。 サーバー エンドポイントは、登録されているサーバー上のパスを表します。 同期グループを作成するには:

1. [Azure portal](https://portal.azure.com/) のストレージ同期サービスで **+ [同期グループ]** を選択します。 このチュートリアルでは *afssyncservice02* を使用します。

   ![Azure Portal で新しい同期グループを作成する](media/storage-sync-files-extend-servers/add-sync-group.png)

1. 次の情報を入力して、同期グループとクラウド エンドポイントを作成します。

   | Value | 説明 |
   | ----- | ----- |
   | **同期グループ名** | この名前は、ストレージ同期サービス内で一意である必要がありますが、理にかなった任意の名前を指定できます。 このチュートリアルでは *afssyncgroup* を使用します。|
   | **サブスクリプション** | このチュートリアルのストレージ同期サービスをデプロイしたサブスクリプション。 |
   | **ストレージ アカウント** | **[ストレージ アカウントの選択]** を選択します。 表示されるウィンドウで、お客様が作成した Azure ファイル共有があるストレージ アカウントを選択します。 このチュートリアルでは、*afsstoracct101918* を使用します。 |
   | **Azure ファイル共有** | お客様が作成した Azure ファイル共有の名前。 このチュートリアルでは、*afsfileshare* を使用します。 |

1. **［作成］** を選択します

同期グループを選択すると、1 つの **クラウド エンドポイント** ができたことを確認できます。

## <a name="add-a-server-endpoint"></a>サーバー エンドポイントを追加する

サーバー エンドポイントは、登録されているサーバー上の特定の場所を表します。 たとえば、サーバー ボリューム上のフォルダーです。 サーバー エンドポイントを追加するには:

1. 新しく作成した同期グループを選択し、 **[サーバー エンドポイントの追加]** を選択します。

   ![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. **[サーバー エンドポイントの追加]** ウィンドウで、次の情報を入力してサーバー エンドポイントを作成します。

   | Value | 説明 |
   | ----- | ----- |
   | **登録済みサーバー** | お客様が作成したサーバーの名前。 このチュートリアルでは *afsvm101918* を使用します。 |
   | **パス** | お客様が作成したドライブへの Windows Server パス。 このチュートリアルでは、*f:\filestosync* を使用します。 |
   | **クラウドの階層化** | このチュートリアルでは、無効のままにします。 |
   | **ボリュームの空き領域** | このチュートリアルでは空白のままにします。 |

1. **［作成］** を選択します

Azure ファイル共有と Windows Server でファイルが同期されるようになりました。

![Azure Storage が正常に同期された](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップする場合は、まず、ストレージ同期サービスからエンドポイントを削除します。 次に、ストレージ同期サービスからサーバーの登録を解除し、同期グループを削除してから、同期サービスを削除します。

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure File Sync を使用して Windows サーバーのストレージ容量を拡張するための基本的な手順を説明しました。Azure File Sync のデプロイ計画の全体像については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure File Sync のデプロイの計画](./storage-sync-files-planning.md)
