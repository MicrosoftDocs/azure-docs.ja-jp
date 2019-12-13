---
title: Azure AD でのレガシ認証プロトコルのブロック
description: 組織でレガシ認証プロトコルをブロックする方法と、それが必要な理由について説明します
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63271567e70955f6dfb0b10a5c882b6dce9545ce
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932188"
---
# <a name="blocking-legacy-authentication"></a>レガシ認証をブロックする
 
指定したユーザーがお使いのクラウド アプリに簡単にアクセスできるように、Azure Active Directory (Azure AD) ではレガシ認証を含め、幅広い認証プロトコルをサポートしています。 レガシ認証は、次のものによって行われる認証要求を指す用語です。

- 先進認証を使用していない古い Office クライアント (Office 2010 クライアントなど)
- IMAP/SMTP/POP3 などの従来のメール プロトコルを使用しているすべてのクライアント

現在、危険にさらそうとするすべてのサインイン試行の大部分はレガシ認証から来ています。 レガシ認証は、多要素認証 (MFA) をサポートしていません。 ディレクトリ上で MFA ポリシーが有効になっている場合でも、悪意のあるアクターはレガシ プロトコルを使用して認証し、MFA を迂回できます。 レガシ プロトコルによって行われる悪意のある認証要求からアカウントを保護するための最善の方法は、これらの試行をすべてまとめてブロックすることです。

## <a name="identify-legacy-authentication-use"></a>レガシ認証の使用を識別する

ディレクトリでレガシ認証をブロックするには、その前にまず、レガシ認証を使用するアプリをユーザーが保有しているかどうかや、それがディレクトリ全体にどのような影響を与えるかを把握する必要があります。 Azure AD のサインイン ログを使用すると、レガシ認証を使用しているかどうかを把握できます。

1. Azure portal > [Azure Active Directory] > [サインイン] に移動します。
1. [クライアント アプリ] 列が表示されていない場合は、[列] > [クライアント アプリ] をクリックして追加します。
1. [クライアント アプリ] でフィルター処理し、表示されるその他のクライアント オプションをすべてオンにして、[適用] をクリックします。
1. [状態] を [成功] でフィルター処理し、[適用] をクリックします。 
1. 必要に応じて [日付] フィルターを使用して、日付範囲を拡張します。

フィルター処理によって、選択したレガシ認証プロトコルによって行われた、成功したサインイン試行のみが表示されます。 個々のサインイン試行をクリックすると、追加の詳細が表示されます。 [基本情報] タブの下の [クライアント アプリ] 列または [クライアント アプリ] フィールドには、データの個々の行の選択後、どのレガシ認証プロトコルが使用されたかが示されます。 これらのログは、どのユーザーがまだレガシ認証に依存しているか、およびどのアプリケーションがレガシ プロトコルを使用して認証要求を行っているかを示しています。 これらのログに表示されず、レガシ認証を使用していないことが確認されたユーザーに対してのみ、条件付きアクセス ポリシーを実装するか、または [ベースライン ポリシー: レガシ認証をブロックする] を有効にします。

## <a name="moving-away-from-legacy-authentication"></a>レガシ認証からの移行 

ディレクトリでどのユーザーがレガシ認証を使用しているか、およびどのアプリケーションがそれに依存しているかが明確になったら、次の手順として、先進認証を使用するようにユーザーをアップグレードします。 先進認証は、より安全なユーザー認証と承認を提供する ID 管理の方法です。 ディレクトリ上に MFA ポリシーが設定されている場合は、先進認証により、ユーザーは必要な場合は確実に MFA の入力を求められるようになります。 これは、レガシ認証プロトコルに代わる、より安全な方法です。

このセクションでは、環境を先進認証に更新する方法に関する詳細な手順の概要を示します。 組織でレガシ認証のブロック ポリシーを有効にする前に、以降の手順に目を通してください。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>手順 1:ディレクトリで先進認証を有効にする

先進認証を有効にするための最初の手順は、ディレクトリで先進認証がサポートされていることを確認することです。 2017 年 8 月 1 日以降に作成されたディレクトリに対して、先進認証は既定で有効になっています。 ディレクトリがこの日付の前に作成された場合は、次の手順を使用して、そのディレクトリに対して先進認証を手動で有効にする必要があります。

1.  [Skype for Business Online の PowerShell モジュール](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)から `Get-CsOAuthConfiguration` を実行して、ディレクトリで既に先進認証がサポートされているかどうかを確認します。
1. このコマンドから空の `OAuthServers` プロパティが返される場合は、先進認証が無効になっています。  `Set-CsOAuthConfiguration` を使用して、先進認証を有効にするように設定を更新します。  `OAuthServers` プロパティにエントリが含まれている場合は、準備ができています。

先に進む前に、必ずこの手順を完了するようにしてください。 ディレクトリ構成によりすべての Office クライアントが使用するプロトコルが規定されるため、まず、その構成が変更されることが重要です。 先進認証をサポートする Office クライアントを使用している場合でも、ディレクトリで先進認証が無効になっていると、既定でレガシ プロトコルが使用されます。

### <a name="step-2-office-applications"></a>手順 2:Office アプリケーション

ディレクトリで先進認証を有効にしたら、Office クライアントの先進認証を有効にすることによってアプリケーションの更新を開始できます。 Office 2016 以降のクライアントは、既定で先進認証をサポートしています。 追加の手順は必要ありません。

Office 2013 以前の Windows クライアントを使用している場合は、Office 2016 以降にアップグレードすることをお勧めします。 ディレクトリで先進認証を有効にする前の手順を完了した後でも、古い Office アプリケーションは引き続きレガシ認証プロトコルを使用します。 Office 2013 クライアントを使用しており、直ちに Office 2016 以降にアップグレードできない場合は、「 [Windows デバイスの Office 2013 の先進認証を有効にする](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)」の記事にある手順に従ってください。 レガシ認証を使用しているときのアカウントの保護に役立つように、ディレクトリ全体で強力なパスワードを使用することをお勧めします。 ディレクトリ全体で脆弱なパスワードを禁止するには、 [Azure AD のパスワード保護](../authentication/concept-password-ban-bad.md)に関するページを確認してください。 

Office 2010 は、先進認証をサポートしていません。 Office 2010 を使用しているすべてのユーザーを最新バージョンの Office にアップグレードする必要があります。 既定でレガシ認証がブロックされるため、Office 2016 以降にアップグレードすることをお勧めします。

MacOS を使用している場合は、Office for Mac 2016 以降にアップグレードすることをお勧めします。 ネイティブなメール クライアントを使用している場合は、すべてのデバイスに MacOS バージョン 10.14 以降を用意する必要があります。

### <a name="step-3-exchange-and-sharepoint"></a>手順 3:Exchange と SharePoint

Windows ベースの Outlook クライアントで先進認証を使用するには、Exchange Online でも先進認証が有効になっている必要があります。 Exchange Online に対して先進認証が無効になっている場合、先進認証をサポートする Windows ベースの Outlook クライアント (Outlook 2013 以降) は、基本認証を使用して Exchange Online メールボックスに接続します。

SharePoint Online は、先進認証が既定で有効になっています。 2017 年 8 月 1 日の後に作成されたディレクトリの場合、先進認証は Exchange Online で既定で有効になっています。 ただし、以前に先進認証を無効にしたか、またはこの日付の前に作成されたディレクトリを使用している場合は、「 [Exchange Online で先進認証を有効にする](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)」の記事にある手順に従ってください。

### <a name="step-4-skype-for-business"></a>手順 4:Skype for Business

Skype for Business によって行われるレガシ認証要求を防止するには、Skype for Business Online の先進認証を有効にする必要があります。 2017 年 8 月 1 日の後に作成されたディレクトリの場合、Skype for Business の先進認証は既定で有効になっています。

既定で先進認証をサポートする Microsoft Teams に移行することをお勧めします。 ただし、現時点で移行できない場合は、Skype for Business クライアントが先進認証の使用を開始できるように、Skype for Business Online の先進認証を有効にする必要があります。 Skype for Business の先進認証を有効にするには、「 [先進認証でサポートされる Skype for Business トポロジ](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)」の記事にある手順に従ってください。

Skype for Business Online の先進認証の有効化に加えて、Skype for Business の先進認証を有効にするときに Exchange Online に対して先進認証を有効にすることをお勧めします。 このプロセスは Exchange Online と Skype for Business Online の先進認証の状態を同期するのに役立つと共に、Skype for Business クライアントに複数のサインイン プロンプトが表示されることを防止します。

### <a name="step-5-using-mobile-devices"></a>手順 5:モバイル デバイスの使用

モバイル デバイス上のアプリケーションでも、レガシ認証をブロックする必要があります。 Outlook for Mobile を使用することをお勧めします。 Outlook for Mobile は既定で先進認証をサポートし、他の MFA ベースライン保護ポリシーを満足します。

ネイティブな iOS メール クライアントを使用するには、メール クライアントが確実にレガシ認証をブロックするように更新されるように、iOS バージョン 11.0 以降を実行している必要があります。

### <a name="step-6-on-premises-clients"></a>手順 6:オンプレミスのクライアント

Exchange Server と Skype for Business をオンプレミスで使用しているハイブリッドのお客様の場合は、先進認証を有効にするように両方のサービスを更新する必要があります。 ハイブリッド環境で先進認証を使用している場合も、引き続きオンプレミスでユーザーを認証します。 ユーザーのリソース (ファイルまたは電子メール) へのアクセスを承認する方法は変化します。

オンプレミスで先進認証の有効化を開始する前に、前提条件を満たしていることを確認してください。 これで、オンプレミスで先進認証を有効にする準備ができました。

先進認証を有効にする手順については、次の記事を参照してください。

* [ハイブリッド先進認証を使用するようにオンプレミスで Exchange Server を構成する方法](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)に関するページ
* [Skype for Business で先進認証 (ADAL) を使用する方法](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)に関するページ

## <a name="next-steps"></a>次の手順

- [ハイブリッド先進認証を使用するようにオンプレミスで Exchange Server を構成する方法](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)に関するページ
- [Skype for Business で先進認証 (ADAL) を使用する方法](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)に関するページ
- [レガシ認証をブロックする](../conditional-access/block-legacy-authentication.md)
