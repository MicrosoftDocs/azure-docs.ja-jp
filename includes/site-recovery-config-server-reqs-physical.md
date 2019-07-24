---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887017"
---
**物理サーバー レプリケーションに使用する構成/プロセス サーバーの要件**

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
IP アドレスの種類 | 静的 
| 
**アクセスの設定** | 
MYSQL | 構成サーバーには MySQL がインストールされている必要があります。 手動でインストールするか、またはデプロイ中に Site Recovery でインストールすることができます。 Site Recovery でインストールする場合は、マシンが http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi に到達できることを確認してください。
URL | 構成サーバーは、次の URL にアクセスする必要があります (直接またはプロキシ経由)。<br/><br/> Azure AD: `login.microsoftonline.com`、`login.microsoftonline.us`、 `*.accesscontrol.windows.net`<br/><br/> レプリケーション データ転送: `*.backup.windowsazure.com`、 `*.backup.windowsazure.us`<br/><br/> レプリケーション管理: `*.hypervrecoverymanager.windowsazure.com`、`*.hypervrecoverymanager.windowsazure.us`、`https://management.azure.com`、 `*.services.visualstudio.com`<br/><br/> ストレージ アクセス: `*.blob.core.windows.net`、 `*.blob.core.usgovcloudapi.net`<br/><br/> 時刻同期: `time.nist.gov`、 `time.windows.com`<br/><br/> テレメトリ (オプション):  `dc.services.visualstudio.com`
ファイアウォール | IP アドレスベースのファイアウォール規則で Azure の URL への通信を許可する必要があります。 IP 範囲を簡略化および制限するために、URL フィルタリングの使用をお勧めします。<br/><br/>**商用 IP:**<br/><br/>- [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> - 米国西部の IP アドレス範囲を許可します (Access Control と Identity Management に使用されます)。<br/><br/> - Azure Active Directory、バックアップ、レプリケーション、ストレージに必要な URL をサポートするために、サブスクリプションの Azure リージョンの IP アドレス範囲を許可します。<br/><br/> **政府機関向け IP:**<br/><br/> - Azure Government データセンターの IP の範囲と HTTPS (443) ポートを許可します。<br/><br/> - Azure Active Directory、バックアップ、レプリケーション、ストレージに必要な URL をサポートするために、すべての US Gov リージョン (バージニア、テキサス、アリゾナ、アイオワ) の IP アドレス範囲を許可します。
ポート | 443 を許可 (コントロール チャネルのオーケストレーション)<br/><br/> 9443 を許可 (データ転送) 


**構成/プロセス サーバーのサイズ要件**

**CPU** | **メモリ** | **キャッシュ ディスク** | **データ変化率** | **レプリケートされるマシン**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 ソケット * 4 コア \@ 2.5 GHz | 16GB | 300 GB | 500 GB 以下 | 100 台未満のマシン
12 vCPU<br/><br/> 2 ソケット * 6 コア \@ 2.5 GHz | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシン
16 vCPU<br/><br/> 2 ソケット * 8 コア \@ 2.5 GHz | 32 GB | 1 TB (テラバイト) | 1 ～ 2 TB | 150 ～ 200 台のマシン

