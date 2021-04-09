---
title: Azure Migrate での VMware 評価サポート
description: Azure Migrate Server Assessment を使用した VMware VM の評価のサポートについて説明します
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 7e0bc21fde2c030de7a836d82384c09c78d993ad
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047827"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評価のサポートマトリックス 

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、VMware 環境で実行されているサーバーを Azure に移行するために検出して評価する場合の前提条件とサポート要件について説明します。 サーバーを評価するには、Azure Migrate プロジェクトを作成し、そのプロジェクトに Server Assessment ツールを追加します。 ツールを追加した後、Azure Migrate アプライアンスをデプロイします。 アプライアンスでオンプレミス サーバーが継続的に検出され、構成とパフォーマンスのメタデータが Azure に送信されます。 検出が完了したら、検出されたサーバーをグループにまとめ、グループに対して評価を実行します。

VMware サーバーを Azure に移行する場合は、[移行のサポート マトリックス](migrate-support-matrix-vmware-migration.md)をご確認ください。



## <a name="limitations"></a>制限事項

**要件** | **詳細**
--- | ---
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。<br/><br/> 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で、VMware 環境にある VM を最大 50,000 台まで検出して評価することができます。 また 1 つのプロジェクトには、物理サーバーおよび Hyper-V 環境にあるサーバーを、評価の上限に達するまで含めることができます。
**検出** | Azure Migrate アプライアンスで、vCenter Server 上のサーバーを最大 10,000 台検出できます。
**評価** | 1 つのグループに最大 35,000 台のサーバーを追加できます。<br/><br/> 1 回の評価で最大 35,000 台のサーバーを評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。


## <a name="vmware-requirements"></a>VMware の要件

**VMware** | **詳細**
--- | ---
**vCenter Server** | 検出および評価対象のサーバーは、vCenter Server バージョン 5.5、6.0、6.5、6.7、または 7.0 で管理されている必要があります。<br/><br/> アプライアンスで ESXi ホストの詳細を指定することによるサーバーの検出は、現在サポートされていません。
**アクセス許可** | Server Assessment には、検出および評価用に vCenter Server の読み取り専用アカウントが必要です。<br/><br/> インストールされているアプリケーションの検出とエージェントレスの依存関係の分析を実行する場合は、 **[Virtual Machines]**  >  **[Guest Operations]\(ゲスト操作\)** でそのアカウントの特権が有効になっている必要があります。

## <a name="server-requirements"></a>サーバーの要件
**VMware** | **詳細**
--- | ---
**サポートされている OS** | すべての Windows および Linux オペレーティング システムを、移行のために評価することができます。
**Storage** | SCSI、IDE、および SATA ベースのコントローラーに接続されているディスクがサポートされています。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 アプライアンスを VMware 環境内のサーバーとしてデプロイするには、OVA テンプレートを使用するか、vCenter Server にインポートするか、[PowerShell スクリプト](deploy-appliance-script.md)を使用します。

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- Azure Government では、[スクリプトを使用して](deploy-appliance-script-government.md)アプライアンスをデプロイする必要があります。
- アプライアンスが[パブリック](migrate-appliance.md#public-cloud-urls)および [Government](migrate-appliance.md#government-cloud-urls) クラウドでアクセスする必要がある URL について確認します。


## <a name="port-access-requirements"></a>ポートのアクセス要件

**[デバイス]** | **接続**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ```https://<appliance-ip-or-name>:44368``` <br/><br/>ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**vCenter サーバー** | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合、検出の設定時にポートを変更できます。
**ESXi ホスト** | [インストールされているアプリケーションの検出](how-to-discover-applications.md)または[エージェントレスの依存関係の分析](concepts-dependency-visualization.md#agentless-analysis)を実行する場合は、アプライアンスが TCP ポート 443 上の ESXi ホストに接続されると、サーバー上のアプリケーションと依存関係が検出されます。

## <a name="application-discovery-requirements"></a>アプリケーションの検出の要件

サーバーの検出に加えて、サーバー上で実行されているアプリケーション、ロール、機能の検出ができます。 アプリケーションを検出することで、オンプレミスのワークロードに合わせて調整された移行パスを特定し、計画することができます。

**サポート** | **詳細**
--- | ---
**サポートされているサーバー** | 現在、VMware 環境内のサーバーに対してのみサポートされています。 アプリケーションの検出は、各 Azure Migrate アプライアンスから最大 10,000 台のサーバーに対して実行できます。
**オペレーティング システム** | すべてのバージョンの Windows および Linux を実行しているサーバーがサポートされています。
**VM 要件** | インストールされているアプリケーションの検出を実行するには、VMware Tools がサーバーにインストールされ、実行されている必要があります。 <br/><br/> VMware ツールのバージョンは、10.2.0 以降である必要があります。<br/><br/> Windows サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**検出** | サーバー上のアプリケーション検出は、サーバーにインストールされている VMware Tools を使用して、vCenter Server から実行されます。 vSphere API を使用して、インストールされているアプリケーションについての情報がアプライアンスによって vCenter Server から収集されます。 アプリケーションの検出はエージェントレスです。 サーバーにエージェントはインストールされず、アプライアンスはサーバーに直接接続されません。 Windows および Linux サーバーで、WMI と SSH がそれぞれ有効化され、使用可能になっている必要があります。
**vCenter Server ユーザー アカウント** | 評価に使用される vCenter Server の読み取り専用アカウントには、アプリケーション検出の対象であるサーバーとやりとりするために、 **[Virtual Machines]**  >  **[Guest Operations]\(ゲスト操作\)** に対して有効になっている特権が必要です。
**サーバー アクセス** | アプリケーション検出のために、アプライアンス構成マネージャーにドメインおよびドメイン以外 (Windows または Linux) の複数の資格情報を追加することができます。<br/><br/> Windows サーバー用にゲスト ユーザー アカウントと、すべての Linux サーバー用に通常または標準ユーザー アカウント (非 sudo アクセス) が必要です。
**ポート アクセス** | アプリケーション検出を実行する対象のサーバーが実行されている ESXi ホスト上で、Azure Migrate アプライアンスから TCP ポート 443 への接続ができる必要があります。 インストールされているアプリケーションの詳細を含むファイルをダウンロードするために、vCenter Server から ESXi ホスト接続が返されます。

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>SQL Server インスタンスとデータベースを検出するための要件

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

[アプリケーション検出](how-to-discover-applications.md)によって SQL Server インスタンスが識別されます。 この情報を使用し、アプライアンス上で提供される Windows 認証または SQL Server 認証の資格情報を介して、アプライアンスからそれぞれの SQL Server インスタンスへの接続が試みられます。 接続後、アプライアンスにより SQL Server インスタンスと SQL Server データベースの構成とパフォーマンスのデータが収集されます。 SQL Server の構成データは 24 時間ごとに更新されます。パフォーマンス データは 30 秒ごとにキャプチャされます。

**サポート** | **詳細**
--- | ---
**サポートされているサーバー** | 現在、VMware 環境内の SQL Server に対してのみサポートされています。 最大 300 個の SQL Server インスタンスまたは 6,000 個の SQL データベースのいずれか少ない方を検出できます。
**Windows サーバー** | Windows Server 2008 以降がサポートされています。
**Linux サーバー** | 現在サポートされていません。
**認証メカニズム** | Windows と SQL Server の両方の認証がサポートされています。 アプライアンス構成マネージャーで両方の種類の認証の資格情報を指定できます。
**SQL Server へのアクセス** | Azure Migrate には、sysadmin サーバー ロールのメンバーである Windows ユーザー アカウントが必要です。
**SQL Server のバージョン** | SQL Server 2008 以降がサポートされています。
**SQL Server のエディション** | Enterprise、Standard、Developer、Express の各エディションがサポートされています。
**サポートされている SQL 構成** | 現在、スタンドアロンの SQL Server インスタンスと対応するデータベースのみの検出がサポートされています。<br/> フェールオーバー クラスターと Always On 可用性グループの識別はサポートされていません。
**サポートされている SQL サービス** | SQL Server データベース エンジンのみがサポートされています。 <br/> SQL Server Reporting Services (SSRS)、SQL Server Integration Services (SSIS)、SQL Server Analysis Services (SSAS) の検出はサポートされていません。

> [!Note]
> Azure Migrate により、Azure Migrate アプライアンスとソース SQL Server インスタンスの間の通信が暗号化されます (Encrypt 接続プロパティが TRUE に設定されている場合)。 これらの接続は、[**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) で暗号化されます (TRUE に設定時)。トランスポート層は、SSL を使用してチャネルを暗号化し、証明書チェーンをバイパスして信頼を検証します。 アプライアンス サーバーは、[**証明書のルート証明機関を信頼**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)するように設定する必要があります。<br/>
サーバーの起動時に証明書がサーバーにプロビジョニングされていない場合、SQL Server は、ログイン パケットの暗号化に使用される自己署名証明書を生成します。 [**詳細情報**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

## <a name="dependency-analysis-requirements-agentless"></a>依存関係の分析の要件 (エージェントレス)

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのサーバー間の依存関係を特定できます。 この表は、エージェントレスの依存関係の分析を設定するための要件をまとめたものです。

**サポート** | **詳細**
--- | --- 
**サポートされているサーバー** | 現在、VMware 環境内のサーバーに対してのみサポートされています。
**Windows サーバー** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit)。<br/>Microsoft Windows Server 2008 (32 ビット)。
**Linux サーバー** | Red Hat Enterprise Linux 7、6、5<br/> Ubuntu Linux 14.04、16.04<br/> Debian 7、8<br/> Oracle Linux 6、7<br/> CentOS 5、6、7。<br/> SUSE Linux Enterprise Server 11 以降。
**サーバーの要件** | 分析するサーバーに VMware Tools (10.2.0 以降) がインストールされ、実行されている必要があります。<br/><br/> サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**検出方法** |  サーバー間の依存関係情報は、VM にインストールされている VMware Tools を使用して、vCenter Server から収集されます。 vSphere API を使用して、アプライアンスによって vCenter Server から情報が収集されます。 サーバーにエージェントはインストールされず、アプライアンスはサーバーに直接接続されません。 Windows および Linux サーバーで、WMI と SSH がそれぞれ有効化され、使用可能になっている必要があります。
**vCenter アカウント** | 評価のために Azure Migrate によって使用される読み取り専用アカウントには、 **[仮想マシン] > [ゲスト操作]** に対して有効になっている特権が必要です。
**Windows サーバーのアクセス許可** |  サーバーに対する管理権限を持つユーザー アカウント (ローカルまたはドメイン)。
**Linux アカウント** | ルート ユーザー アカウント、または /bin/netstat および /bin/ls のファイルに対して次のアクセス許可を持つアカウント。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。<br/><br/> これらの機能は次のコマンドで設定します。 <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
**ポート アクセス** | 依存関係を検出するサーバーを実行している ESXi ホスト上で、Azure Migrate アプライアンスから TCP ポート 443 に接続できる必要があります。 依存関係データを含むファイルをダウンロードするため、vCenter Server から ESXi ホスト接続が返されます。


## <a name="dependency-analysis-requirements-agent-based"></a>依存関係の分析の要件 (エージェント ベース)

[依存関係の分析](concepts-dependency-visualization.md)を使用すると、評価して Azure に移行するオンプレミスのサーバー間の依存関係を特定できます。 この表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。

**要件** | **詳細** 
--- | --- 
**デプロイ前** | Azure Migrate を使用して、Azure Migrate プロジェクトを配置しておく必要があります。Server Assessment ツールプロジェクトに追加して、Azure Migrate プロジェクトを配置する必要があります。<br/><br/>  オンプレミスのサーバーを検出するには、Azure Migrate アプライアンスをセットアップした後、依存関係の視覚化をデプロイします<br/><br/> 初めてプロジェクトを作成する方法については[こちら](create-manage-projects.md)を参照してください。<br/> 既存のプロジェクトに検出および評価ツールを追加する方法については、[こちら](how-to-assess.md)を参照してください。<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**サポートされているサーバー** | オンプレミス環境にあるすべてのサーバーに対してサポートされています。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)の [Service Map](../azure-monitor/insights/service-map.md) ソリューションを使用します。<br/><br/> 新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 プロジェクトのワークスペースは、追加後に変更することはできません。 <br/><br/> ワークスペースは、プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br/><br/> ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Log Analytics では、Azure Migrate に関連付けられたワークスペースは、移行プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各サーバーに次のエージェントをインストールします。<br/><br/> [Microsoft Monitoring エージェント (MMA)](../azure-monitor/platform/agent-windows.md)。<br/> [依存関係エージェント](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> オンプレミス サーバーがインターネットに接続されていない場合、Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。<br/><br/> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | このワークスペースは、Azure Migrate プロジェクトと同じサブスクリプションに含まれている必要があります。<br/><br/> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br/><br/>  ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br/><br/> Azure Migrate プロジェクトのワークスペースは、追加後に変更できません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br/><br/> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br/><br/> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br/><br/> Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層に応じて各ノードの料金が請求されます。<br/><br/>Azure Migrate の一般提供 (GA - 2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 GA の前の既存のワークスペースには引き続き課金されるため、180 日後にのみ支払いが発生するように、新しいプロジェクトを作成することをお勧めします。
**管理** | エージェントをワークスペースに登録する場合、Azure Migrate プロジェクトで提供される ID とキーを使用します。<br/><br/> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br/><br/> 関連付けられた Azure Migrate プロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/platform/manage-access.md)。<br/><br/> Azure Migrate プロジェクトを削除しない場合は、Azure Migrate で作成されたワークスペースは削除しないでください。 削除した場合は、依存関係可視化機能は、期待どおりに機能しません。
**インターネット接続** | サーバーがインターネットに接続されていない場合は、それらに Log Analytics ゲートウェイをインストールする必要があります。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。


## <a name="next-steps"></a>次のステップ

- 評価作成のベストプラクティスを[確認](best-practices-assessment.md)します。
- VMware 評価の[準備](./tutorial-discover-vmware.md)