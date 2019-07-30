---
title: Microsoft Azure Backup Server の新機能
description: Microsoft Azure Backup Server には、VM、ファイルとフォルダー、ワークロードなどを保護するための高度なバックアップ機能があります。 Azure Backup Server V3 をインストールまたはアップグレードする方法を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 131f5ae31649bd1973a3048b8b52a451f27522b4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465000"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server の新機能

Microsoft Azure Backup Server バージョン 3 (MABS V3) は最新のアップグレードであり、重大なバグ修正、Windows Server 2019 のサポート、SQL 2017 のサポート、その他の機能強化が含まれています。 MABS V3 の修正されたバグの一覧とインストール手順については、KB 記事 [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3) を参照してください。

MABS V3 には、次の機能が含まれています。

## <a name="volume-to-volume-migration"></a>ボリュームからボリュームへの移行
MABS V2 の Modern Backup Storage (MBS) では、ワークロード対応ストレージが発表されており、ストレージのプロパティに基づいて、特定のワークロードを特定のストレージにバックアップするように構成することができます。 ただし、構成後に、リソースの使用状況を最適化するために、特定のデータ ソースのバックアップを他のストレージに移動する処理が必要になることがあります。 MABS V3 には、[3 つの手順](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/)で、バックアップを移行し、別のボリュームに保存するように構成する機能があります。

## <a name="prevent-unexpected-data-loss"></a>予想外のデータ損失を防ぐ
企業では、複数の管理者のチームが MABS を管理されます。 バックアップへの使用が推奨されるストレージのガイドラインはありますが、バックアップ ストレージとして MABS に不適切なボリュームを指定すると、重要なデータが失われる可能性があります。 MABS V3 では、[これらの PowerShell コマンドレット](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)を使用して、このようなボリュームをストレージに使用できないボリュームとして構成することで、こうしたシナリオを防ぐことができます。

## <a name="custom-size-allocation"></a>カスタム サイズの割り当て
Modern Backup Storage (MBS) は、必要に応じてストレージの使用量を低下させます。 この処理を行うために、MABS では、保護用に構成されているときにバックアップされるデータのサイズが計算されます。 ただし、ファイル サーバーの場合のように、多数のファイルとフォルダーがまとめてバックアップされる場合は、サイズの計算に時間がかかることがあります。 MABS V3 を使用すると、各ファイルのサイズを計算するのではなく、既定としてボリューム サイズを受け入れるように MABS を構成することができるので、時間を節約できます。

## <a name="optimized-cc-for-rct-vms"></a>RCT VM のための最適化された CC
MABS は RCT (Hyper-V のネイティブの変更追跡機能) を使用しているため、VM がクラッシュしたときのシナリオで時間のかかる一貫性チェックの必要性が少なくなります。 RCT には、VSS スナップショットベースのバックアップによって提供される変更追跡よりも優れた回復性があります。 MABS V3 は、一貫性チェック中に変更されたデータのみを転送することで、ネットワークとストレージの消費をさらに最適化します。

## <a name="support-to-tls-12"></a>TLS 1.2 のサポート
TLS 1.2 は Microsoft が提案するセキュリティで保護された通信方法であり、クラス最高レベルの暗号化を使用しています。 MABS は、証明書ベースの認証とクラウド バックアップ用に、MABS と保護されたサーバー間で TLS 1.2 通信をサポートするようになりました。

## <a name="vmware-vm-protection-support"></a>VMware VM 保護のサポート
運用デプロイでは、VMware VM バックアップがサポートされるようになりました。 MABS V3 は、VMware VM 保護のために以下を提供しています。

-   vCenter および ESXi 6.5 のサポートと、5.5 および 6.0 のサポート
- VMware VM からクラウドへの自動保護。 新しい VMware VM が保護されたフォルダーに追加されると、ディスクおよびクラウドに対して自動的に保護されます。
- VMware の代替場所の復旧の場合に復旧効率を向上。

## <a name="sql-2017-support"></a>SQL 2017 のサポート
MABS V3 は、SQL 2017 と共に MABS データベースとしてインストールできます。 SQL Server を SQL 2016 から SQL 2017 にアップグレードするか、新規にインストールすることができます。 MABS V3 を使用すると、クラスター化された環境とクラスター化されていない環境の両方の SQL 2017 ワークロードをバックアップすることもできます。

## <a name="windows-server-2019-support"></a>Windows Server 2019 のサポート
MABS V3 は Windows Server 2019 にインストールすることができます。 WS2019 と共に MABS V3 を使用するには、OS を WS2019 にアップグレードしてから MABS V3 をインストールまたはアップグレードするか、WS2016 上に V3 をインストールまたはアップグレードした後に OS をアップグレードする方法があります。

MABS V3 は完全なリリースであり、Windows Server 2016、Windows Server 2019 に直接インストールするか、MABS V2 からアップグレードすることができます。 Backup Server V3 にアップグレードまたはインストールする前に、インストールの前提条件を確認してください。
MABS のインストールまたはアップグレード手順の詳細については、[こちら](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)を参照してください。


> [!NOTE]
>
> MABS には、System Center Data Protection Manager と同じコード ベースがあります。 MABS v3 は Data Protection Manager 1807 と同等です。

## <a name="next-steps"></a>次の手順

サーバーを準備する方法、またはワークロードの保護を開始する方法を説明します。
- [MABS V3 の既知の問題](backup-mabs-release-notes-v3.md)
- [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
- [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
- [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
- [Backup Server での Modern Backup Storage の使用](backup-mabs-add-storage.md)
