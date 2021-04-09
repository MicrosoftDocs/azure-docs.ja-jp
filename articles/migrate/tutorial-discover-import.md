---
title: Azure Migrate Server Assessment でインポートされた CSV ファイルを使用してオンプレミスのサーバーを評価する
description: Azure Migrate Server Assessment でインポートされた CSV ファイルを使用して、Azure への移行のためにオンプレミスのサーバーを検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753111"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>チュートリアル:インポートされた CSV ファイルを使用してサーバーを評価する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。 

このチュートリアルでは、インポートされた CSV (コンマ区切り値) ファイルを使用して、Azure Migrate: Server Assessment ツールでオンプレミスのマシンを評価する方法について説明します。 

CSV ファイルを使用する場合、サーバーを検出して評価するために Azure Migrate アプライアンスを設定する必要はありません。 ファイルで共有するデータを制御できます。データの多くは省略可能です。 この方法は次の場合に便利です。

- アプライアンスをデプロイする前に、クイック初期評価を作成したい。
- Azure Migrate アプライアンスを組織内に展開することはできません。
- オンプレミス サーバーへのアクセスを許可する資格情報を共有することはできません。
- セキュリティの制約があるため、アプライアンスによって収集されたデータを収集して Azure に送信することはできません。

> [!NOTE]
> CSV ファイルを使用してインポートされたサーバーを移行することはできません。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure アカウントを設定する
> * Azure Migrate プロジェクトを設定する
> * CSV ファイルを準備する
> * ファイルをインポートする
> * サーバーの評価

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、可能であれば既定のオプションを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

- 1 つの CSV ファイルおよび Azure Migrate プロジェクトに最大 20,000 台のサーバーを追加できます。 
- CSV ファイルに指定されたオペレーティング システム名には、[サポートされている名前](#supported-operating-system-names)と一致するものが含まれている必要があります。


## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure Migrate プロジェクトを作成するには、以下を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可。
- Azure Active Directory アプリを登録するためのアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックス](./media/tutorial-discover-import/search-subscription.png)

2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。 
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。

    ![ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てる](./media/tutorial-discover-import/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、共同作成者または所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    ![[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てる](./media/tutorial-discover-import/assign-role.png)

7. ポータルでユーザーを検索し、 **[サービス]** で **[ユーザー]** を選択します。
8. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>プロジェクトの設定

Azure Migrate プロジェクトがない場合は、新しいプロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-discover-import/new-project.png)

7. **［作成］** を選択します
8. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。

**Azure Migrate: Server Assessment** ツールは、新しいプロジェクトに既定で追加されます。

![既定で追加された Server Assessment ツールを示すページ](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>CSV を準備する

CSV テンプレートをダウンロードし、サーバー情報を追加します。

### <a name="download-the-template"></a>テンプレートをダウンロードする

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]** で **[Import using CSV]\(CSV を使用してインポート\)** を選択します。
3. **[ダウンロード]** を選択して CSV テンプレートをダウンロードします。 また、[直接ダウンロードする](https://go.microsoft.com/fwlink/?linkid=2109031)こともできます。

    ![CSV テンプレートをダウンロードする](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>サーバー情報を追加する

サーバー データを収集し、CSV ファイルに追加します。

- データを収集するには、VMware vSphere や構成管理データベース (CMDB) など、オンプレミスのサーバー管理に使用するツールからデータをエクスポートします。
- サンプル データを確認するには、[サンプル ファイル](https://go.microsoft.com/fwlink/?linkid=2108405)をダウンロードします。

次の表は、入力するファイル フィールドをまとめたものです。

**フィールド名** | **必須** | **詳細**
--- | --- | ---
**サーバー名** | はい | 完全修飾ドメイン名 (FQDN) を指定することをお勧めします。
**IP アドレス (IP address)** | いいえ | サーバー アドレス。
**コア** | はい | サーバーに割り当てられているプロセッサ コアの数。
**[メモリ]** | はい | サーバーに割り当てられている合計 RAM (MB 単位)。
**OS 名** | はい | サーバーのオペレーティング システム。 <br/> この[一覧](#supported-operating-system-names)内の名前と一致するか、この名前を含むオペレーティング システム名は、評価によって認識されます。
**OS バージョン** | いいえ | サーバーのオペレーティング システムのバージョン。
**OS アーキテクチャ** | いいえ | サーバー OS アーキテクチャ <br/> 有効な値: x64、x86、amd64、32 ビット、64 ビット
**ディスクの数** | いいえ | 個々のディスクの詳細が指定されている場合は必要ありません。
**Disk 1 size (ディスク 1 のサイズ)**  | いいえ | ディスクの最大サイズ (GB 単位)。<br/>テンプレートに[列を追加](#add-multiple-disks)することで、さらにディスクの詳細を追加できます。 最大 8 つのディスクを追加できます。
**Disk 1 read ops (ディスク 1 の読み取り操作)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Disk 1 write ops (ディスク 1 の書き込み操作)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Disk 1 read throughput (ディスク 1 の読み取りスループット)** | いいえ | 1 秒あたりにディスクから読み取られたデータ (MB/ 秒)。
**Disk 1 write throughput (ディスク 1 の書き込みスループット)** | いいえ | 1 秒あたりにディスクに書き込まれたデータ (MB/ 秒)。
**CPU utilization percentage (CPU 使用率)** | いいえ | 使用されている CPU の割合。
**Memory utilization percentage (メモリ使用率)** | いいえ | 使用されている RAM の割合。
**Total disks read ops (ディスク読み取り操作の合計)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Total disks write ops (ディスク書き込み操作の合計)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Total disks read throughput (ディスク読み取りスループットの合計)** | いいえ | ディスクから読み取られたデータ (MB/ 秒)。
**Total disks write throughput (ディスク書き込みスループットの合計)** | いいえ | ディスクに書き込まれたデータ (MB/ 秒)。
**Network In throughput (ネットワークの受信スループット)** | いいえ | サーバーによって受信されたデータ (MB/秒)。
**Network out throughput (ネットワークの送信スループット)** | いいえ | サーバーによって送信されたデータ (MB/秒)。
**Firmware type (ファームウェアの種類)** | いいえ | サーバーのファームウェア。 値には "BIOS" または "UEFI" を指定できます。
**MAC アドレス**| いいえ | サーバーの MAC アドレス。


### <a name="add-operating-systems"></a>オペレーティング システムを追加する

評価では特定のオペレーティング システム名が認識されます。 指定する名前は、[サポートされている名前の一覧](#supported-operating-system-names)内のいずれかの文字列と正確に一致している必要があります。

### <a name="add-multiple-disks"></a>複数のディスクを追加する

このテンプレートには、最初のディスクの既定フィールドが用意されています。 最大 8 台のディスクに対して同様の列を追加できます。

たとえば、2 番目のディスクに対してすべてのフィールドを指定するには、次の列を追加します。

- Disk 2 size (ディスク 2 のサイズ)
- Disk 2 read ops (ディスク 2 の読み取り操作)
- Disk 2 write ops (ディスク 2 の書き込み操作)
- Disk 2 read throughput (ディスク 2 の読み取りスループット)
- Disk 2 write throughput (ディスク 2 の書き込みスループット)


## <a name="import-the-server-information"></a>サーバー情報をインポートする

CSV テンプレートに情報を追加したら、CSV ファイルを Server Assessment にインポートします。

1. Azure Migrate の **[マシンの検出]** で、入力したテンプレートに移動します。
2. **[インポート]** を選択します。
3. インポートの状態が表示されます。
    - 状態に警告が表示される場合は、それらを修正するか、対処せずに続行することができます。
    - 評価の精度を向上させるには、警告内の提案に従ってサーバー情報を修正します。
    - 警告を確認して修正を行うには、 **[Download warning details .CSV]\(警告の詳細の .CSV のダウンロード\)** を選択します。 この操作により、警告が含まれた CSV がダウンロードされます。 警告を確認し、必要に応じて問題を修正します。
    - 状態にエラーが表示される場合 (つまり、インポートの状態が **[失敗]** の場合)、インポートを続行する前にこれらのエラーを修正する必要があります。
        1. エラーの詳細を含む CSV をダウンロードします。
        1. 必要に応じて、エラーを確認して対処します。 
        1. 変更したファイルを再度アップロードします。
4. インポートの状態が **[完了]** の場合は、サーバーの情報がインポートされています。 インポート プロセスが完了していないように見える場合は、最新の情報に更新してください。

## <a name="update-server-information"></a>サーバー情報を更新する

サーバーの情報を更新するには、同じ **サーバー名** を使用してサーバーのデータをもう一度インポートします。 **[サーバー名]** フィールドを変更することはできません。 サーバーの削除は現在サポートされていません。

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出後にサーバーが Azure portal に表示されることを確認するには:

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、**検出済みサーバー** の数を表示するアイコンを選択します。
3. **[Import based]\(インポート ベース\)** タブを選択します。



## <a name="supported-operating-system-names"></a>サポートされるオペレーティング システム名

CSV で指定されたオペレーティング システム名には、以下の名前と一致するものが含まれている必要があります。 そうでない場合、それらを評価することはできません。 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure Migrate プロジェクトを作成しました。 
> * インポートされた CSV ファイルを使用してサーバーを検出しました。 次に、[VMware VM を Azure VM に移行](./tutorial-assess-vmware-azure-vm.md)するための評価を実行します。