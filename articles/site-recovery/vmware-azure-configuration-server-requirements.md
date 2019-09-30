---
title: Azure Site Recovery を使用して VMware を Azure にディザスター リカバリーするための構成サーバーの要件 | Microsoft Docs
description: この記事では、Azure Site Recovery による Azure への VMware のディザスター リカバリー用に構成サーバーをデプロイするときのサポートと要件について説明します
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814291"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure に VMware をディザスター リカバリーするための構成サーバーの要件

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを展開します。

- 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。
- 構成サーバーのコンポーネントとプロセスの[詳細をご確認ください](vmware-azure-architecture.md)。

## <a name="configuration-server-deployment"></a>構成サーバーのデプロイ

Azure への VMware VM のディザスター リカバリーのためには、VMware VM として構成サーバーをデプロイします。

- Site Recovery に用意されている OVA テンプレートを Azure portal からダウンロードして vCenter Server にインポートし、構成サーバー VM を設定します。
- OVA テンプレートを使用して構成サーバーをデプロイすると、VM はこの記事で説明されている要件に自動的に準拠します。
- OVA テンプレートを使用して構成サーバーを設定することを強くお勧めします。 ただし、VMware VM のディザスター リカバリーを設定するときに、OVA テンプレートを使用できない場合は、[これらの指示](physical-azure-set-up-source.md)を使用して構成サーバーをデプロイできます。
- Azure へのオンプレミス物理マシンのディザスター リカバリー用に構成サーバーをデプロイする場合は、[こちらの記事](physical-azure-set-up-source.md)の説明に従ってください。 


## <a name="hardware-requirements"></a>ハードウェア要件

**コンポーネント** | **要件** 
--- | ---
CPU コア数 | 8 
RAM | 16 GB
ディスクの数 | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます 
ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
ディスクの空き領域 (リテンション ディスク) | 600 GB

## <a name="software-requirements"></a>ソフトウェア要件

**コンポーネント** | **要件** 
--- | ---
オペレーティング システム | Windows Server 2012 R2 <br> Windows Server 2016
オペレーティング システムのロケール | 英語 (en-us)
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V 
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする 

## <a name="network-requirements"></a>ネットワークの要件

**コンポーネント** | **要件** 
--- | --- 
IP アドレスの種類 | 静的 
インターネットへのアクセス | サーバーは、次の URL にアクセスする必要があります (直接またはプロキシ経由)。 <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF には次の URL へのアクセスも必要です。 <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送) 
NIC の種類 | VMXNET3 (構成サーバーが VMware VM である場合)

## <a name="required-software"></a>必要なソフトウェア

**コンポーネント** | **要件** 
--- | ---
VMware vSphere PowerCLI | VMware 仮想マシン上で構成サーバーが実行されている場合は、[PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) をインストールする必要があります。
MYSQL | MySQL をインストールする必要があります。 手動でインストールするか、または Site Recovery によってインストールすることができます。

## <a name="sizing-and-capacity-requirements"></a>サイズ設定と容量の要件

次の表は、構成サーバーの容量の要件をまとめたものです。 複数の VMware VM をレプリケートする場合、VMware レプリケーションに関する[容量プラン作成の考慮事項](site-recovery-plan-capacity-vmware.md)を確認し、[Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) ツールを実行する必要があります。 

**コンポーネント** | **要件** 
--- | ---

| **CPU** | **メモリ** | **キャッシュ ディスク** | **データの変更率** | **レプリケートされたマシン** |
| --- | --- | --- | --- | --- |
| 8 vCPU<br/><br/> 2 ソケット * 4 コア \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシン |
| 12 vCPU<br/><br/> 2 ソケット * 6 コア \@ 2.5 GHz | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシン |
| 16 vCPU<br/><br/> 2 ソケット * 8 コア \@ 2.5 GHz | 32 GB | 1 TB (テラバイト) | 1 ～ 2 TB | 150 ～ 200 台のマシン | 



## <a name="next-steps"></a>次の手順
Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。
