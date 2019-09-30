---
title: Hyper-V の評価と移行に関する Azure Migrate サポート マトリックス
description: Azure Migrate サービスを使用した Hyper-V の評価と移行に関する設定と制限について、要点を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 0d0329be0b7f864edbfc3c2aabc08f1742359670
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066772"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V の評価と移行のサポート マトリックス

[Azure Migrate サービス](migrate-overview.md)を使用して、マシンを評価して Microsoft Azure クラウドに移行することができます。 この記事では、オンプレミスの Hyper-V VM を評価して移行する際にサポートされる設定と制限について要点を説明します。



## <a name="hyper-v-scenarios"></a>Hyper-V のシナリオ

この表は、Hyper-V VM に関してサポートされるシナリオをまとめたものです。

**Deployment** | **詳細***
--- | ---
**オンプレミスの Hyper-V VM を評価する** | 最初の評価を[設定](tutorial-prepare-hyper-v.md)します。<br/><br/> 大規模な評価を[実行](scale-hyper-v-assessment.md)します。
**Hyper-V VM を Azure に移行する** | Azure への移行を[試します](tutorial-migrate-hyper-v.md)。

Azure Migrate のサーバー移行では、System Center Virtual Machine Manager (VMM) を使って管理している Hyper-V サーバーの移行をサポートしていません。

## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
Azure のアクセス許可 | Azure Migrate プロジェクトを作成するには、サブスクリプションの共同作成者または所有者のアクセス許可が必要です。
Hyper-V VM | 1 つのプロジェクトで最大 35,000 の Hyper-V VM を評価します。 1 つの Azure サブスクリプションに複数のプロジェクトを含めることができます。 評価の上限に達するまでは、1 つのプロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。
[地理的な場所] | Azure Migrate プロジェクトは、いくつかの地域で作成できます。 プロジェクトを作成できるのは特定の地域に限られますが、ターゲットの場所がそれ以外であるマシンを評価または移行することは可能です。 プロジェクトの地域は、検出されたメタデータを格納するためにのみ使用されます。

  **地理的な場所** | **メタデータ ストレージの場所**
  --- | ---
  Azure Government | 米国政府バージニア州
  アジア太平洋 | 東アジアまたは東南アジア
  オーストラリア | オーストラリア東部またはオーストラリア南東部
  ブラジル | ブラジル南部
  カナダ | カナダ中部またはカナダ東部
  ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
  フランス | フランス中部
  インド | インド中部またはインド南部
  日本 |  東日本または西日本
  韓国 | 韓国中部または韓国南部
  イギリス | 英国南部または英国西部
  米国 | 米国中部または米国西部 2


 > [!NOTE]
 > Azure Government は現在、[古いバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)の Azure Migrate でのみサポートされます。


## <a name="assessment-hyper-v-host-requirements"></a>評価 - Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 <br/> 管理者のアクセス許可を割り当てたくない場合には、代わりにローカルまたはドメインのユーザー アカウントを作成し、そのユーザーを Remote Management Users、Hyper-V Administrators、Performance Monitor Users のグループに追加します。 |
| **ホスト オペレーティング システム** | Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2。<br/> Windows Server 2012 を実行している Hyper-V ホスト上にある VM を評価することはできません。 |
| **PowerShell リモート処理**   | 各ホストで有効にする必要があります。 |
| **Hyper-V レプリカ**       | Hyper-V レプリカを使用する (または、同じ VM 識別子を持つ複数の VM がある) 場合、Azure Migrate を使用して元の VM とレプリケートされた VM の両方を検出すると、Azure Migrate によって生成される評価が正確ではない可能性があります。 |


## <a name="assessment-hyper-v-vm-requirements"></a>評価 - Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |



## <a name="assessment-appliance-requirements"></a>評価 - アプライアンスの要件

Azure Migrate では評価のために軽量アプライアンスを実行して Hyper-V VM を検出し、VM メタデータとパフォーマンス データを Azure Migrate に送信します。 アプライアンスは Hyper-V VM 上で実行されます。設定は、Azure portal からダウンロードした圧縮された Hyper-V VHD を使用して行います。 次の表は、アプライアンスの要件をまとめたものです。

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **アプライアンスのデプロイ**   |  アプライアンスを Hyper-V VM としてデプロイします。<br/> Azure Migrate によって提供されるアプライアンス VM は、Hyper-V VM バージョン 5.0 です。<br/> Hyper-V ホストで Windows Server 2012 R2 以降が実行されている必要があります。<br/> ホストには、アプライアンス VM に 16 GB の RAM、8 つの vCPU、約 80 GB のストレージ スペース、1 つの外部スイッチを割り当てることができる十分な領域が必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。
| **Azure Migrate プロジェクト**  |  単一のプロジェクトにアプライアンスを関連付けることができます。<br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> プロジェクト内で最大 35,000 個の VM を評価できます。
| **Hyper-V ホスト**          | アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。
| **検出**              | 1 つのアプライアンスで最大 5,000 個の VM を検出できます。
| **評価グループ**       | 1 つのグループに最大 35,000 個のマシンを追加できます。
| **評価**             | 1 回の評価で最大 35,000 個の VM を評価できます。



## <a name="assessment-appliance-url-access"></a>評価 - アプライアンスの URL アクセス

VM を評価するには、Azure Migrate アプライアンスがインターネットに接続されている必要があります。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用している場合は、表内の URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。
- インターセプト プロキシがある場合は、プロキシ サーバーからアプライアンスへのサーバー証明書のインポートが必要になる場合があります。


**URL** | **詳細**  
--- | ---
*.portal.azure.com | Azure portal への移動
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
management.azure.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
dc.services.visualstudio.com | ログ記録と監視
*.vault.azure.net | アプライアンスとサービス間の通信時の Azure Key Vault のシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。
https://download.microsoft.com/download/* | Microsoft ダウンロード サイトからのダウンロードを許可します。



## <a name="assessment-port-requirements"></a>評価 - ポートの要件

次の表は、評価のためのポート要件をまとめたものです。

**デバイス** | **Connection**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443、5671、5672 で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**Hyper-V ホスト/クラスター** | Common Information Model (CIM) セッションを使用し、WinRM ポート 5985 (HTTP) と 5986 (HTTPS) で、Hyper-V VM の構成とパフォーマンスのメタデータをプルするための受信接続。

## <a name="migration-limitations"></a>移行 - 制限
レプリケーションでは、一度に最大 10 個の VM を選択できます。 より多くのマシンを移行する場合は、10 個単位のグループでレプリケートします。

## <a name="migration-hyper-v-host-requirements"></a>移行 - Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 |
| **ホスト オペレーティング システム** | Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2。 |

## <a name="migration-hyper-v-vm-requirements"></a>移行 - Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **アクセス許可**           | 評価する各 Hyper-V VM に対する管理者のアクセス許可が必要です。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |
| **Azure に必要な変更** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。 次のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8<br/><br/> その他のオペレーティング システムについては、移行前に手動で調整する必要があります。 関連する記事には、その手順が記載されています。 |
| **Linux ブート**                 | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。 |
| **UEFI ブート**                  | UEFI ブートを使用した VM の移行はサポートされません。  |
| **ディスク サイズ**                  | OS ディスク用に 2 TB、データ ディスク用に 4 TB。
| **ディスクの数** | VM あたり最大で 16 台のディスク。
| **暗号化されたディスクまたはボリューム**    | 移行はサポートされません。 |
| **RDM またはパススルー ディスク**      | 移行はサポートされません。 |
| **共有ディスク** | 共有ディスクを使用する VM の移行はサポートされません。
| **NFS**                        | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。 |
| **iSCSI**                      | iSCSI ターゲットを含む VM の移行はサポートされていません。
| **ターゲット ディスク**                | マネージド ディスクのみを含む Azure VM に移行することができます。 |
| **IPv6** | サポートされていません。
| **NIC チーミング** | サポートされていません。
| **Azure Site Recovery** | VM で Azure Site Recovery によるレプリケーションが有効になっている場合、Azure Migrate Server Migration を使用してレプリケートすることはできません。





## <a name="migration-hyper-v-host-url-access"></a>移行 - Hyper-V ホストの URL アクセス

次の表は、Hyper-V ホストの URL アクセス要件をまとめたものです。

**URL** | **詳細**  
--- | ---
login.microsoftonline.com | Active Directory を使用したアクセス制御と ID 管理。
*.backup.windowsazure.com | レプリケーション データの転送と調整。
*.hypervrecoverymanager.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。
dc.services.visualstudio.com | 内部監視に使用するアプリのログをアップロードします。
time.windows.com | システム時刻とグローバル時刻間の時刻同期を確認します。

## <a name="migration-port-access"></a>移行 - ポート アクセス

次の表は、VM 移行用の Hyper-V ホストと VM のポート要件をまとめたものです。

**デバイス** | **Connection**
--- | ---
Hyper-V ホスト/VM | HTTPS ポート 443 で、VM レプリケーション データを Azure Migrate に送信するための送信接続。




## <a name="next-steps"></a>次の手順

移行のために [Hyper-V VM の評価を準備](tutorial-prepare-hyper-v.md)します。
