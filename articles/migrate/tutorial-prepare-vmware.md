---
title: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する
description: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する方法について説明します。
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927308"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>評価および Azure への移行のために VMware VM を準備する

この記事は、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの VMware VM の評価および Azure への移行を準備する場合に役立ちます。

これはシリーズの最初のチュートリアルであり、VMware VM を評価して移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate と連携するように Azure を準備します。
> * Azure Migrate:Server Assessment ツールを使用した VMware VM の評価に向けて準備します。
> * Azure Migrate:Server Migration ツールを使用した VMware VM の移行に向けて準備します。 

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 これらは、簡単な概念実証として役立ちます。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

この表は、Azure で実行する必要があるタスクの概要を示しています。 この表の下に、各タスクの手順を示します。

**タスク** | **詳細** | **アクセス許可**
--- | --- | ---
**Azure Migrate プロジェクトの作成** | Azure Migrate プロジェクトは、Azure Migrate ツール、Microsoft ツール、およびサードパーティのオファリングを使用して、評価と移行を調整および管理するための一元的な場所を提供します。 | お使いの Azure アカウントには、プロジェクトが存在するリソース グループの共同作成者または所有者のアクセス許可が必要です。
**アプライアンスの登録** | Azure Migrate は、軽量の Azure Migrate アプライアンスを使用して VM を検出し、Server Assessment ツールでそれらを評価し、Server Migration ツールでエージェントレス移行を使用してそれらを移行します。 登録について詳しくは[こちら](migrate-appliance-architecture.md#appliance-registration)をご覧ください。 | アプライアンスを登録するには、お使いの Azure アカウントに Azure サブスクリプションに対する共同作成者または所有者のアクセス許可が必要です。
**Azure AD アプリの作成** | アプライアンスの登録時に、Azure Migrate では Azure Active Directory (Azure AD) アプリが 2 つ作成されます。 <br/><br/> - 最初のアプリは、アプライアンスで実行されているエージェントと Azure Migrate との間の通信に使用されます。 <br/><br/> - 2 番目のアプリは、VMware VM のエージェントレス移行の目的でユーザーのサブスクリプション内に作成されたキー コンテナーにアクセスするためにのみ使用されます。   | Azure アカウントには、Azure AD アプリを作成するためにこれらの[アクセス許可](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps)が必要です。
**キー コンテナーの作成** | - 最初のキー コンテナーはアプライアンスの登録の一部として作成され、その構成中にアプライアンスでダウンロードされた証明書の管理に使用されます。 <br/><br/> \- エージェントレス移行を使用して VMware VM を移行するために、ご利用のサブスクリプションのレプリケーション アカウントへのアクセス キーを管理するための別のキー コンテナーが Azure Migrate によって作成されます。| Azure Migrate がキー コンテナーを作成できるようにするには、Azure Migrate プロジェクトが存在するリソース グループに、アクセス許可 (所有者、または共同作成者、およびユーザー アクセス管理者) を設定する必要があります。


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Azure AD アプリを作成するためのアクセス許可を割り当てる

アプライアンスを登録するには、お使いの Azure アカウントに Azure AD アプリを作成するためのアクセス許可が必要です。 次のいずれかの方法を使用して、アクセス許可を割り当てます。

- **方法 1: アカウントにアクセス許可を付与する**:テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザー アカウントに付与できます。
- **方法 2: アクセス許可を持つロールをユーザーアカウントに割り当てる**:テナントおよびグローバル管理者は、(必要なアクセス許可が含まれている) アプリケーション開発者ロールをユーザー アカウントに割り当てることができます。

> [!NOTE]
> 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="method-1-grant-permissions-to-the-account"></a>方法 1:アカウントにアクセス許可を付与する

アカウントにアクセス許可を付与するには、次の手順を実行します。

1. 自分がテナントまたは全体管理者であることを確認します。その後、Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. **[アプリの登録]** を **[はい]** に設定します。 これは、重要ではない既定の設定です。 [詳細については、こちらを参照してください](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>方法 2:アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることもできます。 ロールの割り当てについての[詳しい情報](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)をご覧ください。

### <a name="assign-permissions-to-create-a-key-vault"></a>キー コンテナーを作成するためのアクセス許可を割り当てる

Azure Migrate でキー コンテナーを作成できるようにするには、次のようにアクセス許可を割り当てます。

1. Azure portal で、リソース グループの **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。

    - サーバー評価を実行するには、**共同作成者**のアクセス許可で十分です。
    - エージェントレスのサーバー移行を実行するには、**所有者** (または**共同作成者**および**ユーザー アクセス管理者**) のアクセス許可が必要です。

3. 必要なアクセス許可がない場合は、リソース グループの所有者にそれらを依頼してください。

## <a name="prepare-for-assessment"></a>評価の準備

VMware VM の評価を準備するには、以下が必要です。

1. **VMware の設定を確認します**。 移行する vCenter Server と VM が要件を満たしていることを確認します。
2. **評価のためのアクセス許可を設定します**。 Azure Migrate は、vCenter アカウントを使用して vCenter Server にアクセスし、VM を検出して評価します。
3. **アプライアンスの要件を確認します**。 次のチュートリアルで Azure Migrate アプライアンスをデプロイする前に、そのデプロイ要件を確認します。

### <a name="verify-vmware-settings"></a>VMware の設定の確認

1. 評価に関する [VMware の要件を確認](migrate-support-matrix-vmware.md#vmware-requirements)します。
2. 必要なポートが vCenter サーバー上で開かれていることを[確認](migrate-support-matrix-vmware.md#port-access-requirements)します。
3. vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がお使いのアカウントにあることを確認します。 これは、OVA ファイルを使用して Azure Migrate アプライアンスを VMware VM としてデプロイする場合に必要です。
4. Azure に VM を移行する前に、それらに対していくつかの変更を行う必要があります。

    - 一部のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。 
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
    - 行う必要がある [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) の変更を確認してください。


### <a name="set-up-permissions-for-assessment"></a>評価のためのアクセス許可を設定する

Azure Migrate アプライアンスが評価およびエージェントレス移行のための VM を検出できるように、Azure Migrate が vCenter Server にアクセスする必要があります。 次のようにアカウントを設定します。

1. vSphere Web Client で、 **[管理]**  >  **[アクセス]**  >  **[SSO Users and Groups]\(SSO ユーザーとグループ\)** を開きます。
2. **[ユーザー]** で、 **[New User]\(新しいユーザー\)** アイコンをクリックします。
3. 新しいユーザーの詳細を入力します。
4. この表の値に従ってアクセス許可を選択します。

    **機能** | **アカウントのアクセス許可**
    --- | ---
    [VM の評価](tutorial-assess-vmware.md) | 評価を行うには、アカウントに読み取り専用アクセス権が必要です。
    [VM アプリ、ロール、および機能の検出](how-to-discover-applications.md) | アプリ検出を使用する場合は、評価に使用する読み取り専用アカウントに **[仮想マシン]**  >  **[ゲスト操作]** に対して有効になっている特権が必要です。
    [VM の依存関係の分析 (エージェントレス)](how-to-create-group-machine-dependencies-agentless.md) | 依存関係を分析する場合は、評価に使用する読み取り専用アカウントに **[仮想マシン]**  >  **[ゲスト操作]** に対して有効になっている特権が必要です。
    
> [!NOTE]
> 評価のための VM の検出を特定のスコープに制限する場合は、[この記事](set-discovery-scope.md#assign-a-role-for-assessment)を参照してください。

### <a name="verify-appliance-settings-for-assessment"></a>評価用のアプライアンス設定の確認

[次のチュートリアル](tutorial-assess-vmware.md)では、Azure Migrate アプライアンスを設定し、評価を開始します。 これを行う前に、次のようにアプライアンスの要件を確認してください。 

1. Azure Migrate アプライアンスを展開するための[要件を確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある Azure の URL について確認します。
3. アプライアンスによって使用されるポートに[注意](migrate-support-matrix-vmware.md#port-access-requirements) します。
4. 検出および評価中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。

## <a name="prepare-for-agentless-vmware-migration"></a>エージェントレスの VMware 移行の準備

[エージェントレスまたはエージェントベース移行](server-migrate-overview.md)を使用して VMware VM を移行できます。 このセクションでは、エージェントレス移行の要件について概要を説明します。

1. エージェントレス移行を使用するかどうかを[決定](server-migrate-overview.md#compare-migration-methods)します。
2. 移行するマシンのハイパーバイザーの要件を[確認](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)します。
3. エージェントレス移行を使用して移行する VMware VM の要件を[確認](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)します。
4. エージェントレス移行に関する Azure Migrate アプライアンスの要件を[確認](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless)します。
5. [パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドに必要な URL アクセスをメモします。
6. [ポートのアクセス](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)要件を確認します。
7. 次の手順の説明に従って、エージェントレス移行のアクセス許可を設定します。


### <a name="assign-permissions-to-an-account"></a>アカウントにアクセス許可を割り当てる

Azure Migrate アプライアンスは、エージェントレス移行で VM を検出して移行するために vCenter Server に接続します。 アプライアンスが必要とするアクセス許可をユーザー アカウントに割り当てるか、アクセス許可を持つロールを作成し、そのロールをユーザー アカウントに割り当てることができます。

1. vSphere Web Client で、 **[管理]**  >  **[アクセス]**  >  **[SSO Users and Groups]\(SSO ユーザーとグループ\)** を開きます。
2. **[ユーザー]** で、 **[New User]\(新しいユーザー\)** アイコンをクリックします。
3. 新しいユーザーの詳細を入力します。
4. [これらのアクセス許可](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)を割り当てます。

#### <a name="create-a-role-and-assign-to-an-account"></a>ロールを作成してアカウントに割り当てる

または、アカウントを作成することもできます。 その後、次のように、ロールを作成してアカウントに割り当てます。

1. vCenter Server 管理者として vSphere Web Client にサインインします。
2. vCenter Server インスタンス > **ロールの作成** を選択します。
3. <em>Azure_Migrate</em> など、ロール名を指定し、[必要なアクセス許可](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)をロールに割り当てます。

    ![vCenter Server アカウントの権限](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. 次に、アカウントを作成し、そのアカウントにロールを割り当てます。

> [!NOTE]
> エージェントレス移行のための VM の検出を特定のスコープに制限する場合は、[この記事](set-discovery-scope.md#assign-a-role-for-agentless-migration)を参照してください。



## <a name="prepare-for-agent-based-vmware-migration"></a>エージェントベースの VMware 移行の準備

[エージェントレスまたはエージェントベース移行](server-migrate-overview.md)を使用して VMware VM を移行できます。 このセクションでは、エージェントベース移行の要件について概要を説明します。

1. エージェントベース移行を使用するかどうかを[決定](server-migrate-overview.md#compare-migration-methods)します。
1. 移行するマシンのハイパーバイザーの要件を[確認](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)します。
2. VMware VM の要件を[確認](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)します。これには、移行する各 VM への Mobility Service のインストールも含まれます。
3. エージェントベース移行では、レプリケーション アプライアンスを使用します。
    - レプリケーション アプライアンスのデプロイ要件を[確認](migrate-replication-appliance.md#appliance-requirements)します。
    - アプライアンスに MySQL をインストールするための[いくつかの方法を確認](migrate-replication-appliance.md#mysql-installation)します。
    - [パブリック](migrate-replication-appliance.md#url-access) クラウドと[政府機関向け](migrate-replication-appliance.md#azure-government-url-access)クラウドに必要な URL アクセスをメモします。
    - レプリケーション アプライアンスの[ポート アクセス](migrate-replication-appliance.md#port-access)要件を確認します。
4. Azure に VM を移行する前に、それらに対していくつかの変更を行う必要があります。 行う必要がある [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) の変更を確認してください。



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アクセス許可を設定しました。
> * 評価と移行のために VMware を準備しました。


2 番目のチュートリアルに進み、Azure Migrate プロジェクトを設定し、Azure に移行するために VMware VM を評価します。

> [!div class="nextstepaction"]
> [VMware VM の評価](./tutorial-assess-vmware.md)
