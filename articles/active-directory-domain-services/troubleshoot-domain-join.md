---
title: Azure AD Domain Services でのドメイン参加のトラブルシューティング | Microsoft Docs
description: VM にドメイン参加するか、アプリケーションを Azure Active Directory Domain Services に接続しようとしてマネージド ドメインに対して接続または認証ができないときの、一般的な問題のトラブルシューティングについて説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618605"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインに対するドメイン参加の問題のトラブルシューティング

仮想マシン (VM) に参加しようとするか、アプリケーションを Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインに接続しようとすると、これを行うことができないというエラーが表示されることがあります。 ドメイン参加の問題のトラブルシューティングを行うには、次のようにどこでイシューが発生しているかを確認してください。

* 認証プロンプトが表示されない場合は、VM またはアプリケーションが Azure AD DS マネージド ドメインに接続できません。
    * [ドメイン参加に関する接続イシュー](#connectivity-issues-for-domain-join)のトラブルシューティングを行います。
* 認証中にエラーが発生した場合は、マネージド ドメインに接続することができます。
    * [ドメイン参加中の資格情報関連のイシュー](#credentials-related-issues-during-domain-join)のトラブルシューティングを行います。

## <a name="connectivity-issues-for-domain-join"></a>ドメイン参加の接続性のイシュー

VM がマネージド ドメインを見つけられない場合は、通常、ネットワーク接続または構成のイシューが発生しています。 以下のトラブルシューティングのステップを確認してイシューを見つけ、解決します。

1. マネージド ドメインと同じ、またはピアリングされた仮想ネットワークに VM が接続されていることを確認します。 それ以外の場合、VM は参加するためのドメインを見つけて接続することができません。
    * VM が同じ仮想ネットワークに接続されていない場合は、トラフィックが正常に流れるように、仮想ネットワーク ピアリングまたは VPN 接続が *[Active]\(有効\)* または *[Connected]\(接続済み\)* になっていることを確認します。
1. マネージド ドメインのドメイン名を使用して、ドメインの ping を試行してください (例: `ping aaddscontoso.com`)。
    * ping 応答が失敗した場合は、マネージド ドメインの、ポータルの概要ページに表示されるドメインの IP アドレスに対して ping を実行します (`ping 10.0.0.4` など)。
    * ドメインではなく IP アドレスを ping できた場合、DNS が正しく構成されていない可能性があります。 [マネージド ドメインの DNS サーバーを仮想ネットワークの DNS サーバーとして構成した][configure-dns]ことを確認してください。
1. 仮想マシンの DNS リゾルバー キャッシュのフラッシュを試行します (`ipconfig /flushdns` など)。

### <a name="network-security-group-nsg-configuration"></a>ネットワーク セキュリティ グループ (NSG) の構成

マネージド ドメインを作成すると、ドメイン操作を成功させるための適切な規則を含むネットワーク セキュリティ グループも作成されます。 追加のネットワーク セキュリティ グループの規則を編集または作成した場合、Azure AD DS が接続と認証のサービスを提供するために必要なポートが誤ってブロックされる可能性があります。 これらのネットワーク セキュリティ グループの規則により、パスワード同期が完了しない、ユーザーがサインインできないといったイシューや、ドメイン参加のイシューが発生する可能性があります。

接続のイシューが解決しない場合は、次のトラブルシューティングのステップを確認してください。

1. Azure portal でマネージド ドメインの正常性状態を確認します。 *AADDS001* のアラートが発生している場合は、ネットワーク セキュリティ グループの規則によってアクセスがブロックされています。
1. [必要なポートとネットワーク セキュリティ グループの規則][network-ports]を確認します。 接続している VM または仮想ネットワークに適用されているネットワーク セキュリティ グループの規則が、これらのネットワーク ポートをブロックしていないことを確認してください。
1. ネットワーク セキュリティ グループの構成のイシューが解決されると、正常性のページの *AADDS001* アラートが 2 時間ほどで消えます。 ネットワーク接続を利用できるようになったので、もう一度 VM にドメイン参加してみてください。

## <a name="credentials-related-issues-during-domain-join"></a>ドメイン参加中の資格情報関連のイシュー

マネージド ドメインに参加するための資格情報を要求するダイアログ ボックスが表示された場合、VM は Azure 仮想ネットワークを使用してドメインに接続できます。 ドメイン参加プロセスは、ドメインへの認証または資格情報を使用したドメイン参加プロセスを完了するための承認に失敗します。

資格情報に関連するイシューのトラブルシューティングを行うには、次のトラブルシューティングのステップを確認してください。

1. UPN 形式を使用して資格情報を指定します (例: `dee@contoso.onmicrosoft.com`)。 この UPN が Azure AD で正しく構成されていることを確認してください。
    * 複数のユーザーがテナントで同じ UPN プレフィックスを使用していたり、使用する UPN プレフィックスが最大文字数を超えている場合は、アカウントの *SAMAccountName* が自動生成されることがあります。 そのため、お使いのアカウントの *SAMAccountName* 形式が想定した形式やオンプレミス ドメインで使用する形式と異なる可能性があります。
1. マネージド ドメインの一部であるユーザー アカウントの資格情報を使用して、マネージド ドメインへの VM の参加を試みます。
1. [パスワード同期が有効になっている][enable-password-sync]ことを確認し、最初のパスワード同期が完了するまで十分な時間待機します。

## <a name="next-steps"></a>次のステップ

Active Directory プロセスのうち、ドメイン参加操作の詳細については、「[参加と認証のイシュー][join-authentication-issues]」を参照してください。

VM をマネージド ドメインに参加させる際の問題が解決しない場合は、[ヘルプを参照し、Azure Active Directory のサポート チケットを開いてください][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
