---
title: Azure VM 内の SQL Server のバックアップに関する Azure Backup のサポート マトリックス
description: Azure Backup サービスを使用して Azure VM 内の SQL Server をバックアップする場合のサポート設定と制限事項について概説します。
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 78436981c515b95ccda763d8ac916738b4364953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734795"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure VM 内の SQL Server のバックアップに関するサポート マトリックス

Azure Backup を使用すると、Microsoft Azure クラウド プラットフォームでホストされている Azure VM 内の SQL Server データベースをバックアップできます。 この記事では、Azure VM 内の SQL Server のバックアップのシナリオとデプロイの一般的なサポート設定と制限事項について説明します。

## <a name="scenario-support"></a>シナリオのサポート

**サポート** | **詳細**
--- | ---
**サポートされているデプロイ** | SQL Marketplace の Azure VM と、Marketplace 以外の (SQL Server が手動でインストールされる) VM がサポートされています。
**サポートされているリージョン** | オーストラリア南東部 (ASE)、オーストラリア東部 (AE)、オーストラリア中部 (AC)、オーストラリア中部 2 (AC) <br> ブラジル南部 (BRS)<br> カナダ中部 (CNC)、カナダ東部 (CE)<br> 東南アジア (SEA)、東アジア (EA) <br> 米国東部 (EUS)、米国東部 2 (EUS2)、米国中西部 (WCUS)、米国西部 (WUS)、米国西部 2 (WUS 2)、米国中北部 (NCUS)、米国中部 (CUS)、米国中南部 (SCUS) <br> インド中部 (INC)、インド南部 (INS)、インド西部 <br> 東日本 (JPE)、西日本 (JPW) <br> 韓国中部 (KRC)、韓国南部 (KRS) <br> 北ヨーロッパ (NE)、西ヨーロッパ <br> 英国南部 (UKS)、英国西部 (UKW) <br> US Gov アリゾナ、US Gov バージニア、US Gov テキサス、US DoD 中部、US DoD 東部 <br> ドイツ北部、ドイツ中西部 <br> スイス北部、スイス西部 <br> フランス中部 <br> 中国東部、中国東部 2、中国北部、中国北部 2
**サポートされているオペレーティング システム** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> Linux は現在サポートされていません。
**サポートされる SQL Server のバージョン** | SQL Server 2019、SQL Server 2017 (詳細は[「製品のライフサイクルの検索」ページ](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)を参照)、SQL Server 2016 および SP (詳細は[「製品のライフサイクルの検索」ページ](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)を参照)、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。<br><br>Express LocalDB のバージョンはサポートされていません。
**サポートされている .NET バージョン** | VM にインストールされている .NET Framework 4.5.2 以降

## <a name="feature-considerations-and-limitations"></a>機能の考慮事項と制限事項

|設定  |上限 |
|---------|---------|
|サーバー (およびコンテナー内) で保護できるデータベースの数    |   2000      |
|サポートされているデータベース サイズ (これを超えると、パフォーマンスの問題が発生する可能性があります)   |   2 TB      |
|データベースでサポートされているファイルの数    |   1000      |

>[!NOTE]
> VM リソース、帯域幅、バックアップ ポリシーに基づいて、サーバーごとに推奨される保護されたデータベースの概数を算出するために、[詳細なリソース プランナーをダウンロードします](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)。

* SQL Server のバックアップは、Azure portal または **PowerShell** を使用して構成できます。 CLI はサポートされていません。
* このソリューションは、Azure Resource Manager VM とクラシック VM のどちらの種類の[デプロイ](../azure-resource-manager/management/deployment-models.md)でもサポートされます。
* すべてのバックアップの種類 (完全、差分、ログ) と復旧モデル (簡易、完全、一括ログ) がサポートされています。
* **読み取り専用** データベースの場合: 完全とコピーのみの完全バックアップだけが、バックアップの種類としてサポートされています。
* SQL ネイティブの圧縮は、バックアップ ポリシーでユーザーが明示的に有効にした場合にサポートされます。 Azure Backup を使用すると、ユーザーが設定したこのコントロールの値に応じて、COMPRESSION または NO_COMPRESSION 句を使用してインスタンス レベルの既定値がオーバーライドされます。
* TDE - 有効なデータベース バックアップがサポートされています。 TDE で暗号化されたデータベースを別の SQL Server に復元するには、まず[証明書を宛先サーバーに復元する](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)必要があります。 SQL Server 2016 以降のバージョンでは、TDE が有効になっているデータベースのバックアップの圧縮は使用できますが、[ここ](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)で説明されているように転送サイズは低くなります。
* ミラー データベースおよびデータベース スナップショットのバックアップ操作および復元操作はサポートされていません。
* SQL Server **フェールオーバー クラスター インスタンス (FCI)** はサポートされていません。
* スタンドアロンの SQL Server インスタンスまたは SQL Always On 可用性グループをバックアップするために複数のバックアップ ソリューションを使用すると、バックアップに失敗する可能性があります。 そうしないようにしてください。 また、可用性グループの 2 つのノードを、同じまたは異なるソリューションを使用して個別にバックアップすると、バックアップに失敗する可能性があります。
* 可用性グループが構成されている場合は、バックアップはいくつかの要因に基づいて異なるノードから作成されます。 可用性グループのバックアップ動作を以下にまとめています。

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Always On 可用性グループでのバックアップ動作

バックアップの構成は、可用性グループ (AG) の 1 つのノードでのみ行うことをお勧めします。 バックアップは必ず、プライマリ ノードと同じリージョンに構成してください。 つまり、バックアップの構成場所のリージョンには必ず、プライマリ ノードが存在している必要があります。 AG のすべてのノードがバックアップの構成場所と同じリージョンにあれば問題ありません。

#### <a name="for-cross-region-ag"></a>複数リージョンにまたがる AG

* バックアップの設定に関係なく、バックアップはバックアップの構成場所とは同じリージョン内のノードからのみ実行されます。 複数リージョンにまたがるバックアップはサポートされないためです。 ノードが 2 つしか存在せず、なおかつ、セカンダリ ノードが別のリージョンに存在する場合、プライマリ ノード側からは引き続きバックアップが実行されます (ただし、バックアップの設定が "セカンダリのみ" になっている場合を除く)。
* バックアップの構成場所とは異なるリージョンにノードがフェールオーバーされた場合、フェールオーバーされた側のリージョン内のノードでは、バックアップに失敗します。

バックアップ設定とバックアップの種類 (完全/差分/ログ/コピーのみの完全) に応じて、バックアップは特定のノード (プライマリ/セカンダリ) から作成されます。

#### <a name="backup-preference-primary"></a>バックアップの設定:プライマリ

**バックアップの種類** | **Node**
--- | ---
[完全] | プライマリ
差分 | プライマリ
ログ |  プライマリ
コピーのみの完全 |  プライマリ

#### <a name="backup-preference-secondary-only"></a>バックアップの設定:セカンダリのみ

**バックアップの種類** | **Node**
--- | ---
[完全] | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

#### <a name="backup-preference-secondary"></a>バックアップの設定:セカンダリ

**バックアップの種類** | **Node**
--- | ---
[完全] | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

#### <a name="no-backup-preference"></a>バックアップの設定なし

**バックアップの種類** | **Node**
--- | ---
[完全] | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

## <a name="next-steps"></a>次のステップ

Azure VM 上で実行されている[SQL Server データベースをバックアップする](backup-azure-sql-database.md)方法を学習します。