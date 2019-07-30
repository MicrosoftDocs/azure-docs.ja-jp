---
title: Azure Migrate を使用して評価と Azure への移行のために Hyper-V VM を準備する | Microsoft Docs
description: Azure Migrate を使用して、Hyper-V VM の評価および Azure への移行を準備する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840382"
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
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、Hyper-V の評価と移行に関するハウツーを参照してください。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

### <a name="azure-permissions"></a>Azure のアクセス許可

Azure Migrate をデプロイするには、いくつかのアクセス許可が必要です。

- 評価および移行用の Azure Migrate プロジェクトを作成するためのアクセス許可が自分の Azure アカウントに必要です。 
- Azure Migrate アプライアンスを登録するためのアクセス許可が自分の Azure アカウントに必要です。
    - Azure Migrate では評価のために軽量アプライアンスを実行します。これが Hyper-V VM を検出し、VM メタデータとパフォーマンス データを Azure Migrate に送信します。
    - アプライアンスの登録時に、Azure Migrate によって、アプライアンスを一意に識別する 2 つの Azure Active Directory (Azure AD) アプリが作成されます。
        - 1 つ目のアプリは、Azure Migrate サービス エンドポイントと通信します。
        - 2 つ目のアプリは、登録時に作成された Azure キー コンテナーにアクセスし、Azure AD アプリ情報とアプライアンス構成設定を格納します。
    - 次のいずれかの方法を使用して、これらの Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。
        - テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
        - テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。
    - 次の点に注意してください。
        - アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
        - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。 


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

Azure Migrate プロジェクトを作成するためのアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

VM を評価する Azure Migrate アプライアンスをデプロイする際には、それを登録する必要があります。

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

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細情報](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て 

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細情報](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-for-hyper-v-assessment"></a>Hyper-V の評価の準備

Hyper-V の評価を準備するには、Hyper-V ホストと VM の設定を確認し、アプライアンスのデプロイの設定を確認します。

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

VM ディスクが SMB 共有上にある場合は、関連するすべての Hyper-V ホストでこの手順を完了します。 この手順は、SMB 共有上にディスクがある Hyper-V VM について構成情報を検出するために使用されます。 SMB 共有上に VM ディスクがない場合は、この手順を省略できます。

1. SMB 共有上にディスクがある Hyper-V VM を実行している Hyper-V ホストを特定します。
2. 特定された各 Hyper-V ホスト上で、次のコマンドを実行します。

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- CredSSP 認証を使用すると、Hyper-V ホストは Azure Migrate クライアントの代わりに資格情報を委任できます。
- このコマンドは、すべての Hyper-V ホスト上でリモートで実行できます。
- クラスターに新しいホスト ノードを追加すると、自動的に検出用に追加されますが、必要に応じて新しいノード上で CredSSP を手動で有効にする必要があります。

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

- 検出と評価を行う VM については、各 VM で [Hyper-V 統合サービス](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)を有効にします。 

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
