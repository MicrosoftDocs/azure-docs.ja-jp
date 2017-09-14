---
title: "Azure Site Recovery を使用した Azure へのレプリケーション用にオンプレミスの VMware リソースを準備する | Microsoft Docs"
description: "VMware VM 上で実行されているワークロードを Azure Storage にレプリケートするために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>手順 6: Azure へのオンプレミスの VMware レプリケーションを準備する

この記事の手順に従って、Azure Site Recovery と対話するようにオンプレミスの VMware サーバーを準備すると共に、モビリティ サービスをインストールできるように VMWare VM を準備します。 モビリティ サービス エージェントは、Azure にレプリケートするすべてのオンプレミス VM にインストールする必要があります。

## <a name="prepare-for-automatic-discovery"></a>自動検出を準備する

Site Recovery は vSphere ESXi ホスト (vCenter サーバーの有無を問わず) で実行されている VM を自動的に検出します。 自動検出するには、Site Recovery にホストおよびサーバーにアクセスするアカウントが必要です。

1. 専用のアカウントを使用するには、(vCenter レベルで、下の表に記載されているアクセス許可を持つ) ロールを作成します。 名前は **Azure_Site_Recovery** などにします。
2. 次に、vSphere ホスト/vCenter サーバーにユーザーを作成し、そのユーザーにこのロールを割り当てます。 Site Recovery のデプロイの際に、このユーザー アカウントを指定します。


### <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

Site Recovery は、プロセス サーバーで VM を自動検出するためと VM のフェールオーバーおよびフェールバックのために、VMware にアクセスできる必要があります。

- **移行**: フェールバックせずに VMware VM を Azure に移行するだけの場合は、読み取り専用のロールを持つ VMware アカウントを使用することができます。 このようなロールでフェールオーバーを実行できますが、保護されたソース マシンをシャットダウンすることはできません。 これは移行では必要ありません。
- **レプリケート/回復**: 完全なレプリケーション (レプリケーション、フェールオーバー、フェールバック) をデプロイする場合は、そのアカウントでディスクの作成および削除や VM の電源投入などの操作を実行できる必要があります。
- **自動検出**: 少なくとも読み取り専用のアカウントが必要です。


**タスク** | **必要なアカウントとロール** | **アクセス許可** | **詳細**
--- | --- | --- | ---
**プロセス サーバーによる VMware VM の自動検出** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**フェールオーバー** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。
**フェールオーバーとフェールバック** | 必要なアクセス許可を備えた ロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>モビリティ サービスのプッシュ インストールを準備する

モビリティ サービスは、レプリケートするすべての VM にインストールする必要があります。 このサービスをインストールする方法は、手動インストール、Site Recovery プロセス サーバーからのプッシュ インストール、System Center Configuration Manager などを使用したインストールなど、多数あります。

プッシュ インストールを使用する場合は、Site Recovery で VM へのアクセスに使用できるアカウントを準備する必要があります。

- ドメイン アカウントまたはローカル アカウントを使用できます。
- Windows の場合、ドメイン アカウントを使用していなければ、ローカル マシンでリモート ユーザー アクセス コントロールを無効にする必要があります。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
- CLI から Windows 用のレジストリ エントリを追加する場合は、次のように入力します。       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux の場合、アカウントは、ソース Linux サーバーの root である必要があります。



## <a name="next-steps"></a>次のステップ

[手順 7: コンテナーを作成する](vmware-walkthrough-create-vault.md)方法に関するページに進む

