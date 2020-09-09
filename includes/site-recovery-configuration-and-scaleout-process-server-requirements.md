---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 60012f79c3c04a4ff14c4a7f0609b6940d3402c4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544106"
---
**構成およびプロセス サーバーの要件**


## <a name="hardware-requirements"></a>ハードウェア要件

**コンポーネント** | **要件** 
--- | ---
CPU コア数 | 8 
RAM | 16 GB
ディスクの数 | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます 
ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
ディスクの空き領域 (リテンション ディスク) | 600 GB
 | 

## <a name="software-requirements"></a>ソフトウェア要件

**コンポーネント** | **要件** 
--- | ---
オペレーティング システム | Windows Server 2012 R2 <br> Windows Server 2016
オペレーティング システムのロケール | 英語 (en-*)
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V 
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする 
FIPS (連邦情報処理標準) | FIPS モードを有効にしない
|

## <a name="network-requirements"></a>ネットワークの要件

**コンポーネント** | **要件** 
--- | --- 
IP アドレスの種類 | 静的 
Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送) 
NIC の種類 | VMXNET3 (構成サーバーが VMware VM である場合)
 |
**インターネット アクセス** (サーバーは、直接またはプロキシ経由で次の URL にアクセスする必要があります):|
\*.backup.windowsazure.com | レプリケートされたデータの転送と調整に使用
\*.blob.core.windows.net | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用。 キャッシュ ストレージ アカウントの特定の URL を指定できます。
\*.hypervrecoverymanager.windowsazure.com | レプリケーション管理操作と調整に使用
https:\//login.microsoftonline.com | レプリケーション管理操作と調整に使用 
time.nist.gov | システム時刻とグローバル時刻間の時刻同期の確認に使用
time.windows.com | システム時刻とグローバル時刻間の時刻同期の確認に使用
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (省略可能) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF の設定では、次の追加 URL にアクセスできる必要があります。 Azure Active Directory によるアクセス制御と ID 管理に使用されます。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | MySQL のダウンロードを完了するためのものです。 </br> いくつかのリージョンでは、ダウンロードが CDN URL にリダイレクトされる可能性があります。 必要に応じて、CDN URL もホワイトリストに登録されていることを確認してください。
|

> [!NOTE]
> Site Recovery コンテナーへの[プライベート リンク接続](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md)がある場合は、構成サーバーへの追加のインターネット アクセスは不要です。 これには例外があり、OVA テンプレートを使用して CS マシンを設定しているときに、プライベート リンク アクセス以外から次の URL にアクセスする必要があります: https://management.azure.com 、 https://www.live.com 、および https://www.microsoft.com 。 これらの URL へのアクセスを許可しない場合は、Unified Installer を使用して CS を設定してください。

## <a name="required-software"></a>必要なソフトウェア

**コンポーネント** | **要件** 
--- | ---
VMware vSphere PowerCLI | VMware 仮想マシン上で構成サーバーが実行されている場合は、[PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) をインストールする必要があります。
MYSQL | MySQL をインストールする必要があります。 手動でインストールするか、または Site Recovery によってインストールすることができます。 (詳細については、「 [設定の構成](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)」を参照してください)
|

## <a name="sizing-and-capacity-requirements"></a>サイズ設定と容量の要件

次の表は、構成サーバーの容量の要件をまとめたものです。 複数の VMware VM をレプリケートする場合、[容量プラン作成の考慮事項](../articles/site-recovery/site-recovery-plan-capacity-vmware.md)を確認し、[Azure Site Recovery Deployment Planner ツール](../articles/site-recovery/site-recovery-deployment-planner.md)を実行します。


**CPU** | **[メモリ]** | **キャッシュ ディスク** | **データの変更率** | **レプリケートされたマシン**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 ソケット * 4 コア \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシン
12 vCPU<br/><br/> 2 ソケット * 6 コア \@ 2.5 GHz | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシン
16 vCPU<br/><br/> 2 ソケット * 8 コア \@ 2.5 GHz | 32 GB | 1 TB (テラバイト) | 1 ～ 2 TB | 150 ～ 200 台のマシン
|

