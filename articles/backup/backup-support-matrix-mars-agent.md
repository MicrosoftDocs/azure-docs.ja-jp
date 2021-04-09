---
title: MARS エージェントを使用したサポート マトリックス
description: この記事では、Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターをバックアップする場合の Azure Backup のサポートを要約しています。
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 2170440b7b47861b75801b8dbd334686b4cabc8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98985616"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントを使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとアプリをバックアップしたり、Azure 仮想マシン (VM) をバックアップしたりできます。 この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用してコンピューターをバックアップする場合のサポートの設定および制限を要約しています。

## <a name="the-mars-agent"></a>MARS エージェント

Azure Backup は、MARS エージェントを使用して、オンプレミスのコンピューターや Azure VM から Azure の Backup Recovery Services コンテナーにデータをバックアップします。 MARS エージェントは次の動作が可能です。

- Azure の Backup Recovery Services コンテナーに直接バックアップできるように、オンプレミスの Windows コンピューター上で動作できます。
- コンテナーに直接バックアップできるように、Windows VM 上で動作できます。
- Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) サーバー上で動作できます。 このシナリオでは、コンピューターとワークロードは MABS または DPM サーバーにバックアップされます。 その後、MARS エージェントがこのサーバーを Azure のコンテナーにバックアップします。

> [!NOTE]
>Azure Backup は、夏時間 (DST) の時計の自動調整をサポートしていません。 実際の時刻とスケジュールされたバックアップ時刻がずれないようにするため、夏時間を考慮に入れてポリシーを変更してください。

バックアップ オプションは、エージェントがインストールされている場所によって異なります。 詳細については、[MARS エージェントを使用した Azure Backup アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)に関するページを参照してください。 MABS と DPM のバックアップ アーキテクチャについては、[DPM または MABS へのバックアップ](backup-architecture.md#architecture-back-up-to-dpmmabs)に関するページを参照してください。 バックアップ アーキテクチャに関する[要件](backup-support-matrix-mabs-dpm.md)も参照してください。

**インストール** | **詳細**
--- | ---
最新の MARS エージェントをダウンロードする | 最新バージョンのエージェントはコンテナーからダウンロードするか、または[直接ダウンロード](https://aka.ms/azurebackup_agent)できます。
コンピューターに直接インストールする | MARS エージェントは、オンプレミスの Windows サーバー、またはいずれかの[サポートされるオペレーティング システム](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)を実行している Windows VM に直接インストールできます。
バックアップ サーバーにインストールする | Azure にバックアップするように DPM または MABS を設定する場合は、そのサーバーに MARS エージェントをダウンロードしてインストールします。 バックアップ サーバーのサポート マトリックス内の[サポートされるオペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)にエージェントをインストールできます。

> [!NOTE]
> 既定では、バックアップが有効になっている Azure VM には Azure Backup 拡張機能がインストールされています。 この拡張機能は、VM 全体をバックアップします。 VM 全体ではなく、特定のフォルダーやファイルをバックアップする場合は、この拡張機能と共に MARS エージェントを Azure VM にインストールして実行できます。
> MARS エージェントは、Azure VM 上で実行されると、その VM 上の一時ストレージにあるファイルまたはフォルダーをバックアップします。 それらのファイルまたはフォルダーが一時ストレージから削除されたり、一時ストレージが削除されたりすると、バックアップは失敗します。

## <a name="cache-folder-support"></a>キャッシュ フォルダーのサポート

MARS エージェントを使用してデータをバックアップする場合、エージェントはそのデータのスナップショットを取得し、それをローカルのキャッシュ フォルダーに格納してからデータを Azure に送信します。 キャッシュ (スクラッチ) フォルダーには、次のいくつかの要件があります。

**Cache** | **詳細**
--- | ---
サイズ |  キャッシュ フォルダー内の空き領域は、バックアップ データのサイズ全体の少なくとも 5 ～ 10% である必要があります。
場所 | キャッシュ フォルダーは、バックアップされるコンピューターにローカルに格納されており、かつオンラインである必要があります。 キャッシュ フォルダーがネットワーク共有、リムーバブル メディア、またはオフライン ボリュームに存在していてはいけません。
Folder | キャッシュ フォルダーは、重複除去されたボリューム上や、圧縮されたフォルダー、スパース フォルダー、または再解析ポイントを含むフォルダー内で暗号化されていてはいけません。
場所の変更 | キャッシュの場所は、バックアップ エンジンを停止し (`net stop bengine`)、キャッシュ フォルダーを新しいドライブにコピーすることによって変更できます。 (新しいドライブに十分な領域があることを確認してください。)その後、**HKLM\SOFTWARE\Microsoft\Windows Azure Backup** にある 2 つのレジストリ エントリ (**Config/ScratchLocation** と **Config/CloudBackupProvider/ScratchLocation**) を新しい場所に更新し、エンジンを再起動します。

## <a name="networking-and-access-support"></a>ネットワークとアクセスのサポート

### <a name="url-and-ip-access"></a>URL と IP アクセス

MARS エージェントには、次の URL へのアクセス権が必要です。

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net
- `www.msftconnecttest.com`

これらの IP アドレスに対しては、次のようになります。

- 20.190.128.0/18
- 40.126.0.0/18

上記のすべての URL と IP アドレスにアクセスするには、ポート 443 で HTTPS プロトコルを使用します。

MARS エージェントを使用して Azure VM からファイルとフォルダーをバックアップする場合、アクセスを許可するように Azure 仮想ネットワークを構成する必要もあります。 ネットワーク セキュリティ グループ (NSG) を使用する場合は、*AzureBackup* サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、Azure AD (*AzureActiveDirectory*) および Azure Storage (*Storage*) に対して同様の [NSG 規則](../virtual-network/network-security-groups-overview.md#service-tags)を作成することによって、認証とデータ転送のための接続を許可する必要もあります。 次の手順では、Azure Backup タグの規則を作成するプロセスについて説明します。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。
2. **[設定]** で **[送信セキュリティ規則]** を選択します。
3. **[追加]** を選択します。 [セキュリティ規則の設定](../virtual-network/manage-network-security-group.md#security-rule-settings)の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション **[宛先]** が *[サービス タグ]* に、 **[宛先サービス タグ]** が *[AzureBackup]* に設定されていることを確認します。
4. **[追加]** を選択して、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する NSG 送信セキュリティ規則も、同様に作成できます。 サービス タグの詳細については、[こちらの記事](../virtual-network/service-tags-overview.md)を参照してください。

### <a name="azure-expressroute-support"></a>Azure ExpressRoute のサポート

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

- Azure Backup (Recovery Services コンテナーの場所による)
- Azure Active Directory (12076:5060)
- Azure Storage (Recovery Services コンテナーの場所による)

詳細については、「[ExpressRoute ルーティングの要件](../expressroute/expressroute-routing.md#bgp)」を参照してください。

>[!NOTE]
>パブリック ピアリングは、新しい回線では非推奨です。

### <a name="private-endpoint-support"></a>プライベート エンドポイントのサポート

プライベート エンドポイントを使用して、サーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 Azure Active Directory は現在、プライベート エンドポイントをサポートしていないため、Azure Active Directory に必要な IP および FQDN には、別個に送信アクセスを許可する必要があります。

MARS エージェントを使用してオンプレミスのリソースをバックアップする場合は、バックアップ対象のリソースが含まれるオンプレミスのネットワークが、コンテナー用のプライベート エンドポイントが含まれる Azure VNet と確実にピアリングされているようにします。 その後で、MARS エージェントのインストールを続行し、バックアップを構成することができます。 ただし、バックアップ用のすべての通信が、ピアリングされたネットワークのみを介して行われるようにする必要があります。

MARS エージェントの登録後にそのコンテナー用のプライベート エンドポイントを削除した場合は、コンテナーを Recovery Services コンテナーに再登録する必要があります。 それらに対する保護を停止する必要はありません。

[Azure Backup のプライベート エンドポイント](private-endpoints.md)の詳細を確認してください。

### <a name="throttling-support"></a>調整のサポート

**機能** | **詳細**
--- | ---
帯域幅の制御 | サポートされています。 MARS エージェントで、 **[プロパティの変更]** を使用して帯域幅を調整します。
Network throttling | Windows Server 2008 R2、Windows Server 2008 SP2、または Windows 7 を実行しているバックアップ済みのコンピューターでは使用できません。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

>[!NOTE]
> MARS エージェントは、Windows Server Core SKU をサポートしていません。

MARS エージェントを使用すると、次のように実行されている一部のオペレーティング システム上の Azure に直接バックアップできます。

1. オンプレミスの Windows Server
2. Windows を実行中の Azure VM

これらのオペレーティング システムは 64 ビットであり、かつ最新の Service Pack および更新プログラムが実行されている必要があります。 次の表は、これらのオペレーティング システムをまとめたものです。

**オペレーティング システム** | **ファイル/フォルダー** | **システム状態** | **ソフトウェア/モジュールの要件**
--- | --- | --- | ---
Windows 10 (Enterprise、Pro、Home) | はい | いいえ |  ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows 8.1 (Enterprise、Pro)| はい |いいえ | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows 8 (Enterprise、Pro) | はい | いいえ | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows Server 2016 (Standard、Datacenter、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2012 R2 (Standard、Datacenter、Foundation、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2012 (Standard、Datacenter、Foundation) | はい | はい |- .NET 4.5 <br> \- Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0 <br> - 展開イメージのサービスと管理 (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard、Workgroup) | はい | いいえ | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2019 (Standard、Datacenter、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0

詳細については、「[サポートされている MABS および DPM オペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)」を参照してください。

### <a name="operating-systems-at-end-of-support"></a>サポート終了のオペレーティング システム

以下のオペレーティング システムはサポートが終了しました。引き続き保護された状態を維持するため、オペレーティング システムをアップグレードすることを強くお勧めします。

既存のコミットメントによってオペレーティング システムをアップグレードできない場合は、Windows Server を Azure VM に移行し、Azure VM バックアップを利用して、保護された状態を継続することを検討してください。 Windows Server の移行の詳細については、[こちらの移行に関するページ](https://azure.microsoft.com/migration/windows-server/)を参照してください。

オペレーティング システムのアップグレードや Azure への移行ができないオンプレミス環境やホストされた環境の場合は、引き続き保護とサポートの対象となる状態に留まるため、マシンに対する拡張セキュリティ更新プログラムをアクティブにしてください。 拡張セキュリティ更新プログラムの対象となるのは、特定のエディションのみであること注意してください。 詳細については、[FAQ ページ](https://www.microsoft.com/windows-server/extended-security-updates)を参照してください。

| **オペレーティング システム**                                       | **ファイル/フォルダー** | **システム状態** | **ソフトウェア/モジュールの要件**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate、Enterprise、Pro、Home Premium/Basic、Starter) | はい               | いいえ                 | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください |
| Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | はい               | はい                | - .NET 3.5、.NET 4.5 <br>  - Windows PowerShell <br>  - 互換性のある Microsoft VC++ 再頒布可能パッケージ <br>  - Microsoft 管理コンソール (MMC) 3.0 <br>  - 展開イメージのサービスと管理 (DISM.exe) |
| Windows Server 2008 SP2 (Standard、Datacenter、Foundation)  | はい               | いいえ                 | - .NET 3.5、.NET 4.5 <br>  - Windows PowerShell <br>  - 互換性のある Microsoft VC++ 再頒布可能パッケージ <br>  - Microsoft 管理コンソール (MMC) 3.0 <br>  - 展開イメージのサービスと管理 (DISM.exe) <br>  - Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>Backup の制限

### <a name="size-limits"></a>サイズ制限

Azure Backup では、バックアップできるファイルまたはフォルダー データ ソースのサイズが制限されています。 1 つのボリュームからバックアップする項目が、次の表に要約されているサイズを超えることはできません。

**オペレーティング システム** | **サイズの制限**
--- | ---
Windows Server 2012 またはそれ以降 |54,400 GB
Windows Server 2008 R2 SP1 |1,700 GB
Windows Server 2008 SP2| 1,700 GB
Windows 8 以降| 54,400 GB
Windows 7| 1,700 GB

### <a name="minimum-retention-limits"></a>最小保有期間の制限

さまざまな回復ポイントに対して設定できる最小の保有期間は次のとおりです。

|回復ポイント |Duration  |
|---------|---------|
|日次の回復ポイント    |   7 日      |
|週次の回復ポイント     |    4 週間     |
|月次の回復ポイント    |   3 か月      |
|年次の回復ポイント  |      1 年   |

### <a name="other-limitations"></a>その他の制限事項

- MARS では、1 つのコンテナーに対して、同じ名前を持つ複数のマシンの保護をサポートしていません。

## <a name="supported-file-types-for-backup"></a>バックアップ用にサポートされるファイルの種類

**Type** | **サポート**
--- | ---
暗号化<sup>*</sup>| サポートされています。
Compressed | サポートされています。
スパース | サポートされています。
圧縮 + スパース |サポートされています。
ハード リンク| サポートされていません。 スキップされます。
再解析ポイント| サポートされていません。 スキップされます。
暗号化 + スパース |サポートされていません。 スキップされます。
圧縮ストリーム| サポートされていません。 スキップされます。
スパース ストリーム| サポートされていません。 スキップされます。
OneDrive (同期されるファイルはスパース ストリーム)| サポートされていません。
DFS レプリケーションが有効になっているフォルダー | サポートされていません。

\* MARS エージェントが、暗号化されたファイルにアクセスするために必要な証明書にアクセスできることを確認してください。 アクセスできないファイルはスキップされます。

## <a name="supported-drives-or-volumes-for-backup"></a>バックアップ用にサポートされるドライブまたはボリューム

**ドライブ/ボリューム** | **サポート** | **詳細**
--- | --- | ---
読み取り専用ボリューム| サポートされていません | ボリューム コピー シャドウ サービス (VSS) は、ボリュームが書き込み可能な場合にのみ機能します。
オフライン ボリューム| サポートされていません |VSS は、ボリュームがオンラインである場合にのみ機能します。
ネットワーク共有| サポートされていません |サーバー上でボリュームがローカルである必要があります。
BitLocker でロックされているボリューム| サポートされていません |バックアップを開始する前に、ボリュームのロックを解除する必要があります。
ファイル システムの識別| サポートされていません |NTFS のみがサポートされます。
リムーバブル メディア| サポートされていません |バックアップ項目のすべてのソースが *固定* の状態である必要があります。
重複除去されたドライブ | サポートされています | Azure Backup は、重複除去されたデータを通常のデータに変換します。 データを最適化および暗号化して格納し、コンテナーに送信します。

## <a name="support-for-initial-offline-backup"></a>初期のオフライン バックアップのサポート

Azure Backup は、ディスクを使用して初期バックアップ データを Azure に転送する *オフライン シード処理* をサポートしています。 このサポートは、初期バックアップが数テラバイト (TB) のサイズ範囲にある可能性がある場合に役立ちます。 オフライン バックアップのサポート対象は次のとおりです。

- MARS エージェントを実行しているオンプレミスのコンピューター上でのファイルやフォルダーの直接バックアップ。
- DPM サーバーまたは MABS からのワークロードやファイルのバックアップ。

オフライン バックアップはシステム状態ファイルには使用できません。

## <a name="support-for-data-restoration"></a>データ復元のサポート

Azure Backup の[インスタント リストア](backup-instant-restore-capability.md)機能を使用すると、データをコンテナーにコピーされる前に復元できます。 バックアップするコンピューターが .NET Framework 4.5.2 以降を実行している必要があります。

バックアップを、以前のバージョンのオペレーティング システムを実行しているターゲット コンピューターに復元することはできません。 たとえば、Windows 7 を実行しているコンピューターから取得されたバックアップは Windows 8 以降で復元できます。 ただし、Windows 7 を実行しているコンピューター上で、Windows 8 を実行しているコンピューターから取得されたバックアップを復元することはできません。

## <a name="next-steps"></a>次のステップ

- [MARS エージェントを使用するバックアップ アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)の詳細を学習します。
- [MABS または DPM サーバー上で MARS エージェントを実行する](backup-support-matrix-mabs-dpm.md)場合にサポートされる内容を学習します。
