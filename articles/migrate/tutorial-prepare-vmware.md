---
title: Azure Migrate を使用して評価と Azure への移行のために VMware VM を準備する | Microsoft Docs
description: Azure Migrate を使用して、オンプレミスの VMware VM の評価および Azure への移行を準備する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840338"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>評価および Azure への移行のために VMware VM を準備する

この記事では、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの VMware VM の評価および Azure への移行を準備する方法について説明します。

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 


これはシリーズの最初のチュートリアルであり、VMware VM を評価して移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure を準備します。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * VM 評価用にオンプレミスの VMware サーバーと VM を準備します。
> * VM 移行用にオンプレミスの VMware サーバーと VM を準備します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、VMware の評価と移行に関するハウツーを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

これらの Azure アクセス許可が必要です。

- 評価および移行用の Azure Migrate プロジェクトを作成するためのアクセス許可が自分の Azure アカウントに必要です。 
- Azure Migrate は、VMware VM の評価とエージェントレス移行のために軽量アプライアンスを実行します。これが VM を検出し、VM メタデータとパフォーマンス データを Azure Migrate に送信します。 Azure では、Azure Migrate アプライアンスを登録するためのアクセス許可が必要です。
- Azure Migrate Server Migration を使用して VMware VM を移行するために、Azure Migrate によってリソース グループにキー コンテナーが作成され、ご自分のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーが管理されます。 コンテナーを作成するには、Azure Migrate プロジェクトが存在しているリソース グループに対するロールの割り当てアクセス許可が必要です。 


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

VM のエージェントレス移行を評価または実行するために Azure Migrate アプライアンスをデプロイする際には、それを登録する必要があります。

- アプライアンスの登録時に、Azure Migrate によって、アプライアンスを一意に識別する 2 つの Azure Active Directory (Azure AD) アプリが作成されます
    - 1 つ目のアプリは、Azure Migrate サービス エンドポイントと通信します。
    - 2 つ目のアプリは、登録時に作成された Azure キー コンテナーにアクセスし、Azure AD アプリ情報とアプライアンス構成設定を格納します。
- 次のいずれかの方法を使用して、これらの Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。
    - テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
    - テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

次の点に注意してください。

- アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
- 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。 


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細情報](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て 

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細情報](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

## <a name="assign-role-assignment-permissions"></a>ロール割り当てのアクセス許可の割り当て

次のように、Azure Migrate プロジェクトが存在しているリソース グループに対してロールの割り当てアクセス許可を割り当てます。

1. Azure portal で、リソース グループの **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。

    - サーバー評価を実行するには、**共同作成者**のアクセス許可で十分です。
    - エージェントレスのサーバー移行を実行するには、**所有者** (または**共同作成者**および**ユーザー アクセス管理者**) のアクセス許可が必要です。

3. 必要なアクセス許可がない場合は、リソース グループの所有者にそれらを依頼してください。 



## <a name="prepare-for-vmware-vm-assessment"></a>VMware VM の評価の準備

VMware VM の評価を準備するには、Hyper-V ホストと VM の設定を確認し、アプライアンスのデプロイの設定を確認する必要があります。

### <a name="verify-vmware-settings"></a>VMware の設定の確認

1. VM の評価に関する VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements)します。
2. 必要なポートが vCenter サーバー上で開かれていることを[確認](migrate-support-matrix-vmware.md#assessment-port-requirements)します。


### <a name="set-up-an-account-for-assessment"></a>評価のためのアカウントの設定

評価およびエージェントレス移行のための VM を検出するには、Azure Migrate が vCenter Server にアクセスする必要があります。 評価のためだけであれば、vCenter Server 用の読み取り専用アカウントが必要です。

URL ベースのファイアウォール プロキシを使用している場合は、必要な [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements) へのアクセスを許可します。

URL の検索中に受信するすべての CNAME レコードがプロキシによって解決されることを確認します。


### <a name="verify-appliance-settings-for-assessment"></a>評価用のアプライアンス設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. VMware で Azure Migrate アプライアンスを設定するための要件を[確認](migrate-support-matrix-vmware.md#assessment-appliance-requirements)します。
2. アプライアンスがアクセスする必要がある Azure URL を[確認](migrate-support-matrix-vmware.md#assessment-url-access-requirements)します。
3. 検出および評価中にアプライアンスによって収集されるデータを確認します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#assessment-port-requirements)します。
5. OVA ファイルを使用して、Azure Migrate アプライアンスを VMware VM としてデプロイします。 vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がアカウントにあることを確認します。


## <a name="prepare-for-agentless-vmware-migration"></a>エージェントレスの VMware 移行の準備

VMware VM のエージェントレス移行の要件を確認します。

1. エージェントレス移行に関する VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements)します。
2. エージェントレス移行のために[必要なアクセス許可](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions)を備えるように、vCenter Server にアクセスするアカウントを設定します。
3. エージェントレス移行を使用して Azure に移行する VMware VM の要件に[注意](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)します。
4. エージェントレス移行に関するアプライアンスの要件を[確認](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements)します。
5. エージェントレス移行に関するアプライアンスの [URL アクセス](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements)と[ポート アクセス](migrate-support-matrix-vmware.md#agentless-migration-port-requirements)の要件に注意します。


## <a name="prepare-for-agent-based-vmware-migration"></a>エージェントベースの VMware 移行の準備

VMware VM のエージェントベース移行の要件を確認します。

1. エージェントレス移行に関する VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements)します。 
2. エージェントレス移行のために[必要なアクセス許可](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions)を備えるように、vCenter Server にアクセスするアカウントを設定します。
3. エージェントベースの移行を使用して Azure に移行する VMware VM の要件 (移行する各 VM へのモビリティ サービスのインストールなど) に[注意](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)します。
4. [URL アクセス](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements)に注意します。
5. 各 VM 上で実行されているモビリティ サービスと、Azure Migrate 構成サーバーの[ポート アクセス](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements)要件を確認します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のことを行いました。
 
> [!div class="checklist"] 
> * Azure アクセス許可を設定しました。
> * 評価と移行のために VMware を準備しました。


2 番目のチュートリアルに進み、Azure Migrate プロジェクトを設定し、Azure に移行するために VMware VM を評価します。

> [!div class="nextstepaction"] 
> [VMware VM の評価](./tutorial-migrate-vmware.md) 

