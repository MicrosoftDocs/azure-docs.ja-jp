---
title: Azure Migrate アプライアンスのアーキテクチャ
description: サーバーの評価と移行に使用される Azure Migrate アプライアンスの概要について説明します。
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: a83e044acc329572a5f3bfd4856f90379319ba1d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919745"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate アプライアンスのアーキテクチャ

この記事では、Azure Migrate アプライアンスのアーキテクチャとプロセスについて説明します。 Azure Migrate アプライアンスは、オンプレミスに展開された軽量のアプライアンスで、Azure に移行するための VM と物理サーバーを検出します。 

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途** 
--- | --- | ---
**VMware VM の評価** | Azure Migrate:Server Assessment | VMware VM を検出する。<br/><br/> マシンのアプリと依存関係を検出する。<br/><br/> マシン メタデータとパフォーマンス メタデータを収集して Azure に送信する。
**VMware VM の移行 (エージェントレス)** | Azure Migrate:Server Migration | VMware VM を検出する<br/><br/>  [エージェントレス移行](server-migrate-overview.md)で VMware VM をレプリケートする。
**Hyper-V VM の評価** | Azure Migrate:Server Assessment | Hyper-V VM を検出する。<br/><br/> マシン メタデータとパフォーマンス メタデータを収集して Azure に送信する。
**物理マシン** |  Azure Migrate:Server Assessment |  物理サーバーを検出する。<br/><br/> マシン メタデータとパフォーマンス メタデータを収集して Azure に送信する。

## <a name="appliance-components"></a>アプライアンスのコンポーネント

アプライアンスには多数のコンポーネントがあります。

- **管理アプリ**:これは、アプライアンスの展開中にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。
- **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。 
- **コレクター エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。
- **DRA エージェント**:VM のレプリケーションを調整し、レプリケートされたマシンと Azure 間の通信を調整します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。
- **ゲートウェイ**: レプリケートされたデータを Azure に送信します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。
- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。



## <a name="appliance-deployment"></a>アプライアンスのデプロイ

- Azure Migrate アプライアンスは、[Hyper-V](how-to-set-up-appliance-hyper-v.md) または [VMware](how-to-set-up-appliance-vmware.md) 用のテンプレートを使用するか、[VMware/Hyper-V](deploy-appliance-script.md) または[物理サーバー](how-to-set-up-appliance-physical.md)の PowerShell スクリプト インストーラーを使用して設定できます。 
- アプライアンスのサポート要件と展開の前提条件の概要については、[アプライアンスのサポート マトリックス](migrate-appliance.md)を参照してください。


## <a name="appliance-registration"></a>アプライアンスの登録

アプライアンスのセットアップ中に、Azure Migrate にアプライアンスを登録すると、表にまとめられている操作が実行されます。

**操作** | **詳細** | **アクセス許可**
--- | --- | ---
**ソース プロバイダーの登録** | これらのリソース プロバイダーは、アプライアンスのセットアップ時に選択した次のサブスクリプションに登録されます。Microsoft.OffAzure、Microsoft.Migrate、および Microsoft.KeyVault。<br/><br/> リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 | リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。
**Azure AD アプリ通信を作成する** | Azure Migrate によって、アプライアンス上で実行されているエージェントと Azure 上で実行されているそれぞれのサービスとの間の通信 (認証と承認) に使用される Azure Active Directory (Azure AD) アプリが作成されます。<br/><br/> このアプリには、任意のリソースに対して Azure Resource Manager 呼び出しや RBAC アクセスを行うための特権はありません。 | Azure Migrate でアプリを作成するには、[これらのアクセス許可](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps)が必要です。
**Azure AD アプリの作成 - Key Vault** | このアプリは、VMware VM を Azure にエージェントレスで移行する場合にのみ作成されます。<br/><br/> エージェントレス移行の目的でユーザーのサブスクリプション内に作成されたキー コンテナーにアクセスするためにのみ使用されます。<br/><br/> アプライアンスから検出が開始されると、(顧客のテナント内で作成された) Azure キー コンテナーで RBAC アクセスが可能になります。 | Azure Migrate でアプリを作成するには、[これらのアクセス許可](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)が必要です。



## <a name="collected-data"></a>収集されるデータ

すべての展開シナリオでクライアントによって収集されるデータの概要については、[アプライアンスのサポート マトリックス](migrate-appliance.md)を参照してください。

## <a name="discovery-and-collection-process"></a>検出と収集のプロセス

![Architecture](./media/migrate-appliance-architecture/architecture.png)

アプライアンスは、次のプロセスを使用して、vCenter サーバーおよび Hyper-V ホスト/クラスターと通信します。

1. **検出を開始する**:
    - Hyper-V アプライアンスで検出を開始すると、WinRM ポート 5985 (HTTP) および 5986 (HTTPS) で Hyper-V ホストとの通信が行われます。
    - VMware アプライアンスで検出を開始すると、既定では TCP ポート 443 で vCenter サーバーとの通信が行われます。 vCenter サーバーが別のポートでリッスンしている場合は、それをアプライアンス Web アプリで構成できます。
2. **メタデータとパフォーマンス データを収集する**:
    - アプライアンスは、Common Information Model (CIM) セッションを使用して、ポート 5985 および 5986 で Hyper-V ホストから Hyper-V VM のデータを収集します。
    - アプライアンスは、既定ではポート 443 を使用して通信を行い、vCenter サーバーから VMware VM のデータを収集します。
3. **データを送信する**:アプライアンスは、SSL ポート 443 を介して、収集したデータを Azure Migrate Server Assessment と Azure Migrate Server Migration に送信します。 アプライアンスはインターネット経由で Azure に接続できます。または、ExpressRoute をパブリックまたは Microsoft ピアリングで使用できます。
    - パフォーマンス データの場合、アプライアンスはリアルタイムの使用状況データを収集します。
        - パフォーマンス データは、各パフォーマンス メトリックについて、VMware では 20 秒ごとに収集され、Hyper-V では 30 秒ごとに収集されます。
        - 収集されたデータは、10 分間に 1 つのデータ ポイントを作成するためにロールアップされます。
        - ピーク時の使用率の値は、すべての 20 または 30 秒のデータ ポイントから選択され、評価計算のために Azure に送信されます。
        - 評価のプロパティで指定されたパーセンタイル値 (50/90/95/99) に基づいて、10 分間のポイントが昇順に並べ替えられ、適切なパーセンタイル値を使用して評価が計算されます。
    - Server Migration の場合、アプライアンスは VM データの収集を開始し、それを Azure にレプリケートします。
4. **評価および移行する**:これで、Azure Migrate Server Assessment を使用して、アプライアンスによって収集されたメタデータから評価を作成できます。 また、Azure Migrate Server Migration を使用して VMware VM の移行を開始し、エージェントレスの VM レプリケーションを調整することもできます。





## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate エージェントが更新されると、アプライアンスがアップグレードされます。 アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。 エージェントを手動で更新するために、この既定の設定を変更できます。

レジストリで自動更新をオフにするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance の "AutoUpdate" キーを 0 (ダブルワード) に設定します。

 

## <a name="next-steps"></a>次のステップ

アプライアンスのサポート マトリックスを[確認](migrate-appliance.md)します。

