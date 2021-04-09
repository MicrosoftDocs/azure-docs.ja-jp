---
title: Azure AD Domain Services のサインイン問題を解決する | Microsoft Docs
description: Azure Active Directory Domain Services でユーザーがサインインしたときに発生する一般的な問題とエラーをトラブルシューティングする方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618571"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインでのアカウントのサインインに関する問題をトラブルシューティングする

あるユーザー アカウントで Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインにサインインできない場合、一般的に次のような理由が考えられます。

* [アカウントが Azure AD DS にまだ同期されていません。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [アカウントのサインインを許可するパスワード ハッシュが Azure AD DS に指定されていません。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [アカウントがロックアウトされています。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS では、Azure AD テナントに対して外部となるアカウントについては、資格情報を同期できません。 外部ユーザーは Azure AD DS マネージド ドメインにサインインできません。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>アカウントが Azure AD DS にまだ同期されていません

ディレクトリのサイズによっては、ユーザー アカウントと資格情報ハッシュがマネージド ドメインで使用できるようになるまでに時間がかかる場合があります。 大きなディレクトリの場合、Azure AD からのこの初回の一方向同期に数時間かかり、最大で 1 日か 2 日かかることもあります。 十分に時間を空けてから認証を再試行してください。

Azure AD にオンプレミスのディレクトリ データを同期する目的で Azure AD Connect を使用するハイブリッド環境の場合、必ず最新版の Azure AD Connect を実行し、[Azure AD DS の有効化後に完全な同期を実行するように Azure AD Connect を構成][azure-ad-connect-phs]しておきます。 Azure AD DS を無効にし、再び有効にした場合、以上の手順をもう一度実行する必要があります。

アカウントが Azure AD Connect 経由で同期しない問題が解決されない場合、Azure AD Sync Service を再起動してください。 Azure AD Connect がインストールされているコンピューターから、コマンド プロンプト ウィンドウを開き、次のコマンドを実行します。

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS にパスワード ハッシュが指定されていません

NTLM 認証と Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で生成または保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>オンプレミス同期のあるハイブリッド環境

Azure AD Connect を使用してオンプレミス AD DS 環境から同期するハイブリッド環境の場合、必要な NTLM または Kerberos パスワード ハッシュをローカルで生成し、Azure AD に同期できます。 マネージド ドリームキャストを作成したら、[Azure Active Directory Domain Services とのパスワード ハッシュの同期を有効にします][azure-ad-connect-phs]。 このパスワード ハッシュ同期手順を完了しないと、マネージド ドメインを使用してアカウントにサインインできません。 Azure AD DS を無効にし、再び有効にした場合、この手順をもう一度実行する必要があります。

詳細については、[Azure AD DS のパスワード ハッシュ同期のしくみ][phs-process]に関するページを参照してください。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>オンプレミス同期のあるクラウド限定環境

オンプレミス同期がなく、Azure AD のアカウントのみのマネージド ドメインでも、必要な NTLM または Kerberos パスワード ハッシュを生成する必要があります。 クラウド専用アカウントでサインインできない場合、Azure AD DS の有効化後、アカウントのパスワード変更プロセスが正常に完了していますか。

* **いいえ。パスワードは変更されていません。**
    * [アカウントのパスワードを変更して][enable-user-accounts]必要なパスワード ハッシュを生成してから 15 分待ち、それから再びサインインをお試しください。
    * Azure AD DS を無効にし、再び有効にした場合、各アカウントで手順をもう一度実行してパスワードを変更し、必要なパスワード ハッシュを生成する必要があります。
* **はい。パスワードは変更されています。**
    * `AADDSCONTOSO\deeriley` のような *SAMAccountName* 形式ではなく、`driley@aaddscontoso.com` のように *UPN* 形式を利用してサインインをお試しください。
    * UPN プレフィックスが極端に長いか、マネージド ドメイン上の別のユーザーと同じであるユーザーに対して *SAMAccountName* を自動的に生成できます。 *UPN* 形式の場合、Azure AD テナント内で確実に一意になります。

## <a name="the-account-is-locked-out"></a>アカウントがロックアウトされています

サインインの試行失敗に対して定義されているしきい値に到達すると、マネージド ドメインのユーザー アカウントはロックアウトされます。 サインインが総当たり式に繰り返し試行される場合、自動化されたデジタル攻撃の可能性があります。このアカウント ロックアウト動作はユーザーをそのような攻撃から守るように設計されています。

既定では、2 分間で 5 回パスワードの入力に失敗すると、アカウントは 30 分間ロックアウトされます。

アカウント ロックアウト問題の詳細と解決方法については、[Azure AD DS のアカウント ロックアウト問題を解決する][troubleshoot-account-lockout]方法に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

VM をマネージド ドメインに参加させる際の問題が解決しない場合は、[ヘルプを参照し、Azure Active Directory のサポート チケットを開いてください][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md