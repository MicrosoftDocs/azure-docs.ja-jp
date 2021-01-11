---
title: MABS (Azure Backup Server) V3 UR1 の保護マトリックス
description: この記事では、Azure Backup Server によって保護されるすべてのワークロード、データ型、およびインストールを一覧表示したサポート マトリックスを示します。
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 70d8b8cd26a40b0c7ec8b538bcb702d281f829e6
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826839"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) V3 UR1 の保護マトリックス

この記事では、Azure Backup Server で保護できるさまざまなサーバーおよびワークロードを一覧表示します。 次のマトリックスは、Azure Backup Server で保護できる内容を一覧表示しています。

MABS v3 UR1 には次のマトリックスを使用します。

* ワークロード – テクノロジのワークロードの種類。

* バージョン – そのワークロードでサポートされる MABS バージョン。

* MABS のインストール – MABS をインストールしようとしているコンピューター/場所。

* 保護と回復 – サポートされているストレージ コンテナーやサポートされているデプロイなどのワークロードに関する詳細情報を一覧表示します。

>[!NOTE]
>32 ビット保護エージェントのサポートは、MABS v3 UR1 では非推奨となっています。 「[32 ビット保護エージェントの廃止](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)」を参照してください。

## <a name="protection-support-matrix"></a>保護のサポート マトリックス

以降のセクションでは、MABS に対する保護のサポート マトリックスについて詳しく説明します。

* [アプリケーションのバックアップ](#applications-backup)
* [VM のバックアップ](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>アプリケーションのバックアップ

| **[ワークロード]**               | **Version**                                                  | **Azure Backup Server のインストール**                       | **サポートされる Azure Backup Server** | **保護と回復**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| クライアント コンピューター(64 ビット) | Windows 10                                                  | 物理サーバー  <br><br>    Hyper-V 仮想マシン   <br><br>   VMware 仮想マシン | V3 UR1                            | ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム   <br><br>   保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。  <br><br>    ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server ではボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得します。また、そのスナップショットは、ボリュームが少なくとも 1 GB である場合にのみ機能します。 |
| サーバー (64 ビット)          | Windows Server 2019、2016、2012 R2、2012                    | Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)  <br><br>    物理サーバー  <br><br>    Hyper-V 仮想マシン <br><br>     VMware 仮想マシン  <br><br>    Azure Stack | V3 UR1                            | ボリューム、共有、フォルダー、ファイル <br><br>    重複除去されたボリューム (NTFS のみ)  <br><br>   システム状態とベア メタル (ワークロードが Azure 仮想マシンとして実行されている場合はサポートされません) |
| サーバー (64 ビット)          | Windows Server 2008 R2 SP1、Windows Server 2008 SP2 ([Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります) | 物理サーバー  <br><br>    Hyper-V 仮想マシン  <br><br>      VMware 仮想マシン  <br><br>   Azure Stack | V3 UR1                            | ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル        |
| SQL Server                | SQL Server 2019、2017、2016 と[サポートされている SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)、2014 とサポートされている [SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 物理サーバー  <br><br>     Hyper-V 仮想マシン   <br><br>     VMware 仮想マシン  <br><br>   Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)  <br><br>     Azure Stack | V3 UR1                            | すべてのデプロイ シナリオ: データベース       <br><br>  MABS v3 UR1 では、ReFS ボリュームを介した SQL データベースのバックアップがサポートされます                  |
| Exchange                   | Exchange 2019、2016                                         | 物理サーバー   <br><br>   Hyper-V 仮想マシン  <br><br>      VMware 仮想マシン  <br><br>   Azure Stack  <br><br>    Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合) | V3 UR1                            | 保護 (すべてのデプロイ シナリオ):スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) に属するデータベース  <br><br>    回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース    <br><br>  MABS v3 UR1 では、ReFS を介した Exchange のバックアップがサポートされます |
| SharePoint                 | SharePoint 2019、2016 (最新の SP がインストールされていること)                       | 物理サーバー  <br><br>    Hyper-V 仮想マシン <br><br>    VMware 仮想マシン  <br><br>   Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)   <br><br>   Azure Stack | V3 UR1                            | 保護 (すべてのデプロイ シナリオ):ファーム、フロントエンド Web サーバーのコンテンツ  <br><br>    回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー  <br><br>    コンテンツ データベースに対して SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされません。 |

## <a name="vm-backup"></a>VM のバックアップ

| **[ワークロード]**                                                 | **Version**                                             | **Azure Backup Server のインストール**                      | **サポートされる Azure Backup Server** | **保護と回復**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント | Windows Server 2019、2016、2012 R2、2012               | 物理サーバー  <br><br>    Hyper-V 仮想マシン <br><br>    VMware 仮想マシン | V3 UR1                             | 保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)  <br><br>    回復: 仮想マシン、Windows に対してのみ使用できるファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ |
| VMware VM                                                  | VMware サーバー 5.5、6.0、または 6.5、6.7 (ライセンス版) | Hyper-V 仮想マシン  <br><br>   VMware 仮想マシン         | V3 UR1                             | 保護:VMware VMs on クラスターの共有ボリューム (CSV) 上の VMware VM、NFS、および SAN ストレージ。   <br><br>     回復: 仮想マシン、Windows に対してのみ使用できるファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ <br><br>    VMware vApps はサポートされません。 |

## <a name="linux"></a>Linux

| **[ワークロード]** | **Version**                               | **Azure Backup Server のインストール**                      | **サポートされる Azure Backup Server** | **保護と回復**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Hyper-V または VMware ゲストとして実行されている Linux | 物理サーバー、オンプレミス Hyper-V 仮想マシン、VMWare での Windows VM | V3 UR1                             | Hyper-V は、Windows Server 2012 R2、Windows Server 2016、または Windows Server 2019 で実行されている必要があります。 保護:仮想マシン全体   <br><br>   回復: 仮想マシン全体   <br><br>    ファイル整合性のあるスナップショットのみがサポートされます。    <br><br>   サポートされる Linux のディストリビューションとバージョンの完全なリストについては、[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)に関するページを参照してください。 |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute のサポート

パブリック ピアリング (古い回線で使用可能) と Microsoft ピアリングを使用して、Azure ExpressRoute 経由でデータをバックアップできます。 プライベート ピアリング経由のバックアップはサポートされていません。

パブリック ピアリングを使用して、次のドメインまたはアドレスへのアクセスを確保します。

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft ピアリングの使用時には、サービス、リージョン、関連するコミュニティについて以下の値を選択します。

* Azure Active Directory (12076:5060)
* Microsoft Azure リージョン (Recovery Services コンテナーの場所による)
* Azure Storage (Recovery Services コンテナーの場所による)

詳細については、「[ExpressRoute ルーティングの要件](../expressroute/expressroute-routing.md)」を参照してください。

>[!NOTE]
>パブリック ピアリングは、新しい回線では非推奨です。

## <a name="cluster-support"></a>クラスターのサポート

Azure Backup Server は、次のクラスタ化されたアプリケーション内のデータを保護できます。

* ファイル サーバー

* SQL Server

* Hyper-V - スケールアウトされた MABS 保護エージェントを使用して Hyper-V クラスターを保護する場合、保護された Hyper-V ワークロードに二次的な保護を追加することはできません。

* Exchange Server - Azure Backup Server は、サポートされている Exchange Server バージョンの非共有ディスク クラスター (クラスター連続レプリケーション) を保護することができるほか、ローカル連続レプリケーションのために構成された Exchange Server も保護できます。

* SQL Server - Azure Backup Server は、クラスター共有ボリューム (CSV) 上にホストされた SQL Server データベースのバックアップをサポートしていません。

Azure Backup Server は、MABS サーバーと同じドメイン内、および子または信頼できるドメイン内に配置されたクラスター ワークロードを保護できます。 信頼されていないドメインまたはワークグループ内のデータ ソースを保護する場合は、単一サーバーに対しては NTLM または証明書認証、クラスターに対しては証明書認証のみを使用します。
