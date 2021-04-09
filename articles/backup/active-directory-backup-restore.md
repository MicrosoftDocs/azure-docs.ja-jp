---
title: Active Directory のバックアップおよび復元
description: Active Directory ドメイン コントローラーのバックアップと復元の方法について説明します。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733555"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Active Directory ドメイン コントローラーのバックアップおよび復元

Active Directory のバックアップと、破損、セキュリティ侵害、障害が発生した場合の正常な復元を保証することは、Active Directory メンテナンスの非常に重要な部分です。

この記事では、Active Directory ドメイン コ ントローラーが Azure の仮想マシンであるかオンプレミスのサーバーであるかに関係なく、Azure Backup を使用してそのバックアップと復元を行うための適切な手順について説明します。 ここでは、ドメイン コントローラー全体をそのバックアップ時の状態に復元する必要があるシナリオについて説明します。 お客様に適切な復元シナリオを確認するには、[こちらの記事](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)を参照してください。  

>[!NOTE]
> この記事では、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からの項目の復元については説明しません。 Azure Active Directory ユーザーの復元については、[こちらの記事](../active-directory/fundamentals/active-directory-users-restore.md)を参照してください。

## <a name="best-practices"></a>ベスト プラクティス

- 少なくとも 1 つのドメイン コントローラーが確実にバックアップされるようにします。 複数のドメイン コントローラーをバックアップする場合は、[FSMO (フレキシブル シングル マスター操作) ロール](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement)を保持しているものすべてが確実にバックアップされるようにします。

- Active Directory を頻繁にバックアップします。 廃棄標識の有効期間 (既定では60日) よりも古いオブジェクトは "廃棄済み" になり、有効と見なされなくなるため、バックアップは廃棄標識の有効期間を超えないようにしてください。

- ドメイン コントローラーを復元する方法に関する手順を含む、明確なディザスター リカバリー計画を用意します。 Active Directory フォレストの復元を準備する場合は、「[Active Directory フォレストの復旧ガイド](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)」を参照してください。

- ドメイン コントローラーを復元する必要があり、ドメイン内に機能するドメイン コントローラーが残っている場合は、バックアップから復元する代わりに新しいサーバーを作成できます。 **Active Directory Domain Services** サーバー ロールを新しいサーバーに追加して、それを既存のドメインのドメイン コントローラーにします。 その後、Active Directory データが新しいサーバーにレプリケートされます。 以前のドメイン コントローラーを Active Directory から削除するには、[こちらの記事](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup)の手順に従って、メタデータのクリーンアップを実行します。

>[!NOTE]
>Azure Backup には、Active Directory の項目レベルの復元は含まれません。 削除されたオブジェクトを復元して、ドメイン コントローラーにアクセスできるようにするには、[Active Directory ごみ箱](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt)を使用します。 その方法が使用できない場合は、ドメイン コントローラーのバックアップを使用して、削除されたオブジェクトを **ntdsutil.exe** ツールで復元できます (説明は [こちら](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)にあります)。
>
>SYSVOL の権限のある復元を実行する方法については、[こちらの記事](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)を参照してください。

## <a name="backing-up-azure-vm-domain-controllers"></a>Azure VM ドメイン コントローラーのバックアップ

ドメイン コントローラーが Azure VM の場合は、[Azure VM バックアップ](backup-azure-vms-introduction.md)を使用してサーバーをバックアップできます。

Azure VM ドメイン コントローラーのバックアップ (および将来の復元) が正常に行われるようにするには、「[仮想化ドメイン コントローラーの運用上の考慮事項](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers)」を参照してください。

## <a name="backing-up-on-premises-domain-controllers"></a>オンプレミスのドメイン コントローラーのバックアップ

オンプレミスのドメイン コントローラーをバックアップするには、サーバーのシステム状態データをバックアップする必要があります。

- MARS を使用している場合は、[これらの手順](backup-azure-system-state.md)に従います。
- MABS (Azure Backup Server) を使用している場合は、[これらの手順](backup-mabs-system-state-and-bmr.md)に従います。

>[!NOTE]
> (システム状態または VM から) オンプレミスのドメイン コントローラーを Azure クラウドに復元することはサポートされていません。 オンプレミスの Active Directory 環境から Azure へのフェールオーバーのオプションが必要な場合は、[Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) の使用を検討してください。

## <a name="restoring-active-directory"></a>Active Directory の復元

Active Directory のデータは、**権限あり** と **権限なし** の 2 つのモードのいずれかで復元できます。 権限のある復元では、復元された Active Directory データにより、フォレスト内の他のドメイン コントローラー上で見つかったデータがオーバーライドされます。

ただし、このシナリオでは、既存のドメインにドメイン コントローラーを再構築するため、**権限のない** 復元を実行する必要があります。

復元中、サーバーはディレクトリ サービス復元モード (DSRM) で起動されます。 ディレクトリ サービス復元モードの管理者パスワードを入力する必要があります。

>[!NOTE]
>DSRM パスワードを忘れた場合は、[これらの手順](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))を使用してリセットできます。

### <a name="restoring-azure-vm-domain-controllers"></a>Azure VM ドメイン コントローラーの復元

Azure VM ドメイン コントローラーを復元する場合は、「[ドメイン コントローラー VM を復元する](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)」を参照してください。

単一ドメイン内の 1 つのドメイン コントローラー VM または複数のドメイン コントローラー VM を復元する場合は、他の VM と同じように復元します。 ディレクトリ サービス復元モード (DSRM) も利用できるので、Active Directory の復元シナリオはすべて実行可能です。

1 つのドメイン コントローラー VM をマルチ ドメイン構成で復元する必要がある場合は、[PowerShell を使用して](backup-azure-vms-automation.md#restore-the-disks)ディスクを復元し、VM を作成します。

ドメイン内の最後に残っているドメイン コントローラーを復元する場合や、1 つのフォレスト内の複数のドメインを復元する場合は、[フォレストの復旧](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)をお勧めします。

>[!NOTE]
> Windows 2012 以降の仮想化ドメイン コントローラーでは、[仮想化ベースのセーフガード](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)が使用されます。 これらのセーフガードを使用すると、復元された VM がドメイン コントローラーであるかどうかが Active Directory によって認識され、Active Directory データを復元するために必要な手順が実行されます。

### <a name="restoring-on-premises-domain-controllers"></a>オンプレミスのドメイン コントローラーの復元

オンプレミスのドメイン コントローラーを復元するには、「[ドメイン コントローラーでのシステム状態の回復に関する特別な考慮事項](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)」のガイダンスを使用して、Windows Server へのシステム状態の復元の指示に従ってください。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のサポート マトリックス](backup-support-matrix.md)