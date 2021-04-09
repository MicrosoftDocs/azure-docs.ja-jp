---
title: 概要 - Azure ファイル共有に対するオンプレミス AD DS 認証
description: Azure ファイル共有に対する Active Directory Domain Services (AD DS) 認証について説明します。 この記事では、サポートされるシナリオ、可用性について取り上げると共に、AD DS および Azure Active Directory 間でのアクセス許可のしくみについて説明します。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 26932d05cd3d2ef7704b48463c895e05524d87f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472145"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>概要 - SMB を使用した Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証

[Azure Files](storage-files-introduction.md) では、オンプレミス Active Directory Domain Services (AD DS) と Azure Active Directory Domain Services (Azure AD DS) という 2 種類の Domain Services を使用した、サーバー メッセージ ブロック (SMB) 経由の ID ベースの認証がサポートされます。 認証のための適切なドメイン サービスを選択するために、[「しくみ」のセクション](./storage-files-active-directory-overview.md#how-it-works)を確認することを強くお勧めします。 設定は、選択するドメイン サービスによって異なります。 これらの一連の記事では、Azure ファイル共有での認証のためのオンプレミスの AD DS の有効化と構成に重点を置いています。

Azure ファイル共有を初めて使用する場合は、次の一連の記事を読む前に、[計画ガイド](storage-files-planning.md)に目を通すことをお勧めします。

## <a name="supported-scenarios-and-restrictions"></a>サポートされるシナリオと制限

- Azure Files のオンプレミス AD DS 認証に使用される AD DS ID は、Azure AD に同期されている必要があります。 パスワード ハッシュの同期はオプションです。 
- Azure File Sync によって管理されている Azure ファイル共有がサポートされます。
- AD と RC4-HMAC および [AES 256 暗号化](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)を使用した Kerberos 認証がサポートされています。 現在、AES 256 暗号化のサポートは、名前が 15 文字以下のストレージ アカウントに制限されています。 AES 128 Kerberos 暗号化はまだサポートされていません。
- シングル サインオン エクスペリエンスがサポートされます。
- Windows 7 または Windows Server 2008 R2 より新しい OS バージョンで実行されるクライアント上でのみ、サポートされます。
- ストレージ アカウントの登録先の AD フォレストに対してサポートされのみます。 既定では、単一のフォレストからのみ、AD DS 資格情報を使用して Azure ファイル共有にアクセスできます。 別のフォレストから Azure ファイル共有にアクセスする必要がある場合は、適切なフォレストの信頼が構成されていることを確認してください。詳細については、[よくあるご質問](storage-files-faq.md#ad-ds--azure-ad-ds-authentication)に関するページを参照してください。
- AD DS で作成されたコンピューター アカウントに対する認証はサポートされていません。
- Network File System (NFS) ファイル共有に対する認証はサポートされていません。

SMB 経由の Azure ファイル共有に対して AD DS を有効にすると、AD DS に参加しているマシンでは、既存の AD DS 資格情報を使用して Azure ファイル共有をマウントできます。 この機能は、オンプレミス マシンでホストされているか、Azure でホストされている AD DS 環境で有効にすることができます。

## <a name="videos"></a>ビデオ

いくつかの一般的なユース ケースで Azure Files AD 認証を設定できるように、次のシナリオに対して、ステップ バイ ステップ ガイダンスを含む 2 本の動画を公開しました。

| オンプレミスのファイル サーバーの Azure Files への置き換え (ファイルと AD 認証のためのプライベート リンクに関する設定を含む) | Windows Virtual Desktop のプロファイル コンテナーとしての Azure Files の使用 (AD 認証と FsLogix 構成に関する設定を含む)  |
|-|-|
| [![オンプレミスのファイル サーバーを置き換えるビデオのスクリーンショット - クリックして再生します。](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![Azure Files をプロファイル コンテナーとして使用するビデオのスクリーンショット - クリックして再生します。](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>前提条件 

Azure ファイル共有に対する AD DS 認証を有効にする前に、次の前提条件を完了していることを確認します。 

- [AD DS 環境](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)を選択または作成して、Azure AD Connect を使って [Azure AD に同期します](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    新規または既存のオンプレミスの AD DS 環境で機能を有効にすることができます。 アクセスに使用される ID は、Azure AD と同期されている必要があります。 アクセスする Azure AD テナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。

- オンプレミス マシンまたは Azure VM をオンプレミスの AD DS にドメイン参加させます。 ドメインに参加させる方法については、「[コンピューターをドメインに参加させる](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)」を参照してください。

    マシンが AD DS にドメイン参加していない場合でも、マシンで AD ドメイン コントローラーが認識されていれば、認証に AD 資格情報を利用することはできます。

- Azure ストレージ アカウントを選択または作成します。  最適なパフォーマンスが得られるように、共有にアクセスする予定のクライアントと同じリージョンに、ストレージ アカウントをデプロイすることをお勧めします。 次に、ストレージ アカウント キーを使用して、[Azure ファイル共有をマウントします](storage-how-to-use-files-windows.md)。 ストレージ アカウント キーを使ってマウントすると、接続が検証されます。

    ファイル共有を含むストレージ アカウントが、Azure AD DS 認証用にまだ構成されていないことを確認します。 ストレージ アカウントで Azure Files Azure AD DS 認証が有効になっている場合は、オンプレミスの AD DS を使用するように変更する前に無効にする必要があります。 これは、Azure AD DS 環境で構成された既存の ACL を、適切なアクセス許可を適用するために再構成する必要があることを意味します。


    Azure Files への接続に問題が発生した場合は、[Windows での Azure Files マウント エラーに対して発行したトラブルシューティング ツール](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)に関する記事を参照してください。 また、ポート 445 がブロックされている場合のシナリオを回避するための[ガイダンス](./storage-files-faq.md#on-premises-access)も提供されています。 


- Azure ファイル共有への AD DS 認証を有効にして構成するには、事前に、関連するネットワーク構成を行います。 詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

AD DS を使用した Azure Files 認証は、[すべての Azure パブリック、China および Gov リージョン](https://azure.microsoft.com/global-infrastructure/locations/)で利用できます。

## <a name="overview"></a>概要

ファイル共有上でネットワーク構成を有効にする予定の場合は、AD DS 認証を有効にする前に、[ネットワークの考慮事項](./storage-files-networking-overview.md)に関する記事に目を通して、関連する構成を完了することをお勧めします。

Azure ファイル共有の AD DS 認証を有効にすると、オンプレミスの AD DS の資格情報を使用して Azure ファイル共有に対する認証を行うことができます。 さらに、アクセス許可より適切に管理して、きめ細かいアクセス制御を行うことができます。 これを行うには、AD Connect を使用して、オンプレミスの AD DS から Azure AD に ID を同期する必要があります。 オンプレミスの AD DS の資格情報でファイル/共有レベルのアクセスを管理しながら、Azure AD に同期される ID を使用して共有レベルのアクセスを制御します。

次に、以下の手順に従って、AD DS 認証用に Azure Files を設定します。 

1. [パート 1 : ストレージ アカウントでの AD DS 認証を有効にする](storage-files-identity-ad-ds-enable.md)

1. [パート 2 : ターゲットの AD ID と同期している、Azure AD ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てる](storage-files-identity-ad-ds-assign-permissions.md)

1. [パート 3: SMB を使用してディレクトリとファイルに Windows ACL を構成する](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [パート 4: AD DS に参加している VM に Azure ファイル共有をマウントする](storage-files-identity-ad-ds-mount-file-share.md)

1. [AD DS のストレージ アカウント ID のパスワードを更新します](storage-files-identity-ad-ds-update-password.md)

次の図は、Azure ファイル共有に対する SMB 経由の Azure AD 認証を有効にするためのエンドツーエンド ワークフローを示しています。 

![Files AD ワークフロー図](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Azure ファイル共有へのアクセスに使用される ID は、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) モデルを使用して共有レベルのファイル アクセス許可を適用するために、Azure AD に同期する必要があります。 既存のファイル サーバーから引き継がれたファイルまたはディレクトリの [Windows スタイルの DACL](/previous-versions/technet-magazine/cc161041(v=msdn.10)) は保持され、適用されます。 これにより、エンタープライズ AD DS 環境とのシームレスな統合が提供されます。 オンプレミス ファイル サーバーを Azure ファイル共有に置き換えると、既存のユーザーは、使用されている資格情報を変更することなく、シングル サインオン エクスペリエンスで現在のクライアントから Azure ファイル共有にアクセスできるようになります。  

## <a name="next-steps"></a>次のステップ

Azure ファイル共有に対してオンプレミス AD DS 認証を有効にするには、次の記事に進んでください。

[パート1: アカウントに対する AD DS 認証を有効にする](storage-files-identity-ad-ds-enable.md)
