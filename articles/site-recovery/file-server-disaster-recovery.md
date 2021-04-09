---
title: Azure Site Recovery を使用したファイル サーバーの保護
description: この記事では、Azure Site Recovery を使用してファイル サーバーを保護する方法について説明します。
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: mvc
ms.openlocfilehash: 9cef163c1b53360222ca32a827552fa361e9dd40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874249"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Azure Site Recovery を使用したファイル サーバーの保護 

[Azure Site Recovery](site-recovery-overview.md) は、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery では、オンプレミスのマシンと Azure 仮想マシン (VM) のディザスター リカバリーを管理および調整します。 ディザスター リカバリーには、さまざまなワークロードのレプリケーション、フェールオーバー、復旧が含まれます。

この記事では、Site Recovery を使用してファイル サーバーを保護する方法について説明し、さまざまな環境に合ったその他の推奨事項を示します。 

- [Azure IaaS ファイル サーバー マシンをレプリケートする](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Site Recovery を使用してオンプレミスのファイル サーバーをレプリケートする](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>ファイル サーバーのアーキテクチャ
オープンな分散ファイル共有システムの目的は、地理的に分散したユーザーのグループが、共同作業でファイルを効率的に使用することができ、整合性の要件が適用されていることが保証された環境を提供することです。 多数の同時ユーザーと大量のコンテンツ項目をサポートする一般的なオンプレミスのファイル サーバー エコシステムでは、レプリケーションのスケジューリングと帯域幅の調整に分散ファイル システム レプリケーション (DFSR) を使用します。 

DFSR では、Remote Differential Compression (RDC) という圧縮アルゴリズムを使用します。このアルゴリズムによって、帯域幅が制限されたネットワークでファイルを効率的に更新できます。 これにより、ファイル内のデータの挿入、削除、再配置が検出されます。 ファイルが更新されたときに、変更されたファイル ブロックのみを DFSR でレプリケートすることが可能になります。 ピーク時以外のタイミングで毎日バックアップが作成されるファイル サーバー環境もありますが、これは障害時のニーズに対応しています。 DFSR は実装されていません。

次の図は、DFSR が実装されたファイル サーバー環境を示しています。
        
![DFSR アーキテクチャ](media/site-recovery-file-server/dfsr-architecture.JPG)

上記の図では、メンバーと呼ばれる複数のファイル サーバーが、レプリケーション グループ全体でのファイルのレプリケートにアクティブに参加します。 メンバーの 1 つがオフラインになっても、いずれかのメンバーに要求を送信するすべてのクライアントが、レプリケートされたフォルダー内のコンテンツを使用できます。

## <a name="disaster-recovery-recommendations-for-file-servers"></a>ファイル サーバーのディザスター リカバリーの推奨事項

* **Site Recovery を使用してファイル サーバーをレプリケートする**: Site Recovery を使用して、ファイル サーバーを Azure にレプリケートできます。 オンプレミスの 1 つ以上のファイル サーバーにアクセスできない場合、復旧 VM を Azure で起動できます。 Azure でサイト間 VPN 接続と Active Directory が構成されていれば、これらの VM がクライアントからの要求をオンプレミスで処理できます。 この方法は、DFSR が構成された環境でも、DFSR を使用しない単純なファイル サーバー環境でも使用できます。 

* **DFSR を Azure IaaS VM に拡張する**: DFSR が実装されたクラスター化されたファイル サーバー環境では、オンプレミスの DFSR を Azure に拡張できます。 この場合、Azure VM がファイル サーバーの役割を果たすことができます。 

    * サイト間 VPN 接続と Active Directory の依存関係が処理され、DFSR の準備が整うと、オンプレミスの 1 つ以上のファイル サーバーにアクセスできなくても、クライアントは Azure VM に接続して要求を処理できます。

    * VM の構成が Site Recovery でサポートされていない場合は、この方法を使用できます。 例として、ファイル サーバー環境でよく使用される共有クラスター ディスクが挙げられます。 DFSR は、チャーン レートが中程度の低帯域幅の環境でも適切に機能します。 Azure VM を常時稼働させておくための追加コストを考慮する必要があります。 

* **Azure File Sync を使用してファイルをレプリケートする**: クラウドを使用する予定があるか、または既に Azure VM を使用している場合は、Azure File Sync を使用できます。Azure File Sync は、業界標準の [サーバー メッセージ ブロック](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (SMB) プロトコルを介してアクセスできる、クラウドでのフル マネージドのファイル共有の同期を提供します。 Azure ファイル共有は、クラウドまたはオンプレミスにある Windows、Linux、および macOS に同時にマウントできます。 

次のダイアグラムは、ファイル サーバー環境で使用する戦略を決定する際に役立ちます。

![デシジョン ツリー](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーに関する意思決定で考慮すべき要素

|環境  |推奨  |考慮すべき点 |
|---------|---------|---------|
|ファイル サーバー環境 (DFSR あり/なし)|   [Site Recovery を使用したレプリケーション](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery では、共有ディスク クラスターとネットワーク接続ストレージ (NAS) はサポートされません。 環境でこれらの構成を使用する場合は、他の方法を適宜使用してください。 <br> Site Recovery では SMB 3.0 はサポートされません。 レプリケートされた VM にファイルの変更が反映されるのは、変更がファイルの元の場所で更新された場合だけです。<br>  Site Recovery では、ほぼ同期で実行されるデータ レプリケーション プロセスが提供されます。そのため、予定外のフェールオーバーが発生した場合、データ損失と USN 不一致問題が発生する可能性があります。
|ファイル サーバー環境 (DFSR あり)     |  [Azure IaaS 仮想マシンへの DFSR の拡張](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |    DFSR は、帯域幅が極端に不足している環境でも適切に機能します。 この方法では、Azure VM を常時稼働させておく必要があります。 計画で VM のコストを考慮する必要があります。         |
|Azure Iaas VM     |     File Sync    |     ディザスター リカバリー シナリオで File Sync を使用する場合は、フェールオーバー時に手動アクションを実行して、クライアント マシンがファイル共有に透過的にアクセスできるようにする必要があります。 File Sync では、クライアント マシンからポート 445 を開いておく必要があります。     |


### <a name="site-recovery-support"></a>Site Recovery のサポート
Site Recovery レプリケーションはアプリケーションに依存しないため、ここで紹介する推奨事項は次のシナリオに適用できます。

| source  |セカンダリ サイトへ  |Azure へ
|---------|---------|---------|
|Azure|  -|はい|
|Hyper-V|  はい  |はい
|VMware  |はい|  はい
|物理サーバー|  はい  |はい
 

> [!IMPORTANT]
> 以下の 3 つの方法のいずれかを使用する前に、次の依存関係を考慮してください。



**サイト間接続**: サーバー間の通信を許可するには、オンプレミスのサイトと Azure ネットワークの間の直接接続を確立する必要があります。 ディザスター リカバリー サイトとして使用する Azure 仮想ネットワークへのセキュリティで保護されたサイト間 VPN 接続を使用します。 詳細については、[オンプレミス サイトと Azure 仮想ネットワーク間のサイト間 VPN 接続の確立](../vpn-gateway/tutorial-site-to-site-portal.md)に関する記事をご覧ください。

**Active Directory**: DFSR は Active Directory に依存します。 つまり、ローカル ドメイン コントローラーを使用する Active Directory フォレストが Azure のディザスター リカバリー サイトに拡張されます。 DFSR を使用しない場合でも、対象のユーザーにアクセス権を付与したり、アクセスを検証したりする必要がある場合は、これらの手順を実行してください。 詳細については、[Azure へのオンプレミス Active Directory の拡張](./site-recovery-active-directory.md)に関する記事をご覧ください。

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 仮想マシンのディザスター リカバリーの推奨事項

Azure IaaS VM でホストされるファイル サーバーのディザスター リカバリーを構成および管理する場合は、[Azure Files](../storage/files/storage-files-introduction.md) に移動するかどうかに基づいて、次の 2 つの方法のいずれかを選択できます。

* [File Sync を使用する](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Site Recovery を使用する](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>File Sync を使用して IaaS 仮想マシンでホストされるファイルをレプリケートする

Azure Files を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイスを完全に置き換えたり、補完することができます。 また、File Sync を使用して、オンプレミスまたはクラウドにある Windows サーバーに Azure ファイル共有をレプリケートすることで、使用されているデータを分散キャッシュしてパフォーマンスを向上させることもできます。 以下の手順では、従来のファイル サーバーと同じ機能を実行する Azure VM のディザスター リカバリーの推奨事項を示します。
* Site Recovery を使用してマシンを保護します。 [Azure VM を別の Azure リージョンにレプリケートする](azure-to-azure-quickstart.md)方法に関する記事の手順に従います。
* File Sync を使用して、ファイル サーバーとして機能する VM からクラウドにファイルをレプリケートします。
* Site Recovery の[復旧計画](site-recovery-create-recovery-plans.md)機能を使用し、[Azure ファイル共有をマウント](../storage/files/storage-how-to-use-files-windows.md)して仮想マシン内の共有にアクセスするスクリプトを追加します。

次の手順では、File Sync の使用方法を簡単に説明します。

1. [Azure にストレージ アカウントを作成します](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json)。 ストレージ アカウントに対して読み取りアクセス geo 冗長ストレージを選択した場合は、障害時にセカンダリ リージョンからデータに読み取りアクセスできます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)」を参照してください。
2. [ファイル共有を作成します](../storage/files/storage-how-to-create-file-share.md)。
3. Azure ファイル サーバーで [File Sync を開始](../storage/files/storage-sync-files-deployment-guide.md)します。
4. 同期グループを作成します。 同期グループ内のエンドポイントは、相互に同期を維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つは含まれている必要があります。 また、Windows サーバー上のパスを表すサーバー エンドポイントも 1 つ含まれている必要があります。
5. Azure ファイル共有とオンプレミスのサーバーでファイルの同期が維持されます。
6. オンプレミス環境で障害が発生した場合は、[復旧計画](site-recovery-create-recovery-plans.md)を使用してフェールオーバーを実行します。 [Azure ファイル共有をマウント](../storage/files/storage-how-to-use-files-windows.md)して仮想マシン内の共有にアクセスするスクリプトを追加します。

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Site Recovery を使用して IaaS ファイル サーバー仮想マシンをレプリケートする

IaaS ファイル サーバー仮想マシンにアクセスするオンプレミスのクライアントがある場合は、次の手順をすべて実行します。 それ以外の場合は、手順 3 に進みます。

1. オンプレミス サイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。
2. オンプレミスの Active Directory を拡張します。
3. セカンダリ リージョンへの IaaS ファイル サーバー マシンの[ディザスター リカバリーを設定](azure-to-azure-tutorial-enable-replication.md)します。


セカンダリ リージョンへのディザスター リカバリーの詳細については、[こちらの記事](./azure-to-azure-architecture.md)をご覧ください。


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Site Recovery を使用してオンプレミスのファイル サーバーをレプリケートする

次の手順では、VMware VM のレプリケーションについて説明します。 Hyper-V VM をレプリケートする手順については、[こちらのチュートリアル](./hyper-v-azure-tutorial.md)をご覧ください。

1. オンプレミス マシンのレプリケーションのために [Azure リソースを準備](tutorial-prepare-azure.md)します。
2. オンプレミス サイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。 
3. オンプレミスの Active Directory を拡張します。
4. [オンプレミスの VMware サーバーを準備します](./vmware-azure-tutorial-prepare-on-premises.md)。
5. Azure にオンプレミス VM の[ディザスター リカバリーを設定](./vmware-azure-tutorial.md)します。

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>DFSR を Azure IaaS 仮想マシンに拡張する

1. オンプレミス サイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。 
2. オンプレミスの Active Directory を拡張します。
3. Azure 仮想ネットワークで[ファイル サーバー VM を作成してプロビジョニング](../virtual-machines/windows/quick-create-portal.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)します。
オンプレミス環境とクロス接続された同じ Azure 仮想ネットワークに仮想マシンが追加されていることを確認します。 
4. Windows Server に DFSR をインストールして[構成](https://techcommunity.microsoft.com/t5/storage-at-microsoft/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of/ba-p/424877)します。
5. [DFS 名前空間を実装](/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces)します。
6. DFS 名前空間を実装すると、DFS 名前空間のフォルダー ターゲットを更新することで、運用サイトからディザスター リカバリー サイトへの共有フォルダーのフェールオーバーを実行できます。 Active Directory を使用して DFS 名前空間の変更がレプリケートされると、ユーザーは適切なフォルダー ターゲットに透過的に接続されます。

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>File Sync を使用してオンプレミス ファイルをレプリケートする
File Sync を使用すると、ファイルをクラウドにレプリケートできます。 障害が発生した場合やオンプレミスのファイル サーバーが使用できない場合に、目的のファイルの場所をクラウドからマウントし、クライアント マシンからの要求を引き続き処理できます。
File Sync を Site Recovery と統合する方法は次のとおりです。

* Site Recovery を使用して、ファイル サーバー マシンを保護します。 [こちらのチュートリアル](./vmware-azure-tutorial.md)の手順に従います。
* File Sync を使用して、ファイル サーバーとして機能するマシンからクラウドにファイルをレプリケートします。
* Site Recovery の復旧計画機能を使用して、Azure のフェールオーバーされたファイル サーバー VM で Azure ファイル共有をマウントするスクリプトを追加します。

File Sync を使用するには、次の手順に従います。

1. [Azure にストレージ アカウントを作成します](../storage/common/storage-account-create.md?toc=/azure/storage/files/toc.json)。 ストレージ アカウントに対して読み取りアクセス geo 冗長ストレージ (推奨) を選択した場合は、障害時にセカンダリ リージョンからデータに読み取りアクセスできます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)」を参照してください。
2. [ファイル共有を作成します](../storage/files/storage-how-to-create-file-share.md)。
3. オンプレミスのファイル サーバーに [File Sync をデプロイ](../storage/files/storage-sync-files-deployment-guide.md)します。
4. 同期グループを作成します。 同期グループ内のエンドポイントは、相互に同期を維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つは含まれている必要があります。 また、オンプレミスの Windows サーバー上のパスを表すサーバー エンドポイントも 1 つ含まれている必要があります。
5. Azure ファイル共有とオンプレミスのサーバーでファイルの同期が維持されます。
6. オンプレミス環境で障害が発生した場合は、[復旧計画](site-recovery-create-recovery-plans.md)を使用してフェールオーバーを実行します。 Azure ファイル共有をマウントして仮想マシン内の共有にアクセスするスクリプトを追加します。

> [!NOTE]
> ポート 445 が開いていることを確認します。 Azure Files では SMB プロトコルを使用します。 SMB は TCP ポート 445 経由で通信します。 ファイアウォールがクライアント マシンの TCP ポート 445 をブロックしていないかどうかを確認してください。


## <a name="do-a-test-failover"></a>テスト フェールオーバーを実行する

1. Azure Portal に移動し、Recovery Services コンテナーを選択します。
2. ファイル サーバー環境用に作成された復旧計画を選択します。
3. **[テスト フェールオーバー]** を選択します。
4. 復旧ポイントと Azure 仮想ネットワークを選択して、テスト フェールオーバー プロセスを開始します。
5. セカンダリ環境が立ち上がったら、検証を実行します。
6. 検証が完了したら、復旧計画で **[テスト フェールオーバーのクリーンアップ]** を選択します。テスト フェールオーバー環境がクリーンアップされます｡

テスト フェールオーバーを実行する方法の詳細については、[Site Recovery でのテスト フェールオーバー](site-recovery-test-failover-to-azure.md)に関する記事をご覧ください。

Active Directory と DNS のテスト フェールオーバーの実行に関するガイダンスについては､[Active Directory と DNS のテスト フェールオーバーの考慮事項](site-recovery-active-directory.md)に関する記事をご覧ください｡

## <a name="do-a-failover"></a>フェールオーバーを実行する

1. Azure Portal に移動し､Recovery Services コンテナーを選択します｡
2. ファイル サーバー環境用に作成された復旧計画を選択します。
3. **[フェールオーバー]** を選択します。
4. 復旧ポイントを選択して、フェールオーバー プロセスを開始します。

フェールオーバーを実行する方法の詳細については、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。