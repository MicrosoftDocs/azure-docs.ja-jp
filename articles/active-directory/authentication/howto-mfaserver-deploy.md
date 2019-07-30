---
title: Azure Multi-Factor Authentication Server の概要 | Azure Active Directory
description: オンプレミスで Azure MFA Server を使い始めるための詳細な手順
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15900758945fd5c97198caf47ff01fcfb5a6a794
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057413"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server の概要

<center>

![オンプレミスの MFA Server の概要](./media/howto-mfaserver-deploy/server2.png)</center>

このページでは、新しいサーバーのインストールや、オンプレミスの Active Directory を使用したそのサーバーのセットアップについて説明します。 MFA サーバーを既にインストールしており、アップグレードを検討している場合、「[Upgrade to the latest Azure Multi-Factor Authentication Server (最新の Azure Multi-factor Authentication Server へのアップグレード)](howto-mfaserver-deploy-upgrade.md)」を参照してください。 Web サービスをインストールする方法については、[Azure Multi-Factor Authentication Server モバイル アプリ Web サービスのデプロイ](howto-mfaserver-deploy-mobileapp.md)に関する記事を参照してください。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイの MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="plan-your-deployment"></a>デプロイを計画する

> [!WARNING]
> 2019 年 3 月以降、MFA Server をダウンロードできるのは有料のテナントだけです。 無料/試用版テナントが、アクティブ化資格情報をダウンロードまたは生成することはできません。

Azure Multi-Factor Authentication Server をダウンロードする前に、負荷と高可用性の要件の内容について検討してください。 デプロイの方法と場所を決める際にこの情報を使用します。

必要なメモリ量についての適切なガイドラインは、定期的に認証すると予想されるユーザー数です。

| ユーザー | RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

高可用性または負荷分散のために、複数のサーバーをセットアップする必要がありますか。 Azure MFA Server では、この構成をセットアップする方法が多数あります。 最初の Azure MFA Server をインストールすると、それがマスターになります。 追加のサーバーは下位サーバーとなり、自動的にユーザーや構成をマスターと同期します。 その後、1 つのプライマリ サーバーを構成し、残りがバックアップとなるようにすることも、すべてのサーバー間で負荷分散を設定することもできます。

マスター Azure MFA Server がオフラインになっても、下位サーバーは引き続き 2 段階認証要求を処理できます。 ただし、マスターがオンラインに戻るか、下位サーバーが昇格されるまで、新しいユーザーを追加することはできず、既存のユーザーは自身の設定を更新することができません。

### <a name="prepare-your-environment"></a>環境を準備する

Azure Multi-factor Authentication に使用しているサーバーが次の要件を満たしていることを確認します。

| Azure Multi-Factor Authentication Server の要件 | 説明 |
|:--- |:--- |
| ハードウェア |<li>200 MB のハード ディスク容量</li><li>x32 または x64 対応のプロセッサ</li><li>1 GB 以上の RAM</li> |
| ソフトウェア |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2</li><li>Windows Server 2008、SP1、SP2</li><li>Windows Server 2003 R2</li><li>Windows Server 2003、SP1、SP2</li><li>Windows 10</li><li>Windows 8.1 全エディション</li><li>Windows 8 全エディション</li><li>Windows 7 全エディション</li><li>Windows Vista 全エディション、SP1、SP2</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 以上 (ユーザー ポータルまたは Web サービス SDK をインストールする場合)</li> |
| アクセス許可 | Active Directory に登録するためのドメイン管理者アカウントまたはエンタープライズ管理者アカウント |

### <a name="azure-mfa-server-components"></a>Azure MFA Server のコンポーネント

Azure MFA Server は、次の 3 つの Web コンポーネントで構成されます。

* Web サービス SDK - 他のコンポーネントとの通信を可能にするコンポーネントで、Azure MFA アプリケーション サーバーにインストールされます。
* ユーザー ポータル - ユーザーが Azure Multi-Factor Authentication (MFA) に登録を行い、アカウントを保守するための IIS Web サイトです。
* モバイル アプリ Web サービス - Microsoft Authenticator アプリなどのモバイル アプリを使った 2 段階認証を実現します。

これら 3 つのコンポーネントはすべて、インターネットに接続している同じサーバーにインストールすることができます。 これらのコンポーネントを分散させる場合、Web サービス SDK は Azure MFA アプリケーション サーバーに、ユーザー ポータルとモバイル アプリ Web サービスはインターネットに接続しているサーバーにインストールします。

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Server ファイアウォールの要件

各 MFA サーバーは、次のアドレスに送信するポート 443 で通信できる必要があります。

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

送信ファイアウォールがポート 443 で制限されている場合、次の IP アドレスの範囲を開きます。

| IP サブネット | ネットマスク | IP 範囲 |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

イベントの確認機能を使用しておらず、さらにユーザーが企業ネットワーク上のデバイスで確認を行うためのモバイル アプリを使用していない場合、次の範囲のみが必要です。

| IP サブネット | ネットマスク | IP 範囲 |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

## <a name="download-the-mfa-server"></a>MFA Server のダウンロード

> [!WARNING]
> 2019 年 3 月以降、MFA Server をダウンロードできるのは有料のテナントだけです。 無料/試用版テナントが、アクティブ化資格情報をダウンロードまたは生成することはできません。

Azure Portal から Azure Multi-Factor Authentication Server をダウンロードするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA Server]** を選択します。
3. **[サーバーの設定]** を選択します。
4. **[ダウンロード]** をクリックし、ダウンロード ページの手順に従ってインストーラーを保存します。 

   ![Azure portal から MFA Server をダウンロードする](./media/howto-mfaserver-deploy/downloadportal.png)

5. このページは、インストーラーの実行後に参照するので開いたままにしておいてください。

## <a name="install-and-configure-the-mfa-server"></a>MFA Server のインストールと構成

サーバーをダウンロードできたので、インストールと構成を行うことができます。 インストール先のサーバーが、計画セクションに記載されている要件を満たすようにしてください。

1. 実行可能ファイルをダブルクリックします。
2. [インストール フォルダーの選択] 画面で、フォルダーが正しいことを確認し、 **[次へ]** をクリックします。
3. インストールが完了したら、 **[Finish (終了)]** をクリックします。 構成ウィザードが起動します。
4. 構成ウィザードの [ようこそ] 画面で、 **[認証構成ウィザードの使用をスキップする]** をオンにし、 **[次へ]** をクリックします。 ウィザードを閉じると、サーバーが起動します。

   ![認証構成ウィザードの使用をスキップする](./media/howto-mfaserver-deploy/skip2.png)

5. サーバーをダウンロードしたページに戻り、 **[アクティブ化資格情報の生成]** ボタンをクリックします。 この情報を提供されたボックスの Azure MFA Server にコピーし、 **[アクティブ化]** をクリックします。

## <a name="send-users-an-email"></a>ユーザーへの電子メールの送信

ロールアウトの手間をなくすために、MFA Server とユーザーの通信を確保してください。 ユーザーに、2 段階認証に登録されたことを通知する電子メールを MFA Server から送信することができます。

送信する電子メールは、ユーザーの 2 段階認証の構成方法に応じて決定してください。 たとえば、会社のディレクトリから電話番号をインポートできる場合は、ユーザーが行う操作がわかるように、電子メールに既定の電話番号を記載する必要があります。 電話番号をインポートしない場合、またはユーザーがモバイル アプリを使用する場合は、アカウント登録を完了するよう指示する電子メールを送信します。 この電子メールには、Azure Multi-Factor Authentication ユーザー ポータルへのハイパーリンクを含めます。

電子メールの内容は、ユーザーに対して設定されている認証方法 (電話、SMS、モバイル アプリ) によっても異なります。 たとえば、認証に PIN が要求される場合は、PIN の初期設定を電子メールで通知します。 ユーザーは最初の認証時に PIN の変更を要求されます。

### <a name="configure-email-and-email-templates"></a>電子メールと電子メール テンプレートの構成

左側の電子メール アイコンをクリックして、これらの電子メールの送信設定をセットアップできます。 このページにメール サーバーの SMTP 情報を入力し、 **[ユーザーへ電子メールを送信する]** チェック ボックスをオンにすると電子メールを送信できます。

![MFA Server の電子メールの構成](./media/howto-mfaserver-deploy/email1.png)

[電子メールの内容] タブで、選択可能な電子メール テンプレートを確認できます。 ユーザーの 2 段階認証の構成方法に応じて、最適なテンプレートを選択できます。

![コンソールの MFA Server の電子メール テンプレート](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Active Directory からのユーザーのインポート

サーバーをインストールしたら、ユーザーを追加することができます。 ユーザーは手動で作成できるほか、Active Directory からインポートしたり、Active Directory との自動同期を構成したりすることもできます。

### <a name="manual-import-from-active-directory"></a>Active Directory からの手動インポート

1. Azure MFA Server の左側で **[ユーザー]** を選択します。
2. 下部で **[Active Directory からインポート]** を選択します。
3. 個々のユーザーを検索したり、AD ディレクトリでユーザーを含む OU を検索したりできるようになります。 ここではユーザー OU を指定します。
4. 右側のすべてのユーザーを強調表示し、 **[インポート]** をクリックします。 成功したことを通知するポップアップが表示されます。 インポート ウィンドウを閉じます。

   ![Active Directory からの MFA Server ユーザーのインポート](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Active Directory との自動同期

1. Azure MFA Server の左側で **[ディレクトリの統合]** を選択します。
2. **[同期]** タブに移動します。
3. 一番下の **[追加]** をクリックします。
4. 表示された **[同期項目の追加]** ボックスで、この同期タスクのドメイン、OU (**または**セキュリティ グループ)、設定、認証方法の既定値、言語の既定値を選択し、 **[追加]** をクリックします。
5. **[Active Directory との同期を有効にする]** チェック ボックスをオンにし、1 分から 24 時間の範囲で **[同期間隔]** を選択します。

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Azure Multi-Factor Authentication サーバーでのユーザー データの処理方法

オンプレミスで Multi-Factor Authentication (MFA) サーバーを使用すると、ユーザーのデータはオンプレミス サーバーに格納されます。 永続的なユーザー データはクラウドに格納されません。 ユーザーが 2 段階認証を実行すると、MFA Server から Azure MFA クラウド サービスにデータが送信され、認証が実行されます。 これらの認証要求がクラウド サービスに送信されると、次のフィールドが要求で送信され、ログに記録されるので、ユーザーの認証/使用状況レポートで使用できます。 一部のフィールドは省略可能なので、Multi-Factor Authentication サーバー内で有効または無効にすることができます。 MFA サーバーから MFA クラウド サービスへの通信には、送信方向のポート 443 上で SSL/TLS が使用されます。 以下のフィールドが対象です。

* 一意の ID - ユーザー名または内部 MFA サーバー ID
* 姓と名 (省略可能)
* 電子メール アドレス (省略可能)
* 電話番号 - 音声通話または SMS 認証を行う場合
* デバイス トークン - モバイル アプリ認証を行う場合
* 認証モード
* 認証の結果
* MFA サーバー名
* MFA サーバー IP
* クライアント IP - 使用可能な場合

以上のフィールドに加え、認証結果 (成功/拒否) と拒否の理由も認証データと共に格納され、認証/使用状況レポートで使用できます。

> [!IMPORTANT]
> 2019 年 3 月以降、無料/試用版の Azure AD テナントの MFA Server ユーザーは、音声通話オプションを利用できません。 この変更は、SMS メッセージには影響しません。 有料の Azure AD テナントのユーザーは、引き続き音声通話を利用できます。 この変更は、無料/試用版の Azure AD テナントにのみ影響します。

## <a name="back-up-and-restore-azure-mfa-server"></a>Azure MFA Server のバックアップと復元

どのようなシステムであれ、適切なバックアップを確保することは重要です。

Azure MFA Server をバックアップするには、**PhoneFactor.pfdata** ファイルを含んだ **C:\Program Files\Multi-Factor Authentication Server\Data** フォルダーのコピーを保存する必要があります。 

復元する必要が生じた場合は、次の手順を実行します。

1. Azure MFA Server を新しいサーバーに再インストールします。
2. 新しい Azure MFA Server をアクティブにします。
3. **MultiFactorAuth** サービスを停止します。
4. **PhoneFactor.pfdata** をバックアップ コピーで上書きします。
5. **MultiFactorAuth** サービスを開始します。

新しいサーバーが、あらかじめバックアップしておいた構成とユーザー データで稼働します。

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL プロトコルと暗号スイートの管理

MFA Server バージョン 8.x 以降にアップグレードした後またはこのバージョンをインストールした後は、組織で求められない限り、以前の強度の低い暗号スイートを無効にするか、削除することをお勧めします。 このタスクを完了する方法については、「[Managing SSL/TLS Protocols and Cipher Suites for AD FS (AD FS の SSL/TLS プロトコルおよび暗号スイートの管理)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)」を参照してください

## <a name="next-steps"></a>次の手順

- セルフ サービス アクセス用の[ユーザー ポータル](howto-mfaserver-deploy-userportal.md)のセットアップと構成。
- [Active Directory フェデレーション サービス](multi-factor-authentication-get-started-adfs.md)、[RADIUS 認証](howto-mfaserver-dir-radius.md)、または [LDAP 認証](howto-mfaserver-dir-ldap.md)を使用した Azure MFA サーバーのセットアップと構成。
- [RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md) のセットアップと構成。
- [Azure Multi-Factor Authentication Server モバイル アプリ Web サービスのデプロイ](howto-mfaserver-deploy-mobileapp.md)。
- [Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ](howto-mfaserver-nps-vpn.md)。
