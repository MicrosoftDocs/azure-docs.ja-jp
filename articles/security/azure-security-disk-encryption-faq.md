---
title: FAQ - IaaS VM のための Azure Disk Encryption | Microsoft Docs
description: この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption についてよく寄せられる質問への回答を示します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: c28cf4326593897dcbc90902737fc4846356078d
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653382"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>IaaS VM のための Azure Disk Encryption に関してよくあるご質問

この記事では、Windows および Linux IaaS VM の Azure Disk Encryption に関してよく寄せられる質問 (FAQ) への回答を示します。 このサービスの詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](azure-security-disk-encryption-overview.md)」をご覧ください。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure Disk Encryption はどこで一般公開 (GA) されていますか。

Windows および Linux IaaS VM の Azure Disk Encryption は、すべての Azure パブリック リージョンで一般公開されています。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption では、どのようなユーザー エクスペリエンスを利用できますか。

Azure Disk Encryption GA は、Azure Resource Manager テンプレート、Azure PowerShell、および Azure CLI をサポートしています。 異なるユーザー エクスペリエンスによって柔軟性が得られます。 IaaS VM のディスク暗号化を有効にするオプションは 3 つあります。 Azure Disk Encryption で利用可能なユーザー エクスペリエンスとステップ バイ ステップ ガイダンスの詳細については、[Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)方法に関するページと [Linux で Azure Disk Encryption を有効にする](azure-security-disk-encryption-linux.md)方法に関するページを参照してください。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption の価格はどれくらいですか。

Azure Disk Encryption を使用して VM ディスクを暗号化するための料金は発生しませんが、Azure Key Vault の使用に関連する料金が発生します。 Azure Key Vault のコストの詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」ページを参照してください。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption の使用を開始するにはどうすればよいですか。

最初に、[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)をお読みください。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure Disk Encryption がサポートされる VM サイズとオペレーティングシステムを教えてください。

[Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)の記事には、Azure Disk Encryption をサポートする [VM のサイズ](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes)と [VM オペレーティング システム](azure-security-disk-encryption-prerequisites.md#supported-operating-systems)の一覧が記載されています。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption でブート ボリュームとデータ ボリュームの両方を暗号化できますか。

はい。Windows および Linux IaaS VM のブート ボリュームとデータ ボリュームを暗号化できます。 Windows VM の場合は、データを暗号化する前に、OS ボリュームを暗号化する必要があります。 Linux VM の場合は、先に OS ボリュームを暗号化しなくても、データを暗号化することができます。 Linux の OS ボリュームを暗号化した後で Linux IaaS VM の OS ボリュームの暗号化を無効にすることはできません。 スケール セットの Linux VM の場合、データ ボリュームのみ暗号化できます。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Azure Disk Encryption を使用して、マウント解除されたボリュームを暗号化することはできますか。

いいえ、Azure Disk Encryption で暗号化されるのは、マウントされたボリュームのみになります。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>シークレットまたは暗号化キーを切り替えるにはどうすればいいですか。

シークレットを切り替えるには、別のキー コンテナーを指定して、最初に使用したのと同じコマンドを呼び出してディスクの暗号化を有効にするだけです。 キーの暗号化キーを切り替えるには、新しいキーの暗号化を指定して、最初に使用したのと同じコマンドを呼び出してディスク暗号化を有効にします。 

>[!WARNING]
> - これまで、[Azure AD アプリで Azure AD の資格情報を指定することで Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) を使用してこの VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 この暗号化された VM に対して [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) を使用することはできません。それはサポートされていないシナリオであり、暗号化された VM 用の AAD アプリケーションからの切り替えはまだサポートされていないことを意味します。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>最初にキー暗号化キーを使用していなかった場合は、どのようにしてキー暗号化キーを追加または削除すればいいですか。

キー暗号化キーを追加するには、キー暗号化キーのパラメーターを渡して、有効化コマンドをもう一度呼び出します。 キー暗号化キーを削除するには、キー暗号化キーのパラメーターを指定せずに、有効化コマンドをもう一度呼び出します。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption では、Bring Your Own Key (BYOK) を実施できますか。

はい。独自のキー暗号化キーを指定できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure によって作成されたキー暗号化キーを使用できますか。

はい。Azure Key Vault を使用して、Azure Disk Encryption で使用するキー暗号化キーを生成できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護できますか。

Azure Disk Encryption では、オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護することはできません。 暗号化キーを保護するために使用できるのは、Azure Key Vault サービスのみです。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption を構成するための前提条件は何ですか。

Azure Disk Encryption の前提条件があります。 新しいキー コンテナーを作成するか、既存のキー コンテナーを暗号化できるようにディスク暗号化アクセス用に設定して、シークレットとキーを保護するには、「[Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)」を参照してください。 キー暗号化キーのサポート シナリオの詳細については、[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)に関するページを参照してください。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Azure AD アプリ (以前のリリース) で Azure Disk Encryption を構成するための前提条件は何ですか。

Azure Disk Encryption の前提条件があります。 Azure Active Directory アプリケーションを作成するか、新しいキー コンテナーを作成するか、既存のキー コンテナーをディスク暗号化アクセス用に設定してシークレットとキーの暗号化と保護を実行できるようにするには、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites-aad.md)」を参照してください。 キー暗号化キーのサポート シナリオの詳細については、[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)に関するページを参照してください。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure AD アプリ (以前のリリース) を使用した Azure Disk Encryption は、まだサポートされていますか。
はい。 Azure AD アプリを使用したディスク暗号化は、まだサポートされています。 ただし、新しい VM を暗号化する場合は、Azure AD アプリを使用して暗号化するのではなく、新しい方法を使用することをお勧めします。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Azure AD アプリで暗号化された VM を、Azure AD アプリがない暗号化に移行することはできますか。
  現時点では、Azure AD アプリで暗号化されたマシンを、Azure AD アプリがない暗号化に直接移行するパスはありません。 また、Azure AD アプリがない暗号化から AD アプリによる暗号化への直接的なパスはありません。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption は、どのバージョンの Azure PowerShell をサポートしていますか。

Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用してください。 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードしてください。 Azure Disk Encryption は、Azure SDK Version 1.1.0 では*サポートされていません*。

> [!NOTE]
> Linux 用 Azure Disk Encryption プレビューの拡張機能 "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" は非推奨となっています。 この拡張機能は、Azure Disk Encryption プレビュー リリース向けに公開されたものです。 テスト環境や運用環境のデプロイでプレビュー バージョンの拡張機能を使用することは避けてください。

> Azure Resource Manager (ARM) などのデプロイ シナリオで、Linux IaaS VM の暗号化を有効にするために Linux VM 用の Azure Disk Encryption 拡張機能をデプロイする必要がある場合、Azure Disk Encryption の運用環境に対応した拡張機能 "Microsoft.Azure.Security.AzureDiskEncryptionForLinux" を使用する必要があります。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>カスタム Linux イメージに対して Azure Disk Encryption を適用できますか。

カスタム Linux イメージに対して Azure Disk Encryption を適用することはできません。 前述したサポート対象のディストリビューションのギャラリー Linux イメージのみがサポートされます。 カスタム Linux イメージは現時点ではサポートされていません。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>yum update を使用する Linux Red Hat VM に更新プログラムを適用できますか。

はい。Red Hat Linux VM に対して yum update を実行できます。  詳細については、「[ファイアウォール内の Linux パッケージの管理](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall)」を参照してください。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Linux で推奨される Azure Disk Encryption ワークフローは何ですか。

Linux で最適な結果を得るには、次のワークフローが推奨されます。
* 必要な OS ディストリビューションとバージョンに対応する、変更されていないストック ギャラリー イメージを使用して開始します
* 暗号化するマウント済みのドライブをバックアップします。  このバックアップによって、暗号化が完了する前に VM が再起動された場合など、障害発生時の復旧が可能になります。
* 暗号化 (VM の特性や、接続されているデータ ディスクのサイズによっては、数時間から数日かかることがあります)
* 必要に応じて、イメージをカスタマイズし、イメージにソフトウェアを追加します。

このワークフローが可能でない場合は、DM-Crypt を使用したディスク全体の暗号化に代わる方法として、プラットフォームのストレージ アカウント レイヤーで [Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) を使用することができます。

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>ディスク "Bek ボリューム" または "/mnt/azure_bek_disk" とは何ですか。

Windows の "Bek ボリューム" または Linux の "/mnt/azure_bek_disk" は、暗号化された Azure IaaS VM の暗号化キーを安全に格納するローカルのデータ ボリュームです。
> [!NOTE]
> このディスクのコンテンツの削除や編集はしないでください。 IaaS VM 上のすべての暗号化操作に暗号化キーが必要なため、ディスクのマウントを解除しないでください。


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure Disk Encryption はどのような暗号化方法を使用しますか。

Windows では、ADE は BitLocker AES256 暗号化方法を使用します (Windows Server 2012 より前のバージョンでは AES256WithDiffuser)。 Linux では、ADE は暗号化解除の既定値である 256 ビット ボリューム マスター キーの aes-xts-plain64 を使用します。

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll を使用して、すべてのボリュームの種類を指定した場合、既に暗号化したデータ ドライブ上のデータは消去されますか。
いいえ、Azure Disk Encryption を使用して既に暗号化されているデータ ドライブのデータは消去されません。 EncryptFormatAll で、OS ドライブが再暗号化されなかったのと同様に、既に暗号化されているデータ ドライブは再暗号化されません。 詳細については、「[EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)」を参照してください。        

## <a name="is-xfs-filesystem-supported"></a>XFS ファイルシステムはサポートされていますか。
XFS ボリュームは、EncryptFormatAll を使用したデータ ディスクの暗号化でのみサポートされています。 ボリュームが再フォーマットされ、そのボリューム上の以前のデータがすべて消去されます。 詳細については、「[EncryptFormatAll 条件](azure-security-disk-encryption-linux.md#bkmk_EFACriteria)」を参照してください。

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>暗号化された VM をバックアップおよび復元することはできますか。 

Azure Backup には、同じサブスクリプションおよびリージョン内の暗号化された VM をバックアップおよび復元するメカニズムが用意されています。  手順については、「[暗号化された仮想マシンを Azure Backup でバックアップおよび復元する](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)」をご覧ください。  暗号化された VM を別のリージョンに復元することは、現在サポートされていません。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>質問したり、フィードバックを提供したりするにはどこに移動すればよいですか。

[Azure Disk Encryption フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)で質問したり、フィードバックを提供したりできます。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Disk Encryption に関して最もよく寄せられるご質問について説明しました。 このサービスの詳細については、以下の記事を参照してください。

- [Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)
- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [保存時の Azure データの暗号化](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
