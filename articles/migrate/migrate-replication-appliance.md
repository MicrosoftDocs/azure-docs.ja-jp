---
title: Azure Migrate レプリケーション アプライアンス
description: エージェントベースの VMWare の移行のための Azure Migrate レプリケーション アプライアンスについて説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754098"
---
# <a name="replication-appliance"></a>レプリケーション アプライアンス

この記事では、エージェントベースの移行を使用して、VMware VM、物理マシン、プライベートまたはパブリック クラウドの VM を Azure に移行する場合に、[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールによって使用されるレプリケーション アプライアンスについて説明します。 


## <a name="overview"></a>概要

レプリケーション アプライアンスは、VMware VM または物理サーバーのエージェントベースの移行を設定するときにデプロイされます。 それは、VMware VM または物理サーバーとして、単一のオンプレミス マシンとしてデプロイされます。 以下が実行されます。

- **レプリケーション アプライアンス**:レプリケーション アプライアンスは、Azure にレプリケートするオンプレミスの VMware VM と物理サーバーについて、通信を調整し、データ レプリケーションを管理します。
- **プロセス サーバー**:レプリケーション アプライアンス上に既定でインストールされるプロセス サーバーでは、以下の操作が行われます。
    - **レプリケーション ゲートウェイ**:レプリケーション ゲートウェイとして機能します。 レプリケーションが有効になっているマシンからレプリケーション データを受け取ります。 キャッシュ、圧縮、暗号化によってレプリケーション データを最適化し、Azure に送信します。
    - **エージェント インストーラー**:モビリティ サービスのプッシュ インストールが実行されます。 移行のためにレプリケートする各オンプレミス マシン上で、このサービスがインストールされ、実行されている必要があります。

## <a name="appliance-deployment"></a>アプライアンスのデプロイ

**用途** | **詳細**
--- |  ---
**VMware VM エージェントベースの移行** | OVA テンプレートを Azure Migrate ハブからダウンロードし、それを vCenter Server にインポートして、アプライアンス VM を作成します。
**物理マシン エージェントベースの移行** | VMware インフラストラクチャがない場合、または OVA テンプレートを使用して VMware VM を作成できない場合は、Azure Migrate ハブからソフトウェア インストーラーをダウンロードし、それを実行してアプライアンス マシンを設定します。

> [!NOTE]
> Azure Government でデプロイしている場合、インストール ファイルを使用し、レプリケーション アプライアンスをデプロイします。

## <a name="appliance-requirements"></a>アプライアンスの要件

Azure Migrate ハブで提供されている OVA テンプレートを使用してレプリケーション アプライアンスを設定すると、そのアプライアンスは Windows Server 2016 を実行し、サポート要件に準拠しています。 物理サーバーにレプリケーション アプライアンスを手動で設定する場合は、サーバーが要件に準拠していることを確認してください。

**コンポーネント** | **要件**
--- | ---
 | **VMware VM アプライアンス**
PowerCLI | VMware VM 上でレプリケーション アプライアンスが実行されている場合は、[PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) をインストールする必要があります。
NIC の種類 | VMXNET3 (アプライアンスが VMware VM である場合)
 | **ハードウェアの設定**
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | 3 (OS ディスク、プロセス サーバーのキャッシュ ディスク、リテンション ドライブ)
空きディスク領域 (キャッシュ) | 600 GB
ディスクの空き領域 (リテンション ディスク) | 600 GB
**ソフトウェアの設定** |
オペレーティング システム | Windows Server 2016 または Windows Server 2012 R2
ライセンス | アプライアンスには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。<br/><br/> 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
オペレーティング システムのロケール | 英語 (en-us)
TLS | TLS 1.2 を有効にする必要があります。
.NET Framework | .NET Framework 4.6 以降が (強力な暗号が有効にされた) マシンにインストールされている必要があります。
MySQL | MySQL をアプライアンスでインストールする必要があります。<br/> MySQL をインストールする必要があります。 手動でインストールするか、アプライアンスのデプロイ中に Site Recovery でインストールすることができます。
その他のアプリ | レプリケーション アプライアンスで他のアプリを実行しないでください。
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10))を有効にする <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定を有効にする
**ネットワーク設定** |
IP アドレスの種類 | 静的
Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)
NIC の種類 | VMXNET3

## <a name="mysql-installation"></a>MySQL のインストール 

MySQL は、レプリケーション アプライアンス マシンにインストールする必要があります。 これらの方法のいずれかを使用してインストールできます。

**方法** | **詳細**
--- | ---
手動でダウンロードしてインストールする | MySQL アプリケーションをダウンロードし、C:\Temp\ASRSetup フォルダー内に配置してから手動でインストールします。<br/> アプライアンスを設定すると、MySQL が既にインストールされているものとして表示されます。
オンライン ダウンロードなし | MySQL インストーラー アプリケーションを C:\Temp\ASRSetup フォルダーに配置します。 アプライアンスをインストールし、MySQL をダウンロードおよびインストールするようクリックすると、追加したインストーラーが設定で使用されます。
Azure Migrate でダウンロードしてインストールする | アプライアンスをインストールし、MySQL を求められたら、 **[ダウンロードしてインストール]** を選択します。

## <a name="url-access"></a>URL アクセス

レプリケーション アプライアンスは、Azure パブリック クラウドで次の URL にアクセスできる必要があります。

**URL** | **詳細**
--- | ---
\*.backup.windowsazure.com | レプリケートされたデータの転送と調整に使用
\*.store.core.windows.net | レプリケートされたデータの転送と調整に使用
\*.blob.core.windows.net | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用
\*.hypervrecoverymanager.windowsazure.com | レプリケーション管理操作と調整に使用
https:\//management.azure.com | レプリケーション管理操作と調整に使用
*.services.visualstudio.com | テレメトリの目的で使用 (省略可能)
time.windows.com | システム時刻とグローバル時刻間の時刻同期の確認に使用。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | アプライアンス設定では、次の URL にアクセスできる必要があります。 Azure Active Directory によるアクセス制御と ID 管理に使用されます。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL のダウンロードを完了するためのものです。 いくつかのリージョンでは、ダウンロードが CDN URL にリダイレクトされる可能性があります。 必要に応じて、CDN URL も確実に許可されているようにしてください。


## <a name="azure-government-url-access"></a>Azure Government URL アクセス

レプリケーション アプライアンスは、Azure Government でこれらの URL にアクセスできる必要があります。

**URL** | **詳細**
--- | ---
\*.backup.windowsazure.us | レプリケートされたデータの転送と調整に使用
\*.store.core.windows.net | レプリケートされたデータの転送と調整に使用
\*.blob.core.windows.net | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用
\*.hypervrecoverymanager.windowsazure.us | レプリケーション管理操作と調整に使用
https:\//management.usgovcloudapi.net | レプリケーション管理操作と調整に使用
*.services.visualstudio.com | テレメトリの目的で使用 (省略可能)
time.nist.gov | システム時刻とグローバル時刻間の時刻同期の確認に使用。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVA を利用するアプライアンス設定は、次の URL にアクセスできる必要があります。 Azure Active Directory によるアクセス制御と ID 管理に使用されます。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL のダウンロードを完了するためのものです。 いくつかのリージョンでは、ダウンロードが CDN URL にリダイレクトされる可能性があります。 必要に応じて、CDN URL も確実に許可されているようにしてください。

## <a name="port-access"></a>ポート アクセス

**[デバイス]** | **接続**
--- | ---
VM | VM 上で実行される Mobility Service は、レプリケーション管理のために、インバウンド ポート HTTPS 443 でオンプレミスのレプリケーション アプライアンス (構成サーバー) と通信します。<br/><br/> VM は、受信ポート HTTPS 9443 でレプリケーション データを (構成サーバー マシン上で実行されている) プロセス サーバーに送信します。 このポートは変更可能です。
レプリケーション アプライアンス | レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
プロセス サーバー | プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、アウトバウンド ポート 443 経由で Azure ストレージに送信します。<br/> 既定では、プロセス サーバーはレプリケーション アプライアンスで実行されます。


## <a name="replication-process"></a>レプリケーション プロセス

1. VM に対してレプリケーションを有効にした場合、指定したレプリケーション ポリシーを使用して、Azure Storage への最初のレプリケーションが開始されます。 
2. トラフィックは、インターネット経由で Azure Storage のパブリック エンドポイントにレプリケートされます。 オンプレミス サイトから Azure へのサイト間仮想プライベート ネットワーク (VPN) を介したトラフィックのレプリケートはサポートされていません。
3. 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 マシンの追跡された変更はログに記録されます。
4. 通信は、次のように行われます。
    - VM は、レプリケーション管理のために、受信ポート HTTPS 443 上でレプリケーション アプライアンスと通信します。
    - レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
    - VM は、受信ポート HTTPS 9443 上でプロセス サーバーにレプリケーション データを送信します (プロセス サーバーは、レプリケーション アプライアンス上で実行されます)。 このポートは変更可能です。
    - プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、アウトバウンド ポート 443 経由で Azure ストレージに送信します。
5. レプリケーション データ ログは、まず Azure のキャッシュ ストレージ アカウントに記録されます。 これらのログは処理され、データは Azure マネージド ディスクに格納されます。

![図はレプリケーション プロセスのアーキテクチャを示しています。](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスは、Azure Migrate ハブから手動でアップグレードされます。 常に最新バージョンを実行することをお勧めします。

1. [Azure Migrate] > [サーバー] > [Azure Migrate:Server Assessment]、[インフラストラクチャ サーバー] で、 **[構成サーバー]** をクリックします。
2. **[構成サーバー]** では、新しいバージョンのレプリケーション アプライアンスが使用可能な場合に、 **[エージェントのバージョン]** にリンクが表示されます。 
3. インストーラーをレプリケーション アプライアンス マシンにダウンロードし、アップグレードをインストールします。 インストーラーによって、アプライアンス上で現在実行中のバージョンが検出されます。
 
## <a name="next-steps"></a>次のステップ

- エージェントベースの VMware VM の移行のためのレプリケーション アプライアンスを設定する[方法を確認](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance)します。
- 物理サーバーのためのレプリケーション アプライアンスを設定する[方法を確認](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)します。
