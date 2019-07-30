---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 3b4992a16061bef782f012aa7887b248e3423234
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568343"
---
**構成/プロセス サーバー要件**

**コンポーネント** | **要件** 
--- | ---
**ハードウェアの設定** | 
CPU コア数 | 8 
RAM | 16 GB
ディスクの数 | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます 
ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
ディスクの空き領域 (リテンション ディスク) | 600 GB
 | 
**ソフトウェアの設定** | 
オペレーティング システム | Windows Server 2012 R2 <br> Windows Server 2016
オペレーティング システムのロケール | 英語 (en-us)
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V 
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする 
| 
**ネットワークの設定** | 
IP アドレスの種類 | 静的 
Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送) 
NIC の種類 | VMXNET3 (構成サーバーが VMware VM である場合)
 |
**インターネット アクセス** (サーバーは、次の URL にアクセスする必要があります (直接またはプロキシ経由))。|
\*.backup.windowsazure.com | レプリケートされたデータの転送と調整に使用
\*.store.core.windows.net | レプリケートされたデータの転送と調整に使用
\*.blob.core.windows.net | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用
\*.hypervrecoverymanager.windowsazure.com | レプリケーション管理操作と調整に使用
https:\//management.azure.com | レプリケーション管理操作と調整に使用 
*.services.visualstudio.com | テレメトリの目的で使用 (省略可能)
time.nist.gov | システム時刻とグローバル時刻間の時刻同期の確認に使用。
time.windows.com | システム時刻とグローバル時刻間の時刻同期の確認に使用。
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF のセットアップでは、次の URL にアクセスできる必要があります。 Azure Active Directory によるアクセス制御と ID 管理に使用されます。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | MySQL のダウンロードを完了するためのものです。 </br> いくつかのリージョンでは、ダウンロードが CDN URL にリダイレクトされる可能性があります。 必要に応じて、CDN URL もホワイトリストに登録されていることを確認してください。
|
**インストールするソフトウェア** | 
VMware vSphere PowerCLI | VMware 仮想マシン上で構成サーバーが実行されている場合は、[PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) をインストールする必要があります。
MYSQL | MySQL をインストールする必要があります。 手動でインストールするか、または Site Recovery によってインストールすることができます。 (詳細については、「 [設定の構成](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)」を参照してください)

**構成/プロセス サーバーのサイズ要件**

**CPU** | **メモリ** | **キャッシュ ディスク** | **データの変更率** | **レプリケートされたマシン**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 ソケット * 4 コア \@ 2.5 GHz | 16GB | 300 GB | 500 GB 以下 | 100 台未満のマシン
12 vCPU<br/><br/> 2 ソケット * 6 コア \@ 2.5 GHz | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシン
16 vCPU<br/><br/> 2 ソケット * 8 コア \@ 2.5 GHz | 32 GB | 1 TB (テラバイト) | 1 ～ 2 TB | 150 ～ 200 台のマシン

