---
title: FAQ - Windows VM のための Azure Disk Encryption | Microsoft Docs
description: この記事では、Windows IaaS VM 用の Microsoft Azure Disk Encryption についてよく寄せられる質問への回答を示します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: b6cd7e93451de9c5761db22024ac82abaaa0949e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246012"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Windows VM 用の Azure Disk Encryption に関する FAQ

この記事では、Windows VM 用の Azure Disk Encryption についてよく寄せられる質問 (FAQ) への回答を示します。 このサービスの詳細については、「[Azure Disk Encryption の概要](disk-encryption-overview.md)」をご覧ください。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure Disk Encryption はどこで一般公開 (GA) されていますか。

Azure Disk Encryption は、すべての Azure パブリック リージョンで一般公開されています。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption では、どのようなユーザー エクスペリエンスを利用できますか。

Azure Disk Encryption GA は、Azure Resource Manager テンプレート、Azure PowerShell、および Azure CLI をサポートしています。 異なるユーザー エクスペリエンスによって柔軟性が得られます。 VM のディスク暗号化を有効にするオプションは 3 つあります。 Azure Disk Encryption で利用可能なユーザー エクスペリエンスとステップ バイ ステップ ガイダンスの詳細については、「[Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md)」 (Windows 用の Azure Disk Encryption シナリオ) を参照してください。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption の価格はどれくらいですか。

Azure Disk Encryption を使用して VM ディスクを暗号化するための料金は発生しませんが、Azure Key Vault の使用に関連する料金が発生します。 Azure Key Vault のコストの詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」ページを参照してください。

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption の使用を開始するにはどうすればよいですか。

最初に、[Azure Disk Encryption の概要](disk-encryption-overview.md)をお読みください。

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Azure Disk Encryption がサポートされる VM サイズとオペレーティングシステムを教えてください。

[Azure Disk Encryption の概要](disk-encryption-overview.md)の記事には、Azure Disk Encryption をサポートする [VM のサイズ](disk-encryption-overview.md#supported-vm-sizes)と [VM オペレーティング システム](disk-encryption-overview.md#supported-operating-systems)の一覧が記載されています。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption でブート ボリュームとデータ ボリュームの両方を暗号化できますか。

ブート ボリュームとデータ ボリュームの両方を暗号化できます。ただし、先に OS ボリュームを暗号化しなければ、データは暗号化できません。

OS ボリュームを暗号化した後で OS ボリュームの暗号化を無効にすることはできません。

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Azure Disk Encryption を使用して、マウント解除されたボリュームを暗号化することはできますか。

いいえ、Azure Disk Encryption で暗号化されるのは、マウントされたボリュームのみになります。

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>シークレットまたは暗号化キーを切り替えるにはどうすればいいですか。

シークレットを切り替えるには、別のキー コンテナーを指定して、最初に使用したのと同じコマンドを呼び出してディスクの暗号化を有効にするだけです。 キーの暗号化キーを切り替えるには、新しいキーの暗号化を指定して、最初に使用したのと同じコマンドを呼び出してディスク暗号化を有効にします。 

>[!WARNING]
> - これまで、[Azure AD アプリで Azure AD の資格情報を指定することで Azure Disk Encryption](disk-encryption-windows-aad.md) を使用してこの VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 この暗号化された VM に対して Azure Disk Encryption を使用することはできません。それはサポートされていないシナリオであり、暗号化された VM 用の AAD アプリケーションからの切り替えはまだサポートされていないことを意味します。

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>最初にキー暗号化キーを使用していなかった場合は、どのようにしてキー暗号化キーを追加または削除すればいいですか。

キー暗号化キーを追加するには、キー暗号化キーのパラメーターを渡して、有効化コマンドをもう一度呼び出します。 キー暗号化キーを削除するには、キー暗号化キーのパラメーターを指定せずに、有効化コマンドをもう一度呼び出します。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption では、Bring Your Own Key (BYOK) を実施できますか。

はい。独自のキー暗号化キーを指定できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」を参照してください。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure によって作成されたキー暗号化キーを使用できますか。

はい。Azure Key Vault を使用して、Azure Disk Encryption で使用するキー暗号化キーを生成できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーの詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」を参照してください。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護できますか。

Azure Disk Encryption では、オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護することはできません。 暗号化キーを保護するために使用できるのは、Azure Key Vault サービスのみです。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」を参照してください。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption を構成するための前提条件は何ですか。

Azure Disk Encryption の前提条件があります。 新しいキー コンテナーを作成するか、既存のキー コンテナーを暗号化できるようにディスク暗号化アクセス用に設定して、シークレットとキーを保護するには、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」の記事を参照してください。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault.md)」を参照してください。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Azure AD アプリ (以前のリリース) で Azure Disk Encryption を構成するための前提条件は何ですか。

Azure Disk Encryption の前提条件があります。 Azure Active Directory アプリケーションを作成するか、新しいキー コンテナーを作成するか、既存のキー コンテナーをディスク暗号化アクセス用に設定してシークレットとキーの暗号化と保護を実行できるようにするには、「[Azure AD を使用した Azure Disk Encryption](disk-encryption-windows-aad.md)」を参照してください。 キー暗号化キーのサポート シナリオの詳細については、「[Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成](disk-encryption-key-vault-aad.md)」を参照してください。

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Azure AD アプリ (以前のリリース) を使用した Azure Disk Encryption は、まだサポートされていますか。
はい。 Azure AD アプリを使用したディスク暗号化は、まだサポートされています。 ただし、新しい VM を暗号化する場合は、Azure AD アプリを使用して暗号化するのではなく、新しい方法を使用することをお勧めします。 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Azure AD アプリで暗号化された VM を、Azure AD アプリがない暗号化に移行することはできますか。
  現時点では、Azure AD アプリで暗号化されたマシンを、Azure AD アプリがない暗号化に直接移行するパスはありません。 また、Azure AD アプリがない暗号化から AD アプリによる暗号化への直接的なパスはありません。 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption は、どのバージョンの Azure PowerShell をサポートしていますか。

Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用してください。 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードしてください。 Azure Disk Encryption は、Azure SDK Version 1.1.0 では*サポートされていません*。

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>ディスク "Bek ボリューム" または "/mnt/azure_bek_disk" とは何ですか。

"Bek ボリューム" は、暗号化された Azure VM の暗号化キーを安全に格納するローカルのデータ ボリュームです。

> [!NOTE]
> このディスクのコンテンツの削除や編集はしないでください。 IaaS VM 上のすべての暗号化操作に暗号化キーが必要なため、ディスクのマウントを解除しないでください。

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Azure Disk Encryption はどのような暗号化方法を使用しますか。

Azure Disk Encryption では、BitLocker AES256 暗号化方法が使用されます (Windows Server 2012 より前のバージョンでは AES256WithDiffuser)。 

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>EncryptFormatAll を使用して、すべてのボリュームの種類を指定した場合、既に暗号化したデータ ドライブ上のデータは消去されますか。
いいえ、Azure Disk Encryption を使用して既に暗号化されているデータ ドライブのデータは消去されません。 EncryptFormatAll で、OS ドライブが再暗号化されなかったのと同様に、既に暗号化されているデータ ドライブは再暗号化されません。 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>暗号化された VM をバックアップおよび復元することはできますか。 

Azure Backup には、同じサブスクリプションおよびリージョン内の暗号化された VM をバックアップおよび復元するメカニズムが用意されています。  手順については、「[暗号化された仮想マシンを Azure Backup でバックアップおよび復元する](../../backup/backup-azure-vms-encryption.md)」をご覧ください。  暗号化された VM を別のリージョンに復元することは、現在サポートされていません。  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>質問したり、フィードバックを提供したりするにはどこに移動すればよいですか。

[Azure Disk Encryption フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)で質問したり、フィードバックを提供したりできます。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Disk Encryption に関して最もよく寄せられるご質問について説明しました。 このサービスの詳細については、以下の記事を参照してください。

- [Azure Disk Encryption の概要](disk-encryption-overview.md)
- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [保存時の Azure データの暗号化](../../security/fundamentals/encryption-atrest.md)
