---
title: Azure Migrate を使用して評価と Azure への移行のために Hyper-V VM を準備する | Microsoft Docs
description: Azure Migrate を使用して、Hyper-V VM の評価および Azure への移行を準備する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 87df37cc6baa863bb0b068bdfeb9cde873e38836
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952079"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V VM の評価および Azure への移行を準備する

この記事では、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの Hyper-V VM の評価および Azure への移行を準備する方法について説明します。

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 

これはシリーズの最初のチュートリアルであり、Hyper-V VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure を準備します。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * サーバー評価用にオンプレミスの Hyper-V ホストと VM を準備します。
> * サーバー移行用にオンプレミスの Hyper-V ホストと VM を準備します。


> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、Hyper-V の評価と移行に関する手引きを参照してください。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

### <a name="azure-permissions"></a>Azure のアクセス許可

Azure Migrate のデプロイに対するアクセス許可を設定する必要があります。

- お使いの Azure アカウントで Azure Migrate プロジェクトを作成するためのアクセス許可。 
- お使いの Azure アカウントで Azure Migrate アプライアンスを登録するためのアクセス許可。 アプライアンスは、Hyper-V の検出と移行に使用されます。 アプライアンスの登録時に、Azure Migrate によって、アプライアンスを一意に識別する 2 つの Azure Active Directory (Azure AD) アプリが作成されます。
    - 1 つ目のアプリは、Azure Migrate サービス エンドポイントと通信します。
    - 2 つ目のアプリは、登録時に作成された Azure キー コンテナーにアクセスし、Azure AD アプリ情報とアプライアンス構成設定を格納します。



### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

Azure Migrate プロジェクトを作成するためのアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

次のいずれかの方法を使用して、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

次の点に注意してください。

- アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
- 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。 


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細情報](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て 

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細情報](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-for-hyper-v-assessment"></a>Hyper-V の評価の準備

Hyper-V の評価を準備するには、次の手順を実行します。

1. Hyper-V ホストの設定を確認します。
2. 各ホスト上で PowerShell リモート処理を設定して、Azure Migrate アプライアンスが WinRM 接続を介してホスト上で PowerShell コマンドを実行できるようにします。
3. VM ディスクがリモート SMB ストレージに配置されている場合は、資格情報の委任が必要になります。 
    - Azure Migrate アプライアンスがクライアントとして機能し、資格情報をホストに委任できるように、CredSSP 委任を有効にします。 T
    - 次に示すように、各ホストがアプライアンスの代理として機能できるようにします。
    - 後でアプライアンスを設定するときに、アプライアンス上で委任を有効にします。
4. アプライアンスの要件と、アプライアンスに必要な URL またはポート アクセスを確認します。
5. アプライアンスで VM の検出に使用されるアカウントを設定します。
6. 検出と評価を行いたい各 VM で Hyper-V 統合サービスを設定します。


これらの設定は、下記の手順を使用して手動で構成できます。 または、Hyper-V の前提条件の構成スクリプトを実行します。

### <a name="hyper-v-prerequisites-configuration-script"></a>Hyper-V の前提条件の構成スクリプト

このスクリプトを使用して、Hyper-V ホストを検証し、Hyper-V VM を検出して評価するために必要な設定を構成します。 これは次のように動作します。

- サポートされている PowerShell バージョンでスクリプトが実行されていることを確認します。
- 自分 (スクリプトを実行しているユーザー) が Hyper-V ホストの管理特権を持っていることを確認します。
- Azure Migrate サービスが Hyper-V ホストと通信するために使用されるローカル ユーザー アカウント (管理者ではありません) を作成できるようにします。 このユーザー アカウントは、ホスト上のこれらのグループに追加されます。
    - リモート管理ユーザー
    - Hyper-V 管理者
    - パフォーマンス モニター ユーザー
- サポートされているバージョンの Hyper-V がホストで実行されていることを確認し、Hyper-V ロールを確認します。
- WinRM サービスを有効にし、ホスト上のポート 5985 (HTTP) と 5986 (HTTPS) を開きます (メタデータの収集に必要)。
- ホストでの PowerShell リモート処理を有効にします。
- ホストによって管理されているすべての VM で Hyper-V 統合サービスが有効になっていることを確認します。 
- 必要に応じて、ホスト上で CredSSP を有効にします。

次のようにスクリプトを実行します。

1. Hyper-V ホストに PowerShell バージョン 4.0 以降がインストールされていることを確認します。
2. [Microsoft ダウンロード センター](https://aka.ms/migrate/script/hyperv)からスクリプトをダウンロードします。 このスクリプトは、Microsoft によって暗号的に署名されています。
3. MD5 または SHA256 ハッシュ ファイルを使用して、スクリプトの整合性を検証します。 このコマンドを実行して、スクリプトのハッシュを生成します。
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    使用例: 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```
    
    ハッシュ値は次のとおりです。
    Hash | 値
    --- | ---
    **MD5** | 0ef418f31915d01f896ac42a80dc414e
    **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2


4.  スクリプトの整合性を検証した後、この PowerShell コマンドを使用して、各 Hyper-V ホストでスクリプトを実行します。
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```


### <a name="verify-hyper-v-host-settings"></a>Hyper-V ホストの設定の確認

1. サーバー評価のための [Hyper-V ホストの要件](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements)を確認します。
2. Hyper-V ホスト上で[必要なポート](migrate-support-matrix-hyper-v.md#assessment-port-requirements)が開かれていることを確認します。

### <a name="enable-powershell-remoting-on-hosts"></a>ホスト上での PowerShell リモート処理の有効化

次のように、各ホスト上で PowerShell リモート処理を設定します。

1. 各ホスト上で、管理者として PowerShell コンソールを開きます。
2. 次のコマンドを実行します。

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>ホスト上での CredSSP の有効化

ホストに VM があり、ディスクが SMB 共有上にある場合は、ホスト上でこの手順を実行します。

- このコマンドは、すべての Hyper-V ホスト上でリモートで実行できます。
- クラスターに新しいホスト ノードを追加すると、自動的に検出用に追加されますが、必要に応じて新しいノード上で CredSSP を手動で有効にする必要があります。

次のように有効にします。

1. SMB 共有上にディスクがある Hyper-V VM を実行している Hyper-V ホストを特定します。
2. 特定された各 Hyper-V ホスト上で、次のコマンドを実行します。

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

アプライアンスを設定したら、[アプライアンス上で CredSSP を有効にして](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds)その設定を終了します。 これについては、このシリーズの次のチュートリアルで説明します。


### <a name="verify-appliance-settings"></a>アプライアンスの設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. アプライアンスの要件を[確認](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)します。
2. アプライアンスがアクセスする必要がある Azure URL を[確認](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)します。
3. 検出および評価中にアプライアンスによって収集されるデータを確認します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-hyper-v.md#assessment-port-requirements)します。


### <a name="set-up-an-account-for-vm-discovery"></a>VM 検出用のアカウントの設定

Azure Migrate には、オンプレミスの VM を検出するためのアクセス許可が必要です。

- Hyper-V ホストおよびクラスターに対する管理者アクセス許可を備えた、ドメインまたはローカルのユーザー アカウントを設定します。

    - 検出に含めるすべてのホストとクラスターに対して 1 つのアカウントが必要です。
    - アカウントは、ローカル アカウントでもドメイン アカウントでもかまいません。 Hyper-V ホストまたはクラスターに対する管理者アクセス許可を用意することをお勧めします。
    - または、管理者アクセス許可を割り当てたくない場合、以下のアクセス許可が必要です。
        - リモート管理ユーザー
        - Hyper-V 管理者
        - パフォーマンス モニター ユーザー

### <a name="enable-integration-services-on-vms"></a>VM での統合サービスの有効化

Azure Migrate が VM 上のオペレーティング システム情報をキャプチャできるように、各 VM で統合サービスを有効にする必要があります。

検出と評価を行う VM については、各 VM で [Hyper-V 統合サービス](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)を有効にします。 

## <a name="prepare-for-hyper-v-migration"></a>Hyper-V の移行の準備

1. 移行のための Hyper-V ホスト要件を[確認](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements)します。
2. Azure に移行する Hyper-V VM の要件を[確認](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements)します。
3. VM 移行のために Hyper-V ホストおよびクラスターがアクセスする必要がある Azure URL を[メモ](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のことを行いました。
 
> [!div class="checklist"] 
> * Azure アカウントのアクセス許可を設定しました。
> * 評価および移行のために Hyper-V ホストおよび VM を準備しました。

次のチュートリアルに進み、Azure Migrate プロジェクトを作成し、Azure に移行するために Hyper-V VM を評価します。

> [!div class="nextstepaction"] 
> [Hyper-V VM を評価する](./tutorial-assess-hyper-v.md) 
