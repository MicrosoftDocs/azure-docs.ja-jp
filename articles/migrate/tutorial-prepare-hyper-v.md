---
title: Azure Migrate を使用した評価と移行に向けて Hyper-V VM を準備する
description: Azure Migrate を使用した評価と移行に向けて Hyper-V VM を準備する方法について説明します。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 8ecb886b5d5cd9d6811788043b924880b4c366c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928923"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V VM の評価および Azure への移行を準備する

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)と [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) を使用した Hyper-V VM の評価および Azure への移行を準備する方法について説明します。


これはシリーズの最初のチュートリアルであり、Hyper-V VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate と連携するように Azure を準備します。
> * Hyper-V VM の評価を準備します。
> * Hyper-V VM の移行を準備する 

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

この表は、Azure で実行する必要があるタスクの概要を示しています。 表の下に、手順を示します。

**タスク** | **詳細** | **アクセス許可**
--- | --- | ---
**Azure Migrate プロジェクトの作成** | Azure Migrate プロジェクトは、Azure Migrate ツール、Microsoft ツール、およびサードパーティのオファリングを使用して、評価と移行を調整および管理するための一元的な場所を提供します。 | Azure アカウントには、プロジェクトが存在するリソース グループの共同作成者または所有者のアクセス許可が必要です。
**アプライアンスの登録** | Azure Migrate は、軽量な Azure Migrate アプライアンスを使用して、Hyper-V VM を検出しおよび評価します。 [詳細については、こちらを参照してください](migrate-appliance-architecture.md#appliance-registration)。 | アプライアンスを登録するには、Azure アカウントに Azure サブスクリプションに対する共同作成者または所有者のアクセス許可が必要です。
**Azure AD アプリの作成** | アプライアンスを登録するとき、Azure Migrate によって、アプライアンス上で実行されているエージェントと Azure Migrate との間の通信に使用される Azure Active Directory (Azure AD) アプリが作成されます。 | Azure アカウントには、Azure AD アプリを作成するためのアクセス許可が必要です。
**キー コンテナーの作成** | キー コンテナーはアプライアンスの登録の一部として作成され、その構成中にアプライアンスでダウンロードされた証明書の管理に使用されます。| Azure Migrate がキー コンテナーを作成できるようにするには、Azure Migrate プロジェクトが存在するリソース グループに対する共同作成者のアクセス許可が Azure アカウントに必要です。
**VM を作成します** | リソース グループと仮想ネットワーク内に VM を作成し、Azure マネージド ディスクに書き込むためのアクセス許可が必要です。 | 使用する Azure アカウントには、仮想マシン共同作成者ロールが必要です。


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

Azure Migrate プロジェクトを作成するためのアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Azure AD アプリを作成するためのアクセス許可を割り当てる

次のいずれかの方法を使用して、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

> [!NOTE]
> - このアプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
> - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細については、こちらを参照してください](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。


### <a name="assign-azure-account-permissions"></a>Azure アカウントのアクセス許可を割り当てる

仮想マシンの共同作成者ロールをアカウントに割り当てて、以下を行うためのアクセス許可を持てるようにします。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- Azure マネージド ディスクに書き込む。 


### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

[Azure ネットワークをセットアップ](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 オンプレミスのマシンは Azure マネージド ディスクにレプリケートされます。 移行のために Azure にフェールオーバーすると、これらのマネージド ディスクから Azure VM が作成され、設定した Azure ネットワークに参加させられます。


## <a name="prepare-for-assessment"></a>評価の準備

Hyper-V の VM 評価を、手動で、または構成スクリプトを使用して、準備することができます。 以下に準備の手順を示します。 スクリプトを使用して準備する場合、これらは自動的に構成されます。

**Step** | **[スクリプト]** | **手動**
--- | --- | ---
**Hyper-V ホストの要件を確認する** | スクリプトにより、サポートされているバージョンの Hyper-V がホストで実行されていること、および Hyper-V ロールが確認されます。<br/><br/> WinRM サービスを有効にし、ホスト上のポート 5985 (HTTP) と 5986 (HTTPS) を開きます (メタデータの収集に必要)。 | サーバー評価のための [Hyper-V ホストの要件](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)を確認します。<br/><br/> Hyper-V ホスト上で[必要なポート](migrate-support-matrix-hyper-v.md#port-access)が開かれていることを確認します。
**PowerShell のバージョンを確認する** | サポートされている PowerShell バージョンでスクリプトが実行されていることを確認します。 | Hyper-V ホストで PowerShell バージョン4.0 以降が実行されていることを確認します。
**アカウントの作成** | 自分 (スクリプトを実行しているユーザー) が Hyper-V ホストの管理特権を持っていることを確認します。<br/><br/>  Hyper-V ホストと通信するために Azure Migrate サービスによって使用されるローカル ユーザー アカウント (管理者ではありません) を作成できるようにします。 このユーザー アカウントは、ホスト上のこれらのグループに追加されます。<br/><br/> - リモート管理ユーザー<br/><br/> - Hyper-V 管理者<br/><br/>- パフォーマンス モニター ユーザー | Hyper-V ホストおよびクラスターに対する管理者アクセス許可を備えた、ドメインまたはローカルのユーザー アカウントを設定します。<br/><br/> - 検出に含めるすべてのホストとクラスターに対して 1 つのアカウントが必要です。<br/><br/> - アカウントは、ローカル アカウントでもドメイン アカウントでもかまいません。 Hyper-V ホストまたはクラスターに対する管理者アクセス許可を用意することをお勧めします。<br/><br/> または、管理者アクセス許可を割り当てたくない場合、以下のアクセス許可が必要です。リモート管理ユーザー、Hyper-V 管理者、パフォーマンス モニター ユーザー。
**PowerShell リモート処理を有効にする** | ホスト上で PowerShell リモート処理を有効にして、Azure Migrate アプライアンスが WinRM 接続を介してホスト上で PowerShell コマンドを実行できるようにします。| 設定するには、各ホスト上で、管理者として PowerShell コンソールを開き、次のコマンドを実行します。<br/><br/>``` Enable-PSRemoting -force ```
**Hyper-V 統合サービスの設定** | ホストによって管理されているすべての VM で Hyper-V 統合サービスが有効になっていることを確認します。 |  各 VM で [Hyper-V 統合サービスを有効にします](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。<br/><br/> Windows Server 2003 を実行している場合は、[これらの手順に従います](prepare-windows-server-2003-migration.md)。
**VM ディスクがリモート SMB 共有に配置されている場合に資格情報を委任する** | スクリプトにより資格情報が委任されます。 | [CredSSP を有効にして](#enable-credssp-to-delegate-credentials)資格情報を委任します。

### <a name="run-the-script"></a>スクリプトを実行する

次のようにスクリプトを実行します。

1. Hyper-V ホストに PowerShell バージョン 4.0 以降がインストールされていることを確認します。
2. [Microsoft ダウンロード センター](https://aka.ms/migrate/script/hyperv)からスクリプトをダウンロードします。 このスクリプトは、Microsoft によって暗号的に署名されています。
3. MD5 または SHA256 ハッシュ ファイルを使用して、スクリプトの整合性を検証します。 ハッシュタグの値は以下に記載されています。 このコマンドを実行して、スクリプトのハッシュを生成します。
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    使用例:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. スクリプトの整合性を検証した後、この PowerShell コマンドを使用して、各 Hyper-V ホストでスクリプトを実行します。
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>ハッシュタグの値

ハッシュ値は次のとおりです。

| **ハッシュ** | **Value** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>CredSSP を有効にして資格情報を委任する

ホストに VM があり、ディスクが SMB 共有上にある場合は、ホスト上でこの手順を実行します。

- このコマンドは、すべての Hyper-V ホスト上でリモートで実行できます。
- クラスターに新しいホスト ノードを追加すると、自動的に検出用に追加されますが、必要に応じて新しいノード上で CredSSP を手動で有効にする必要があります。

次のように有効にします。

1. SMB 共有上にディスクがある Hyper-V VM を実行している Hyper-V ホストを特定します。
2. 特定された各 Hyper-V ホスト上で、次のコマンドを実行します。

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

アプライアンスを設定したら、[アプライアンス上で CredSSP を有効にして](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds)、その設定を終了します。 これについては、このシリーズの次のチュートリアルで説明します。


## <a name="prepare-for-appliance-deployment"></a>アプライアンスのデプロイの準備

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. アプライアンスの要件を[確認](migrate-appliance.md#appliance---hyper-v)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある Azure の URL について確認します。 URL ベースのファイアウォールまたはプロキシを使用している場合は、必要な URL へのアクセスが許可されていることを確認します。
3. 検出および評価中にアプライアンスによって収集されるデータを確認します。
4. アプライアンスのポート アクセス要件を[確認](migrate-appliance.md#collected-data---hyper-v)します。


## <a name="prepare-for-hyper-v-migration"></a>Hyper-V の移行の準備

1. Hyper-V ホストの移行の要件と、VM 移行のために Hyper-V ホストおよびクラスターがアクセスする必要がある Azure URL を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)します。
2. Azure に移行する Hyper-V VM の要件を[確認](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)します。
3. VM を Azure に移行する前に、いくつかの変更を行う必要があります。
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
    - [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) で必要な変更を確認してください。



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アカウントのアクセス許可を設定しました。
> * 評価および移行のために Hyper-V ホストおよび VM を準備しました。
> * Azure Migrate アプライアンスのデプロイを準備しました。

次のチュートリアルに進み、Azure Migrate プロジェクトを作成し、アプライアンスをデプロイして、Azure に移行する Hyper-V VM を検出および評価します。

> [!div class="nextstepaction"]
> [Hyper-V VM を評価する](./tutorial-assess-hyper-v.md)
