---
title: Azure Migrate を使用した評価と移行に向けて物理サーバーを準備する
description: Azure Migrate を使用した評価と移行に向けて物理サーバーを準備する方法について説明します。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aba1608c9219e7e8dffe66344b04fa3f085b06f3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927376"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>物理サーバーの評価および Azure への移行を準備する

この記事では、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの物理サーバーの評価および Azure への移行を準備する方法について説明します。

[Azure Migrate](./migrate-services-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 

これはシリーズの最初のチュートリアルであり、Azure Migrate を使用して物理サーバーを評価する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure を準備します。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * サーバー評価のために、オンプレミスの物理サーバーを準備します。


> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、物理サーバーの評価に関するハウツーを参照してください。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure-for-server-assessment"></a>サーバー評価のために Azure を準備します。

Azure Migrate と連携するように Azure を設定します。 

**タスク** | **詳細** 
--- | --- 
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するために共同作成者または所有者のアクセス許可が必要です。 
**リソースプロバイダーの登録 (評価のみ)** | Azure Migrate は、軽量な Azure Migrate アプライアンスを使用して、Azure Migrate:Server Assessment によってマシンを検出し、評価します。<br/><br/> アプライアンスの登録中、リソースプロバイダーはアプライアンスで選択されたサブスクリプションに登録されます。 [詳細については、こちらを参照してください](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。
**Azure AD アプリの作成 (評価のみ)** | アプライアンスを登録するとき、Azure Migrate によって、アプライアンス上で実行されているエージェントと Azure 上で実行されているそれぞれのサービスとの間の通信に使用される Azure Active Directory (Azure AD) アプリが作成されます。 [詳細については、こちらを参照してください](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> Azure AD アプリを作成するためのアクセス許可が必要です (アプリケーション開発者ロールで利用可能)。
**キー コンテナーの作成** | キー コンテナーはアプライアンスの登録の一部として作成され、その構成中にアプライアンスでダウンロードされた証明書の管理に使用されます。<br/><br/>Azure Migrate がキー コンテナーを作成できるようにするには、Azure アカウントにAzure Migrate プロジェクトが存在するリソース グループに対する共同作成者のアクセス許可が必要です。


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


## <a name="prepare-azure-for-physical-server-migration"></a>物理サーバーの移行に向けて Azure を準備する

Server Migration を使用して物理サーバーを移行する準備を Azure で行います。

**タスク** | **詳細**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するために共同作成者または所有者のアクセス許可が必要です。
**Azure アカウントのアクセス許可の確認** | ご使用の Azure アカウントには、VM を作成するためのアクセス許可と Azure マネージド ディスクへの書き込みアクセス許可が必要です。
**Azure ネットワークの作成** | Azure にネットワークを設定します。


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-azure-account-permissions"></a>Azure アカウントのアクセス許可を割り当てる

仮想マシン共同作成者ロールを Azure アカウントに割り当てます。 これで、次の作業を行うためのアクセス許可が得られます。
  - 選択したリソース グループ内に VM を作成する。
  - 選択した仮想ネットワーク内に VM を作成する。
  - Azure マネージド ディスクに書き込む。 

### <a name="create-an-azure-network"></a>Azure ネットワークを作成する

Azure 仮想ネットワーク (VNet) を[設定](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Azure にレプリケートすると、Azure VM が作成され、移行の設定時に指定した Azure VNet に参加させられます。


## <a name="prepare-for-physical-server-assessment"></a>物理サーバーの評価を準備する

物理サーバーの評価を準備するには、物理サーバーの設定を確認し、アプライアンスのデプロイの設定を確認する必要があります。

### <a name="verify-physical-server-settings"></a>物理サーバーの設定を確認する

1. サーバー評価のため、[物理サーバーの要件](migrate-support-matrix-physical.md#physical-server-requirements)を確認します。
2. 物理サーバー上で[必要なポート](migrate-support-matrix-physical.md#port-access)が開かれていることを確認します。


### <a name="verify-appliance-settings"></a>アプライアンスの設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. 物理サーバーのアプライアンスの要件を[確認](migrate-appliance.md#appliance---physical)します。
2. アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある Azure の URL について確認します。
3. 検出および評価中にアプライアンスによって収集されるデータを[確認](migrate-appliance.md#collected-data---vmware)します。
4. 物理サーバー評価のためのポート アクセスの要件に[注意](migrate-support-matrix-physical.md#port-access)します。


### <a name="set-up-an-account-for-physical-server-discovery"></a>物理サーバー検出用のアカウントを設定する

Azure Migrate には、オンプレミスのサーバーを検出するためのアクセス許可が必要です。

- **Windows:** ドメインに参加しているサーバーを検出するにはドメイン アカウントが、ドメインに参加していないマシンを検出するにはローカル アカウントが必要です。 次のグループにユーザー アカウントを追加する必要があります:リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー。
- **Linux:** 検出する Linux サーバーのルート アカウントが必要です。

## <a name="prepare-for-physical-server-migration"></a>物理サーバーの移行を準備する

物理サーバーの移行の要件を確認します。

> [!NOTE]
> 物理マシンを移行するとき、Azure Migrate:Server Migration では、Azure Site Recovery サービスのエージェントベースのディザスター リカバリーと同じレプリケーション アーキテクチャが使用されており、一部のコンポーネントでは、同じコード ベースが共有されています。 一部のコンテンツは、Site Recovery のドキュメントにリンクされている場合があります。

1. 移行のための物理サーバーの要件を[確認](migrate-support-matrix-physical-migration.md#physical-server-requirements)します。
2. Azure Migrate:Server Migration では、物理サーバーの移行にレプリケーション サーバーが使用されます。
    - レプリケーション アプライアンスのデプロイ要件と、アプライアンスでの MySQL のインストールの[オプション](migrate-replication-appliance.md#mysql-installation)を[確認](migrate-replication-appliance.md#appliance-requirements)します。
    - パブリック クラウドおよび政府機関向けクラウドにアクセスするレプリケーション アプライアンスに必要な [Azure URL](migrate-appliance.md#url-access) を確認します。
    - レプリケーション アプライアンスの[ポート](migrate-replication-appliance.md#port-access) アクセス要件を確認します。
3. Azure に VM を移行する前に、それらに対していくつかの変更を行う必要があります。
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
    - [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) で必要な変更を確認してください。



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アカウントのアクセス許可を設定しました。
> * 物理サーバーの評価を準備する

次のチュートリアルに進み、Azure Migrate プロジェクトを作成し、Azure に移行するために物理サーバーを評価します。

> [!div class="nextstepaction"]
> [物理サーバーを評価する](./tutorial-assess-physical.md)
