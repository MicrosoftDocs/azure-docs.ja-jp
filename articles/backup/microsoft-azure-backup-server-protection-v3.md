---
title: Azure Backup Server V3 RTM でバックアップできる内容
description: この記事では、Azure Backup Server V3 RTM によって保護されるすべてのワークロード、データ型、およびインストールを一覧表示した保護のマトリックスを示します。
ms.date: 11/13/2018
ms.topic: conceptual
ms.openlocfilehash: 1ec8240844061b9b250a3cbf92ffcc5f2b3f474b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986889"
---
# <a name="azure-backup-server-v3-rtm-protection-matrix"></a>Azure Backup Server V3 RTM の保護マトリックス

次のマトリックスは、Azure Backup Server V3 RTM 以前のバージョンで保護できる内容を一覧表示しています。

## <a name="protection-support-matrix"></a>保護のサポート マトリックス

|ワークロード|Version|Azure Backup Server</br> インストール|サポートされる Azure Backup Server|保護と回復|
|------------|-----------|---------------|--------------|--------------|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 10|物理サーバー<br /><br />Hyper-V 仮想マシン<br /><br />VMware 仮想マシン|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8.1|物理サーバー<br /><br />Hyper-V 仮想マシン|V3、V2|ファイル<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8.1|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 7|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 7|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)|V3、V2|ボリューム、共有、フォルダー、ファイル、重複除去されたボリューム<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 Azure Backup Server はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|サーバー (64 ビット)|Windows Server 2019|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /><br />VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /><br />物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3 <br />Not Nano サーバー|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)、重複除去されたボリューム|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2016|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /><br />VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /><br />物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2<br />Not Nano サーバー|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)、重複除去されたボリューム|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012 R2 - Datacenter および Standard|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、Azure Backup Server が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012 R2 - Datacenter および Standard|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、Azure Backup Server が Windows Server 2012 または 2012 R2 で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、Azure Backup Server が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、Azure Backup Server が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、Azure Backup Server が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2<br />SP1 を実行しており、かつ [Windows Management Frame](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2<br />SP1 を実行しており、かつ [Windows Management Frame](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります|ボリューム、共有、フォルダー、ファイル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2<br />SP1 を実行しており、かつ [Windows Management Frame](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 SP2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|サポートされていません|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 SP2|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Storage Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|SQL Server|SQL Server 2019|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合) <br /><br /> VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2017|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合) <br /><br /> VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2016 SP2|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン <br /><br /> VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2016 SP1|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン <br /><br /> VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2016|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン <br /><br /> VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2014|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2014|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|すべてのデプロイ シナリオ: データベース|
|Exchange|Exchange 2016|物理サーバー<br/><br/> オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack<br /> <br />Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|Exchange|Exchange 2016|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|Exchange|Exchange 2013|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|Exchange|Exchange 2013|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|Exchange|Exchange 2010|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|Exchange|Exchange 2010|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース<br/><br/> ReFS 経由での Exchange のバックアップはサポートされていません |
|SharePoint|SharePoint 2016|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /><br />Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /><br />VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、フロント エンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、フロント エンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合) - <br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2010|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2010|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2010|VMware 内の Windows 仮想マシン (VMware 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /> <br /> Azure Stack|V3、V2|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2019|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2016|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2012 R2 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2012 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2008 R2 SP1 - Enterprise および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|V3、V2|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の MABS 保護エージェント|Windows Server 2008 SP2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|サポートされていません|保護:Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダーの項目レベルの回復、ボリューム、仮想ハード ドライブ|
|VMware VM|VMware vCenter/vSphere ESX/ESXi ライセンス版 5.5/6.0/6.5 |物理サーバー、 <br/>オンプレミスの Hyper-V VM、 <br/> VMware 内の Windows VM|V3、V2|VMware VMs on クラスターの共有ボリューム (CSV) 上の VMware VM、NFS、および SAN ストレージ。<br /> ファイルとフォルダーの項目レベルの回復は、Windows VM にのみ利用できます。VMware vApp はサポートされていません。|
|VMware VM|[VMware vSphere ライセンス版 6.7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |物理サーバー、 <br/>オンプレミスの Hyper-V VM、 <br/> VMware 内の Windows VM|V3|VMware VMs on クラスターの共有ボリューム (CSV) 上の VMware VM、NFS、および SAN ストレージ。<br /> ファイルとフォルダーの項目レベルの回復は、Windows VM にのみ利用できます。VMware vApp はサポートされていません。|
|Linux|[Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) または [VMware](backup-azure-backup-server-vmware.md) のゲストとして実行されている Linux|物理サーバー、 <br/>オンプレミスの Hyper-V VM、 <br/> VMware 内の Windows VM|V3、V2|Hyper-V が Windows Server 2012 R2 または Windows Server 2016 上で実行されている必要があります。 保護:仮想マシン全体<br /><br />回復: 仮想マシン全体 <br/><br/> ファイル整合性のあるスナップショットのみがサポートされます。 <br/><br/> サポートされる Linux ディストリビューションおよびバージョンの完全なリストについては、「[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)」の記事を参照してください。|

## <a name="azure-expressroute-support"></a>Azure ExpressRoute のサポート

パブリック ピアリング (古い回線で使用可能) と Microsoft ピアリングを使用して、Azure ExpressRoute 経由でデータをバックアップできます。 プライベート ピアリング経由のバックアップはサポートされていません。

パブリック ピアリングを使用して、次のドメインまたはアドレスへのアクセスを確保します。

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP アドレス
  * 20.190.128.0/18
  * 40.126.0.0/18

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

    Windows Server 2008 R2 上で Hyper-V を実行する場合は、KB [975354](https://support.microsoft.com/kb/975354) で説明されている更新プログラムをインストールするようにしてください。
    クラスター構成内の Windows Server 2008 R2 上で Hyper-V を実行する場合は、SP2 および KB [971394](https://support.microsoft.com/kb/971394) をインストールするようにしてください。

* Exchange Server - Azure Backup Server は、サポートされている Exchange Server バージョンの非共有ディスク クラスター (クラスター連続レプリケーション) を保護することができるほか、ローカル連続レプリケーションのために構成された Exchange Server も保護できます。

* SQL Server - Azure Backup Server は、クラスター共有ボリューム (CSV) 上にホストされた SQL Server データベースのバックアップをサポートしていません。

Azure Backup Server は、MABS サーバーと同じドメイン内、および子または信頼できるドメイン内に配置されたクラスター ワークロードを保護できます。 信頼されていないドメインまたはワークグループ内のデータ ソースを保護する場合は、単一サーバーに対しては NTLM または証明書認証、クラスターに対しては証明書認証のみを使用します。
