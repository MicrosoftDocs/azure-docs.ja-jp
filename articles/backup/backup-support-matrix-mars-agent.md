---
title: Azure Backup を使用した Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターのバックアップのサポート マトリックス
description: この記事では、Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターをバックアップする場合の Azure Backup のサポートを要約しています。
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: d9f3c9b94a093df539a6ca4660383837becf1709
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464845"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントを使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとアプリをバックアップしたり、Azure 仮想マシン (VM) をバックアップしたりできます。 この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用してコンピューターをバックアップする場合のサポートの設定および制限を要約しています。

## <a name="the-mars-agent"></a>MARS エージェント

Azure Backup は、MARS エージェントを使用して、オンプレミスのコンピューターや Azure VM から Azure の Backup Recovery Services コンテナーにデータをバックアップします。 MARS エージェントは次の動作が可能です。
- Azure の Backup Recovery Services コンテナーに直接バックアップできるように、オンプレミスの Windows コンピューター上で動作できます。
- コンテナーに直接バックアップできるように、Windows VM 上で動作できます。
- Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) サーバー上で動作できます。 このシナリオでは、コンピューターとワークロードは MABS または DPM サーバーにバックアップされます。 その後、MARS エージェントがこのサーバーを Azure のコンテナーにバックアップします。

バックアップ オプションは、エージェントがインストールされている場所によって異なります。 詳細については、[MARS エージェントを使用した Azure Backup アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)に関するページを参照してください。 MABS と DPM のバックアップ アーキテクチャについては、[DPM または MABS へのバックアップ](backup-architecture.md#architecture-back-up-to-dpmmabs)に関するページを参照してください。 バックアップ アーキテクチャに関する[要件](backup-support-matrix-mabs-dpm.md)も参照してください。

**インストール** | **詳細**
--- | ---
最新の MARS エージェントをダウンロードする | 最新バージョンのエージェントはコンテナーからダウンロードするか、または[直接ダウンロード](https://aka.ms/azurebackup_agent)できます。
コンピューターに直接インストールする | MARS エージェントは、オンプレミスの Windows サーバー、またはいずれかの[サポートされるオペレーティング システム](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)を実行している Windows VM に直接インストールできます。
バックアップ サーバーにインストールする | Azure にバックアップするように DPM または MABS を設定する場合は、そのサーバーに MARS エージェントをダウンロードしてインストールします。 バックアップ サーバーのサポート マトリックス内の[サポートされるオペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)にエージェントをインストールできます。

> [!NOTE]
> 既定では、バックアップが有効になっている Azure VM には Azure Backup 拡張機能がインストールされています。 この拡張機能は、VM 全体をバックアップします。 VM 全体ではなく、特定のフォルダーやファイルをバックアップする場合は、この拡張機能と共に MARS エージェントを Azure VM にインストールして実行できます。
> MARS エージェントは、Azure VM 上で実行されると、その VM 上の一時ストレージにあるファイルまたはフォルダーをバックアップします。 それらのファイルまたはフォルダーが一時ストレージから削除されたり、一時ストレージが削除されたりすると、バックアップは失敗します。


## <a name="cache-folder-support"></a>キャッシュ フォルダーのサポート

MARS エージェントを使用してデータをバックアップする場合、エージェントはそのデータのスナップショットを取得し、それをローカルのキャッシュ フォルダーに格納してからデータを Azure に送信します。 キャッシュ (スクラッチ) フォルダーには、次のいくつかの要件があります。

**キャッシュ** | **詳細**
--- | ---
Size |  キャッシュ フォルダー内の空き領域は、バックアップ データのサイズ全体の少なくとも 5 ～ 10% である必要があります。
Location | キャッシュ フォルダーは、バックアップされるコンピューターにローカルに格納されており、かつオンラインである必要があります。 キャッシュ フォルダーがネットワーク共有、リムーバブル メディア、またはオフライン ボリュームに存在していてはいけません。
Folder | キャッシュ フォルダーは、重複除去されたボリューム上か、圧縮されたフォルダー、スパース フォルダー、または再解析ポイントを含むフォルダー内で暗号化されている必要があります。
場所の変更 | キャッシュの場所は、バックアップ エンジンを停止し (`net stop bengine`)、キャッシュ フォルダーを新しいドライブにコピーすることによって変更できます。 (新しいドライブに十分な領域があることを確認してください。)その後、**HKLM\SOFTWARE\Microsoft\Windows Azure Backup** にある 2 つのレジストリ エントリ (**Config/ScratchLocation** と **Config/CloudBackupProvider/ScratchLocation**) を新しい場所に更新し、エンジンを再起動します。

## <a name="networking-and-access-support"></a>ネットワークとアクセスのサポート

### <a name="url-access"></a>URL アクセス

MARS エージェントには、次の URL へのアクセス権が必要です。

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>調整のサポート

**機能** | **詳細**
--- | ---
帯域幅の制御 | サポートされています。 MARS エージェントで、 **[プロパティの変更]** を使用して帯域幅を調整します。
Network throttling | Windows Server 2008 R2、Windows Server 2008 SP2、または Windows 7 を実行しているバックアップ済みのコンピューターでは使用できません。

## <a name="support-for-direct-backups"></a>直接バックアップのサポート

MARS エージェントを使用すると、オンプレミスのコンピューターや Azure VM で実行されている一部のオペレーティング システム上の Azure に直接バックアップできます。 これらのオペレーティング システムは 64 ビットであり、かつ最新の Service Pack および更新プログラムが実行されている必要があります。 次の表は、これらのオペレーティング システムをまとめたものです。

**オペレーティング システム** | **ファイル/フォルダー** | **システム状態**
--- | --- | ---
Windows 10 (Enterprise、Pro、Home) | はい | いいえ
Windows 8.1 (Enterprise、Pro)| はい |いいえ
Windows 8 (Enterprise、Pro) | はい | いいえ
Windows 7 (Ultimate、Enterprise、Pro、Home Premium/Basic、Starter) | はい | いいえ
Windows Server 2016 (Standard、Datacenter、Essentials) | はい | はい
Windows Server 2012 R2 (Standard、Datacenter、Foundation、Essentials) | はい | はい
Windows Server 2012 (Standard、Datacenter、Foundation) | はい | はい
Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | はい | はい
Windows Server 2008 SP2 (Standard、Datacenter、Foundation) | はい | いいえ
Windows Storage Server 2016/2012 R2/2012 (Standard、Workgroup) | はい | いいえ

詳細については、「[サポートされている MABS および DPM オペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)」を参照してください。

## <a name="backup-limits"></a>Backup の制限

Azure Backup では、バックアップできるファイルまたはフォルダー データ ソースのサイズが制限されています。 1 つのボリュームからバックアップする項目が、次の表に要約されているサイズを超えることはできません。

**オペレーティング システム** | **サイズの制限**
--- | ---
Windows Server 2012 またはそれ以降 |  54,400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 以降  | 54,400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>バックアップ用にサポートされるファイルの種類

**Type** | **サポート**
--- | ---
暗号化   | サポートされています。
圧縮 | サポートされています。
スパース | サポートされています。
圧縮 + スパース | サポートされています。
ハード リンク  | サポートされていません。 スキップされます。
再解析ポイント   | サポートされていません。 スキップされます。
暗号化 + スパース |  サポートされていません。 スキップされます。
圧縮ストリーム   | サポートされていません。 スキップされます。
スパース ストリーム   | サポートされていません。 スキップされます。
OneDrive (同期されるファイルはスパース ストリーム)  | サポートされていません。

## <a name="supported-drives-or-volumes-for-backup"></a>バックアップ用にサポートされるドライブまたはボリューム

**ドライブ/ボリューム** | **サポート** | **詳細**
--- | --- | ---
読み取り専用ボリューム   | サポートされていません | ボリューム コピー シャドウ サービス (VSS) は、ボリュームが書き込み可能な場合にのみ機能します。
オフライン ボリューム | サポートされていません |   VSS は、ボリュームがオンラインである場合にのみ機能します。
ネットワーク共有   | サポートされていません |   サーバー上でボリュームがローカルである必要があります。
Bitlocker で保護されているボリューム | サポートされていません |   バックアップを開始する前に、ボリュームのロックを解除する必要があります。
ファイル システムの識別  | サポートされていません |   NTFS のみがサポートされます。
リムーバブル メディア | サポートされていません |   バックアップ項目のすべてのソースが*固定*の状態である必要があります。
重複除去されたドライブ | サポートされています | Azure Backup は、重複除去されたデータを通常のデータに変換します。 データを最適化および暗号化して格納し、コンテナーに送信します。

## <a name="support-for-initial-offline-backup"></a>初期のオフライン バックアップのサポート

Azure Backup は、ディスクを使用して初期バックアップ データを Azure に転送する*オフライン シード処理*をサポートしています。 このサポートは、初期バックアップが数テラバイト (TB) のサイズ範囲にある可能性がある場合に役立ちます。 オフライン バックアップのサポート対象は次のとおりです。

- MARS エージェントを実行しているオンプレミスのコンピューター上でのファイルやフォルダーの直接バックアップ。
- DPM サーバーまたは MABS からのワークロードやファイルのバックアップ。

オフライン バックアップはシステム状態ファイルには使用できません。

## <a name="support-for-data-restoration"></a>データ復元のサポート

Azure Backup の[インスタント リストア](backup-instant-restore-capability.md)機能を使用すると、データをコンテナーにコピーされる前に復元できます。 バックアップするコンピューターが .NET Framework 4.5.2 以降を実行している必要があります。

バックアップを、以前のバージョンのオペレーティング システムを実行しているターゲット コンピューターに復元することはできません。 たとえば、Windows 7 を実行しているコンピューターから取得されたバックアップは Windows 8 以降で復元できます。 ただし、Windows 7 を実行しているコンピューター上で、Windows 8 を実行しているコンピューターから取得されたバックアップを復元することはできません。

## <a name="next-steps"></a>次の手順
- [MARS エージェントを使用するバックアップ アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)の詳細を学習します。
- [MABS または DPM サーバー上で MARS エージェントを実行する](backup-support-matrix-mabs-dpm.md)場合にサポートされる内容を学習します。
