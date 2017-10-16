---
title: "Azure VM バックアップの FAQ | Microsoft Docs"
description: "一般的な質問への回答: Azure VM バックアップの動作、制限、ポリシーの変更があったときに起こること"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "azure vm バックアップ, azure vm 復元, バックアップ ポリシー"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 1372a9e05cb47f6c68240bffccd46b0fbebb5464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Azure VM バックアップ サービスについての質問
この記事では、Azure VM バックアップの構成要素が理解しやすいよう、よく寄せられる質問とその回答を記載しています。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## <a name="configure-backup"></a>バックアップの構成
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Recovery Services コンテナーでサポートされるのはクラシック VM と Resource Manager ベースの VM のどちらですか。 <br/>
Recovery Services コンテナーでは両方のモデルがサポートされています。  (クラシック ポータルで作成された) クラシック VM または (Azure Portal で作成された) Resource Manager VM を、Recovery Services コンテナーにバックアップできます。

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Azure VM バックアップでサポートされない構成は、どのようなものですか。
[サポートされるオペレーティング システム](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup)と [VM のバックアップの制限](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)に関するページを参照してください。

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>バックアップ構成ウィザードに VM が表示されません。なぜでしょうか。
バックアップの構成ウィザードで Azure Backup の対象として表示されるのは、次に該当する VM だけです。
* まだ保護されていない - VM のバックアップ状態は、VM ブレードに移動し、ブレードの設定メニューの [バックアップ状態] を見て確認できます。 詳細については、[VM のバックアップ状態の確認](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)方法に関するページを参照してください。
* VM と同じリージョンに属している

## <a name="backup"></a>バックアップ
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>オンデマンド バックアップ ジョブのリテンション期間スケジュールは、スケジュールされたバックアップと同じでしょうか。
いいえ。 オンデマンド バックアップ ジョブのリテンション期間はご自身で指定する必要があります。 ポータルからトリガーした場合、既定で 30 日間保存されます。 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>最近いくつかの VM で Azure Disk Encryption を有効にしました。 既存のバックアップは今後も正常に機能しますか。
Azure Backup サービスに Key Vault へのアクセス許可を与える必要があります。 PowerShell でこうしたアクセス許可を付与するには、[PowerShell](backup-azure-vms-automation.md) ドキュメントの "*バックアップの有効化*" に関するセクションに記載されている手順を使用します。

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>VM のディスクを Managed Disks に移行しました。 既存のバックアップは今後も正常に機能しますか。
はい。バックアップはシームレスに機能します。バックアップを再構成する必要はありません。 

## <a name="restore"></a>復元
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>ディスクの復元と完全 VM 復元とは、どのように使い分ければよいでしょうか。
Azure の完全 VM 復元は、VM をすばやく復元して作成する方法の 1 つと考えてください。 [VM の復元] を選択すると、VM 作成の過程で作成されるリソースの一意性を確保するために、ディスクの名前や、ディスクで使用されるコンテナー、パブリック IP アドレス、ネットワーク インターフェイス名が変更されます。また、その VM は可用性セットに追加されません。 

ディスクの復元は、次のような場合に使用してください。
* バックアップ構成からサイズを変更するなど、特定時点の構成から作成された VM をカスタマイズする。
* バックアップの時点では存在しなかった構成を追加する。 
* 作成されるリソースの名前付け規則を制御する。
* 可用性セットに VM を追加する。
* PowerShell または宣言型のテンプレート定義でしか実現できない構成がある。

## <a name="manage-vm-backups"></a>VM バックアップの管理
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>VM のバックアップ ポリシーを変更した場合どうなりますか。
新しいポリシーを VMに適用すると、新しいポリシーのスケジュールとリテンション期間が適用されます。 リテンション期間が延長された場合、既にある復旧ポイントは、新しいポリシーに従って保存するようにマーキングされます。 リテンション期間が短縮された場合、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。 
