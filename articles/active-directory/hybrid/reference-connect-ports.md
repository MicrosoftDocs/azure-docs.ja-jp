---
title: ハイブリッド ID で必要なポートとプロトコル - Azure | Microsoft Docs
description: このページは Azure AD Connect のために開く必要があるポートのテクニカル リファレンス ページです
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520032"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>ハイブリッド ID で必要なポートとプロトコル
次のドキュメントは、ハイブリッド ID ソリューションを実装するために必要なポートとプロトコルに関するテクニカル リファレンスです。 次の図を使用して、対応する表を参照してください。

![What is Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>表 1 - Azure AD Connect とオンプレミスの AD
この表は、Azure AD Connect サーバーとオンプレミスの AD 間の通信に必要なポートとプロトコルについて説明しています。

| Protocol | Port | 説明 |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |送信先フォレストでの DNS 参照です。 |
| Kerberos |88 (TCP/UDP) |AD フォレストに対する Kerberos 認証です。 |
| MS-RPC |135 (TCP) |Azure AD Connect ウィザードを AD フォレストにバインドするときの初期構成中に使用され、パスワードの同期中にも使用されます。 |
| LDAP |389 (TCP/UDP) |AD からのデータのインポートに使用されます。 データは Kerberos の署名およびシールによって暗号化されます。 |
| SMB | 445 (TCP) |AD フォレストにコンピューター アカウントを作成するためにシームレス SSO によって使用されます。 |
| LDAP/SSL |636 (TCP/UDP) |AD からのデータのインポートに使用されます。 データ転送は署名されて暗号化されます。 TLS を使用している場合にのみ使用されます。 |
| RPC |49152 から 65535 (ランダムな高い数値の RPC ポート)(TCP) |Azure AD Connect を AD フォレストにバインドするときの初期構成中と、パスワードの同期中に使用されます。 動的ポートが変更されている場合は、そのポートを開く必要があります。 詳細については、[KB929851](https://support.microsoft.com/kb/929851)、[KB832017](https://support.microsoft.com/kb/832017)、[KB224196](https://support.microsoft.com/kb/224196) を参照してください。 |
|WinRM  | 5985 (TCP) |Azure AD Connect ウィザードで gMSA を使用して AD FS をインストールする場合にのみ使用します|
|AD DS Web サービス | 9389 (TCP) |Azure AD Connect ウィザードで gMSA を使用して AD FS をインストールする場合にのみ使用します |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>表 2 - Azure AD Connect と Azure AD
この表は、Azure AD Connect サーバーと Azure AD 間の通信に必要なポートとプロトコルについて説明しています。

| Protocol | Port | 説明 |
| --- | --- | --- |
| HTTP |80 (TCP) |TLS/SSL 証明書を検証するための CRL (証明書失効リスト) をダウンロードするために使用されます。 |
| HTTPS |443(TCP) |Azure AD と同期するために使用されます。 |

ファイアウォールで開く必要のある URL と IP アドレスの一覧については、「[Office 365 の URL と IP アドレスの範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」および [Azure AD Connect の接続性のトラブルシューティング](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard)に関するページを参照してください。

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>表 3 - Azure AD Connect と AD FS フェデレーション サーバー/WAP
この表は、Azure AD Connect サーバーと AD FS フェデレーション/WAP サーバー間の通信に必要なポートとプロトコルについて説明しています。  

| Protocol | Port | 説明 |
| --- | --- | --- |
| HTTP |80 (TCP) |TLS/SSL 証明書を検証するための CRL (証明書失効リスト) をダウンロードするために使用されます。 |
| HTTPS |443(TCP) |Azure AD と同期するために使用されます。 |
| WinRM |5985 |WinRM リスナー |

## <a name="table-4---wap-and-federation-servers"></a>表 4 - WAP とフェデレーション サーバー
この表は、フェデレーション サーバーと WAP サーバー間の通信に必要なポートとプロトコルについて説明しています。

| Protocol | Port | 説明 |
| --- | --- | --- |
| HTTPS |443(TCP) |認証で使用されます。 |

## <a name="table-5---wap-and-users"></a>表 5 - WAP とユーザー
この表は、ユーザーと WAP サーバー間の通信に必要なポートとプロトコルについて説明しています。

| Protocol | Port | 説明 |
| --- | --- | --- |
| HTTPS |443(TCP) |デバイスの認証で使用されます。 |
| TCP |49443 (TCP) |証明書の認証で使用されます。 |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>表 6a および 6b - シングル サインオン (SSO) でのパススルー認証およびシングル サインオン (SSO) でのパスワード ハッシュ同期
次の表は、Azure AD Connect と Azure AD 間の通信に必要なポートとプロトコルについて説明しています。

### <a name="table-6a---pass-through-authentication-with-sso"></a>表 6a - SSO でのパススルー認証のトラブルシューティング
|プロトコル|ポート番号|説明
| --- | --- | ---
|HTTP|80|SSL などのセキュリティ検証用の送信 HTTP トラフィックに使用されます。 コネクタの自動更新機能が正常に機能するためにも必要です。
|HTTPS|443| 機能の有効化と無効化、コネクタの登録、コネクタ更新プログラムのダウンロード、およびすべてのユーザー サインイン要求の処理などの操作のために、送信 HTTPS トラフィックを有効にします。

さらに、Azure AD コネクタが [Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に対して直接 IP 接続できる必要があります。

### <a name="table-6b---password-hash-sync-with-sso"></a>表 6b - SSO でのパスワード ハッシュ同期

|プロトコル|ポート番号|説明
| --- | --- | ---
|HTTPS|443| SSO 登録を有効にします (SSO 登録プロセスでのみ必要です)。

さらに、Azure AD コネクタが [Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に対して直接 IP 接続できる必要があります。 ここでも、これは SSO 登録プロセスでのみ必要です。

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a & 7b - Azure AD Connect Health エージェント (AD FS/Sync) と Azure AD
次の表は、Azure AD Connect Health エージェントと Azure AD 間の通信に必要なエンドポイント、ポート、プロトコルについて説明しています。

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a - Azure AD Connect Health エージェント (AD FS/Sync) と Azure AD 用のポートとプロトコル
この表は、Azure AD Connect Health エージェントと Azure AD 間の通信に必要な以下の送信ポートとプロトコルについて説明しています。  

| Protocol | Port | 説明 |
| --- | --- | --- |
| HTTPS |443(TCP) |送信 |
| Azure Service Bus |5671 (TCP) |送信 |

Azure Service Bus ポート 5671 は最新バージョンのエージェントでは必要なくなりました。 Azure AD Connect Health エージェントの最新バージョンで必要なのは、ポート 443 のみです。

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Azure AD Connect Health エージェント (AD FS/Sync) と Azure AD 用のエンドポイント
エンドポイントの一覧については、 [Azure AD Connect Health エージェントの「必要条件」セクション](how-to-connect-health-agent-install.md#requirements)を参照してください。

