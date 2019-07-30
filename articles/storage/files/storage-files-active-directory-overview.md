---
title: SMB を使用した Azure Files の Azure Active Directory 認証の概要 (プレビュー) - Azure Storage
description: Azure Files は、Azure Active Directory (Azure AD) Domain Services を使用した、SMB (Server Message Block) 上の ID ベースの認証をサポートします。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269370"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Azure Files での SMB アクセスに対する Azure Active Directory Domain Services (AAD DS) 認証の概要 (プレビュー)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure Files に対して AAD DS 認証を有効にする方法については、「[SMB を使用して Azure Files への Azure Active Directory Domain Services 認証を有効にする (プレビュー)](storage-files-active-directory-enable.md)」をご覧ください。

## <a name="glossary"></a>用語集 
SMB を使用した Azure Files の Azure AD Domain Service 認証に関連するいくつかの重要な用語を理解することをお勧めします。

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 Azure AD には、主要なディレクトリ サービス、アプリケーション アクセスの管理、ID 保護の機能が一つのソリューションとして統合されています。 詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

-   **Azure AD Domain Services**  
    Azure AD Domain Services では、マネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証) を提供します。 これらのサービスは、Windows Server Active Directory と完全に互換性があります。 詳細については、「 [Azure Active Directory (AD) のドメイン サービス](../../active-directory-domain-services/overview.md)」を参照してください。

-   **Azure のロールベースのアクセス制御 (RBAC)**  
    Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用して業務遂行に必要な最小限の権限をユーザーに付与することで、リソースへのアクセスを管理できます。 RBAC の詳細については、「[Azure のロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

-   **Kerberos 認証**

    Kerberos は、ユーザーまたはホストの身元を確認するために使用される認証プロトコルです。 Kerberos の詳細については、「[Kerberos 認証の概要](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)」を参照してください。

-  **サーバー メッセージ ブロック (SMB) プロトコル**  
    SMB は、業界標準のネットワーク ファイル共有プロトコルです。 SMB は、Common Internet File System (CIFS) とも呼ばれます。 SMB の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD Domain Services 認証の利点
Azure Files に対する Azure AD Domain Services 認証には、共有キー認証と比較して、以下のようなさまざまな利点があります。

-   **従来の ID ベースのファイル共有アクセス エクスペリエンスを Azure AD と Azure AD Domain Services を使用してクラウド上に展開する**  
    従来のファイル サーバーを Azure Files に置き換えて、お使いのアプリケーションを "リフト アンド シフト" でクラウドに移行する場合は、アプリケーションによるファイル データへのアクセスを Azure AD 資格情報で認証することができます。 Azure Files では、AAD DS ドメイン参加済み Windows VM からの SMB を介した Azure Files へのアクセスでの Azure AD 資格情報の使用がサポートされています。 また、ユーザー名、パスワード、およびその他のグループ割り当てを保持するために、オンプレミスのすべての Active Directory オブジェクトを Azure AD と同期することも選択できます。

-   **Azure のファイル共有への詳細なアクセス制御の適用が可能**  
    特定の ID に対して、ファイル共有、ディレクトリ、またはファイル レベルでアクセス許可を付与できます。 たとえば、プロジェクトのコラボレーションのために、複数のチームが 1 つの Azure ファイル共有を使用しているとします。 その場合、機密ではないディレクトリについてはすべてのチームにアクセス権限を付与し、機密の財務データが含まれるディレクトリについては財務チームのみにアクセス権限を付与できます。 

-   **データだけでなく、ACL のバックアップも可能**  
    Azure Files では、既存のオンプレミスのファイル共有をバックアップすることができます。 Azure Files では、SMB 経由で Azure Files に共有ファイルをバックアップすると、データと共に ACL も保持されます。

## <a name="how-it-works"></a>動作のしくみ
Azure Files は、ドメインに参加している VM から得た Azure AD の資格情報で Kerberos 認証をすることをサポートするために、Azure AD Domain Services を使用しています。 Azure Files で Azure AD を使用するには、まず Azure Active Directory Domain Services を有効にして、ファイル データにアクセスしようとする元の VM からドメインに参加する必要があります。 ドメインに参加している VM は、Azure AD Domain Services と同じ仮想ネットワーク (VNET) 内に存在する必要があります。 

VM 上で実行されているアプリケーションに関連付けられている ID が Azure Files のデータにアクセスしようとした場合、その要求は Azure AD Domain Services に送信され、ID が認証されます。 認証が成功した場合、Azure AD Domain Services は Kerberos トークンを返します。 アプリケーションは Kerberos トークンが含まれる要求を送信し、Azure Files はそのトークンを使用して要求を承認します。 Azure Files はトークンのみを受け取ります。Azure AD の資格情報は保持しません。

![SMB 経由の Azure AD 認証のスクリーン ショット](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>SMB アクセスに対する Azure AD Domain Services 認証を有効にする
2018 年 9 月 24 日より後に作成された新しいまたは既存のストレージ アカウントに対して、Azure Files 用の Azure AD Domain Services 認証を有効にできます。 

この機能を有効にする前に、ストレージ アカウントが関連付けられているプライマリ Azure AD テナントに、Azure AD Domain Services がデプロイされていることを確認します。 Azure AD Domain Services の設定がまだ完了していない場合は、「[Azure portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/create-instance.md)」に記載の手順に従ってください。

Azure AD Domain Services のデプロイは、通常 10～15 分要します。 Azure AD Domain Services のデプロイが完了後、SMB 経由で Azure Files の Azure AD 認証を有効にできます。 詳細については、「[SMB を使用して Azure Files への Azure Active Directory Domain Services 認証を有効にする (プレビュー)](storage-files-active-directory-enable.md)」を参照してください。 

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files の共有レベルのアクセス権限を構成する
Azure AD Domain Services 認証を有効にした後、Azure AD の ID のカスタム RBAC ロールを構成し、ストレージ アカウント内の任意のファイル共有へのアクセス権を割り当てることができます。

ドメインに参加している VM 上で実行されているアプリケーションが Azure のファイル共有をマウントしようとしたり、ディレクトリまたはファイルにアクセスしようとしたりした場合、そのアプリケーションの Azure AD 資格情報について、適切な共有レベルの権限と NTFS 権限があることが確認されます。 共有レベルのアクセス許可の構成の詳細については、「[SMB を使用して Azure Files への Azure Active Directory 認証を有効にする (プレビュー)](storage-files-active-directory-enable.md)」を参照してください。

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Azure Files のディレクトリレベルまたはファイルレベルの権限を構成する 
Azure Files は、ルート ディレクトリを含め、ディレクトリレベルおよびファイルレベルで標準の NTFS ファイル権限を適用します。 ディレクトリレベルまたはファイルレベルの権限の構成は、SMB 経由の構成のみがサポートされています。 VM 上で対象のファイル共有をマウントし、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [SET-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) コマンドを使用して権限を構成します。 

> [!NOTE]
> Windows のファイルエクスプローラー経由の NTFS 権限の構成は、プレビューではサポートされていません。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>スーパーユーザーの権限でストレージ アカウントのキーを使用する 
ストレージ アカウント キーを保持するユーザーは、スーパー ユーザーの権限で Azure Files にアクセスできます。 スーパー ユーザーの権限は、RBAC の共有レベルで構成され、Azure AD によって適用されたすべてのアクセス制御の制限を超えることができます。 Azure のファイル共有をマウントするには、スーパー ユーザーの権限が必要です。 

> [!IMPORTANT]
> セキュリティのベスト プラクティスの一貫として、ストレージ アカウント キーの共有や、Azure AD の権限の利用は、可能な限り避けてください。

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Azure のファイル共有にディレクトリとファイルのデータ インポート用の ACL を保持する
Azure Files では、Azure ファイル共有にデータをコピーするときに、ディレクトリまたはファイルの ACL の保持をサポートするようになりました。 ディレクトリまたはファイルの ACL を Azure Files にコピーできます。 たとえば、[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) を使用し、`/copy:s` のフラグを設定することで、データと ACL の両方を Azure のファイル共有にコピーすることができます。 ACL の保持は既定でオンになっており、ストレージ アカウントに関する Azure AD Domain Services 認証機能を明示的に有効にする必要はありません。 

## <a name="pricing"></a>価格
お使いのストレージアカウントで SMB 経由の Azure AD 認証を有効にする場合、追加のサービス料金は発生しません。 価格の詳細については、「[Azure Files の価格](https://azure.microsoft.com/pricing/details/storage/files/)」と「[Azure AD Domain Services の価格](https://azure.microsoft.com/pricing/details/active-directory-ds/)」のページを参照してください。

## <a name="next-steps"></a>次の手順
Azure Files や、SMB 経由の Azure AD 認証の詳細については、これらのリソースを参照してください。

- [Azure Files の概要](storage-files-introduction.md)
- [SMB を使用して Azure Files への Azure Active Directory 認証を有効にする (プレビュー)](storage-files-active-directory-enable.md)
- [FAQ](storage-files-faq.md)
