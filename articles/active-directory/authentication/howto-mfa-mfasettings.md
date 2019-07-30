---
title: Azure Multi-Factor Authentication を構成する - Azure Active Directory
description: この記事では、Azure Portal で Azure Multi-Factor Authentication 設定を構成する方法について説明します
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 618377d532dc932eb21f282ac8ba7f6c07df16f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113451"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure Multi-Factor Authentication の設定を構成する

この記事は、Azure portal で Multi-Factor Authentication の設定を管理するうえで役立ちます。 ここでは、Azure Multi-factor Authentication を最大限に活用するために役立つさまざまなトピックについて説明します。 すべてのバージョンの Azure Multi-Factor Authentication ですべての機能を使用できるわけではありません。

Azure portal で Azure Multi-Factor Authentication に関連する設定にアクセスするには、 **[Azure Active Directory]**  >  **[MFA]** の順に移動します。

![Azure portal - Azure AD Multi-Factor Authentication の設定](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>設定

これらの設定の一部は、MFA サーバー、Azure MFA、またはその両方に適用されます。

| 機能 | 説明 |
| ------- | ----------- |
| アカウントのロックアウト | 連続して拒否された認証試行が多すぎる場合に、Multi-Factor Authentication サービス内でアカウントを一時的にロックします。 この機能は、認証のために PIN を入力するユーザーにのみ適用されます。 (MFA サーバー) |
| [ユーザーのブロック/ブロック解除](#block-and-unblock-users) | 特定のユーザーが Multi-Factor Authentication 要求を受信できないようにするために使用されます。 ブロックされているユーザーを認証しようとすると、自動的に拒否されます。 ユーザーはブロックされた時間から 90 日間ブロックされ続けます。 |
| [不正アクセスのアラート](#fraud-alert) | ユーザーが不正な確認要求をレポートできるかどうかに関する設定を構成します |
| 通知 | MFA サーバーからのイベントの通知を有効にします。 |
| [OATH トークン](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | クラウドベースの Azure MFA 環境で、ユーザーの OATH トークンを管理するために使用されます。 |
| [電話の設定](#phone-call-settings) | クラウド環境とオンプレミス環境の電話と案内メッセージに関連する設定を構成します。 |
| プロバイダー | アカウントに関連付けた可能性がある既存の認証プロバイダーを表示します。 2018 年 9 月 1 日時点では、新しい認証プロバイダーは作成できません |

## <a name="manage-mfa-server"></a>MFA サーバーの管理

このセクションの設定は、MFA サーバーのみが対象です。

| 機能 | 説明 |
| ------- | ----------- |
| サーバーの設定 | MFA サーバーをダウンロードして、環境を初期化するためのアクティブ化資格情報を生成します |
| [ワンタイム バイパス](#one-time-bypass) | 期間限定で、2 段階認証を実行することなくユーザーを認証できるようにします。 |
| [キャッシュ規則](#caching-rules) |  キャッシュは主に、VPN などのオンプレミス システムによって複数の認証要求が送信され、最初の要求がまだ処理中である場合に使用されます。 この機能を使用すれば、処理中の最初の認証にユーザーが成功した後で、後続の要求が自動的に処理されるようになります。 |
| サーバーの状態 | バージョン、状態、IP、最後の通信日時など、オンプレミスの MFA サーバーの状態を表示します。 |

## <a name="activity-report"></a>アクティビティ レポート

ここで使用できるレポートは、MFA サーバー (オンプレミス) に固有のものです。 Azure MFA (クラウド) のレポートについては、Azure AD のサインイン レポートを参照してください。

## <a name="block-and-unblock-users"></a>ユーザーのブロックおよびブロック解除

ユーザーが認証要求を受信できないようにする場合は、_ユーザーのブロック/ブロック解除_ 機能を使用します。 ブロックされているユーザーを認証しようとすると、自動的に拒否されます。 ユーザーはブロックされた時間から 90 日間ブロックされ続けます。

### <a name="block-a-user"></a>ユーザーのブロック

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[ユーザーのブロック/ブロック解除]** に移動します。
3. **[追加]** を選択してユーザーをブロックします。
4. **[レプリケーション グループ]** を選択します。 ブロックされているユーザーのユーザー名を **username\@domain.com** として入力します。 **[理由]** フィールドにコメントを入力します。
5. **[追加]** を選択してユーザーのブロックを終了します。

### <a name="unblock-a-user"></a>ユーザーのブロック解除

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[ユーザーのブロック/ブロック解除]** に移動します。
3. ブロック解除するユーザーの横にある、 **[アクション]** 列の **[ブロック解除]** を選択します。
4. **[Reason for unblocking]\(ブロック解除の理由)** フィールドにコメントを入力します。
5. **[ブロック解除]** を選択して、ユーザーのブロック解除を終了します。

## <a name="fraud-alert"></a>不正アクセスのアラート

ユーザーが各自のリソースに対する不正アクセスの試みを通報できるように、_不正アクセスのアラート_ 機能を構成します。 ユーザーは、モバイル アプリまたは電話で不正アクセスの試みを通報できます。

### <a name="turn-on-fraud-alerts"></a>不正アクセスのアラートを有効にする

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[不正アクセスのアラート]** の順に移動します。
3. **[ユーザーが不正アクセスを通報できるようにする]** 設定を **[オン]** に設定します。
4. **[保存]** を選択します。

### <a name="configuration-options"></a>構成オプション

* **[Block user when fraud is reported]\(不正アクセスが報告されたときのユーザーのブロック\)** : ユーザーが不正アクセスを報告した場合、そのユーザーのアカウントは 90 日間、または管理者がそのアカウントのブロックを解除するまでブロックされます。 管理者はサインイン レポートを使用してサインインを確認し、将来の不正アクセスを防止するための適切なアクションを実行できます。 その後、管理者はそのユーザーのアカウントの[ブロックを解除](#unblock-a-user)できます。
* **[Code to report fraud during initial greeting]\(案内メッセージ中に入力する不正アクセス通報コード\)** : ユーザーは、2 段階認証を実行する電話を受けた場合、通常、 **#** を押してサインインを確認します。 不正アクセスを通報する場合は、 **#** を押す前にコードを入力します。 このコードは、既定では **0** ですが、カスタマイズすることができます。

   >[!NOTE]
   >Microsoft の既定の音声案内では、不正アクセスのアラートを送信するには **0#** を押すようにユーザーに指示します。 **0** 以外のコードを使用する場合は、ユーザーに適切な指示を行う独自のカスタム音声案内を記録し、アップロードする必要があります。
   >

### <a name="view-fraud-reports"></a>不正アクセスの通報を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[サインイン]** の順に選択します。これで、不正アクセスの通報が標準の Azure AD サインイン レポートの一部になります。

## <a name="phone-call-settings"></a>電話の設定

### <a name="caller-id"></a>発信者 ID

**MFA 発信者の ID 番号** - これは、ユーザーの電話に表示される番号です。 米国ベースの番号だけを使用できます。

>[!NOTE]
>公衆電話網経由で Multi-Factor Authentication の呼び出しが行われた場合、発信者 ID をサポートしていない通信事業者を通じて呼び出しがルーティングされることがあります。 このため、Multi-Factor Authentication システムが常に発信者 ID を送信していても、発信者 ID は保証されません。

### <a name="custom-voice-messages"></a>カスタム音声メッセージ

_カスタム音声メッセージ_ 機能では、独自の録音やあいさつを 2 段階認証で使用できます。 これらのメッセージは、Microsoft による録音に加えて使用するか、その代わりに使用できます。

開始する前に、次の制限に注意してください。

* サポートされているファイルの形式は .wav と .mp3 です。
* ファイル サイズの上限は 5 MB です。
* 認証メッセージは、20 秒より短くする必要があります。 20 秒より長いメッセージの場合は、確認に失敗する可能性があります。 メッセージが終わる前にユーザーが応答せず、確認がタイムアウトになる可能性があります。

### <a name="custom-message-language-behavior"></a>カスタム メッセージ言語の動作

カスタム音声メッセージがユーザーに再生されるときのメッセージの言語は、次の要因によって決まります。

* 現在のユーザーの言語。
  * ユーザーのブラウザーで検出された言語。
  * 他の認証シナリオでは、異なる動作になる可能性があります。
* 使用できるカスタム メッセージの言語。
  * この言語は、カスタム メッセージが追加されるときに管理者が選択します。

たとえば、カスタム メッセージが 1 つのみで、言語がドイツ語の場合は次のようになります。

* ドイツ語で認証されたユーザーには、カスタムのドイツ語のメッセージが聞こえます。
* 英語で認証されたユーザーには、標準の英語メッセージが聞こえます。

### <a name="set-up-a-custom-message"></a>カスタム メッセージを設定する

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[MFA]**  >  **[電話の設定]** の順に移動します。
1. **[案内の追加]** を選択します。
1. 案内の種類を選択します。
1. 言語を選択します。
1. アップロードする .mp3 や .wav サウンド ファイルを選択します。
1. **[追加]** を選択します。

## <a name="one-time-bypass"></a>ワンタイム バイパス

_ワンタイム バイパス_ 機能は、2 段階認証の実行なしでユーザーを 1 回だけ認証できるようにします。 このバイパスは一時的なものであり、指定された秒数が経過すると無効になります。 モバイル アプリまたは電話で通知または通話に対応しない状況でも、ワンタイム バイパスを使用することで、ユーザーは目的のリソースにアクセスできるようになります。

### <a name="create-a-one-time-bypass"></a>ワンタイム バイパスを作成する

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[ワンタイム バイパス]** の順に移動します。
3. **[追加]** を選択します。
4. 必要に応じて、バイパスのレプリケーション グループを選択します。
5. ユーザー名を **username\@domain.com** として入力します。 バイパスが持続する秒数を入力します。 バイパスの理由を入力します。
6. **[追加]** を選択します。 制限時間はすぐに有効になります。 ユーザーはワンタイム バイパスの有効期限が切れる前にサインインする必要があります。

### <a name="view-the-one-time-bypass-report"></a>ワンタイム バイパス レポートを表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[ワンタイム バイパス]** の順に移動します。

## <a name="caching-rules"></a>キャッシュ規則

_キャッシュ_ 機能を使用して、ユーザー認証後の認証の試みを許可する期間を設定することができます。 指定した期間内のユーザーの後続の認証の試みは自動的に成功します。 キャッシュは主に、VPN などのオンプレミス システムによって複数の認証要求が送信され、最初の要求がまだ処理中である場合に使用されます。 この機能を使用すれば、処理中の最初の認証にユーザーが成功した後で、後続の要求が自動的に処理されるようになります。

>[!NOTE]
>キャッシュ機能は Azure Active Directory (Azure AD) へのサインインに使用するためのものではありません。

### <a name="set-up-caching"></a>キャッシュを設定する

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[MFA]**  >  **[キャッシュ規則]** の順に移動します。
3. **[追加]** を選択します。
4. ドロップダウン リストから**キャッシュの種類**を選択します。 **キャッシュする秒数**の最大数を入力します。
5. 必要に応じて、認証の種類を選択し、アプリケーションを指定します。
6. **[追加]** を選択します。

## <a name="mfa-service-settings"></a>MFA サービスの設定

Azure Multi-Factor Authentication におけるアプリ パスワード、信頼できる IP、確認オプション、および多要素認証の記憶の設定は、サービス設定で確認できます。 Azure portal からサービス設定にアクセスするには、 **[Azure Active Directory]**  >  **[MFA]**  >  **[作業の開始]**  >  **[構成]**  >  **[追加のクラウドベースの MFA 設定]** の順に移動します。

![Azure Multi-Factor Authentication のサービス設定](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>アプリ パスワード

Office 2010 以前や iOS 11 以前の Apple Mail のような一部のアプリでは、2 段階認証はサポートされません。 アプリは、2 つ目の認証を受け入れるように構成されていません。 これらのアプリケーションを使用する場合は、_アプリ パスワード_ 機能を利用します。 従来のパスワードの代わりにアプリ パスワードを使用すれば、アプリが 2 段階認証をバイパスして動作を続行できるように指定できます。

Microsoft Office 2013 クライアント以降向けの最新の認証がサポートされています。 Office 2013 クライアント (Outlook を含む) は、最新の認証プロトコルをサポートしており、2 段階認証を有効にすることができます。 クライアントを有効にした場合、そのクライアントではアプリ パスワードは必要ありません。

>[!NOTE]
>条件付きアクセス ベースの多要素認証ポリシーと最新の認証では、アプリ パスワードは機能しません。

### <a name="considerations-about-app-passwords"></a>アプリ パスワードに関する考慮事項

アプリ パスワードを使用する場合は、次の重要な点を考慮してください。

* アプリ パスワードは、アプリケーションごとに 1 回だけ入力します。 ユーザーはパスワードを記録する必要はなく、また毎回入力する必要はありません。
* 実際のパスワードは自動的に生成され、ユーザーが指定することはありません。 自動的に生成されたパスワードのほうが攻撃者から推測されづらく、より安全です。
* 1 ユーザーあたり 40 個というパスワード数の制限があります。
* パスワードをキャッシュし、オンプレミス シナリオで使用するアプリケーションは、アプリ パスワードが職場または学校アカウント外では不明であるため、起動できない場合があります。 このシナリオの例として、オンプレミスだが、アーカイブ メールはクラウド内にある Exchange メールがあります。 このシナリオでは、同じパスワードは機能しません。
* ユーザーのアカウントで Multi-Factor Authentication を有効にした後で、Outlook や Microsoft Skype for Business などのほとんどのブラウザー以外のクライアントでアプリ パスワードを使用できます。 Windows PowerShell などのブラウザー以外のアプリケーションからアプリ パスワードを使用して、管理操作を実行することはできません。 ユーザーが管理者アカウントを持っている場合でも、操作を実行することはできません。 PowerShell スクリプトを実行するには、サービス アカウントを強固なパスワードで作成します。そのアカウントで 2 段階認証を有効にしないでください。

>[!WARNING]
>クライアントがオンプレミスの自動検出エンドポイントとクラウドの自動検出エンドポイントの両方と通信するハイブリッド環境では、アプリ パスワードは機能しません。 オンプレミスでの認証にはドメイン パスワードが必要です。 クラウドでの認証にはアプリ パスワードが必要です。

### <a name="guidance-for-app-password-names"></a>アプリ パスワードの名前に関するガイダンス

アプリ パスワードの名前は、それが使用されるデバイスを反映させるようにします。 Outlook、Word、Excel などのブラウザー以外のアプリケーションがあるラップトップでは、これらのアプリに対して **Laptop** という名前のアプリ パスワードを 1 つ作成します。 デスクトップ コンピューター上で実行される同じアプリケーションに対しては、**Desktop** という名前の別のアプリ パスワードを作成します。

>[!NOTE]
>アプリケーションごとに 1 つのアプリ パスワードではなく、デバイスごとに 1 つのアプリ パスワードを作成することをお勧めします。

### <a name="federated-or-single-sign-on-app-passwords"></a>フェデレーション (シングル サインオン) アプリ パスワード

Azure AD は、オンプレミスの Windows Server Active Directory ドメイン サービス (AD DS) とのフェデレーション (シングル サインオン (SSO)) をサポートします。 組織が Azure AD とフェデレーションされているときに Azure Multi-factor Authentication を使用する場合は、アプリ パスワードについて、次の点を考慮してください。

>[!NOTE]
>次の点はフェデレーション (SSO) 顧客にのみ適用されます。

* アプリ パスワードは Azure AD によって検証されます。したがって、フェデレーションをバイパスします。 フェデレーションは、アプリ パスワードを設定するときにのみアクティブに使用されます。
* フェデレーション (SSO) ユーザーの場合、パッシブ フローとは異なり、ID プロバイダー (IdP) には接続されません。 アプリ パスワードは職場または学校アカウントに格納されます。 ユーザーが退職した場合、そのユーザーの情報は、**DirSync** を使用して、リアルタイムで職場または学校アカウントに送信されます。 アカウントの無効化/削除を同期させるには最大 3 時間かかる場合があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
* オンプレミスのクライアント アクセス制御設定は、アプリ パスワード機能には適用されません。
* アプリ パスワード機能で使用できるオンプレミスの認証ログ/監査機能はありません。
* 一部の高度なアーキテクチャでは、2 段階認証をクライアントで使用するときに資格情報の組み合わせが必要になります。 これらの資格情報には、職場または学校アカウントのユーザー名とパスワード、およびアプリ パスワードを含めることができます。 要件は認証の実行方法によって異なります。 オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、職場または学校アカウントのユーザー名が必要です。 Azure AD に対して認証するクライアントの場合は、アプリ パスワードが必要です。

  たとえば、次のアーキテクチャがあるとします。

  * Active Directory のオンプレミスのインスタンスが Azure AD とフェデレーションしている。
  * Exchange をオンラインで使用している。
  * Skype for Business をオンラインで使用している。
  * Azure Multi-Factor Authentication を使用している。

  このシナリオでは、次の資格情報を使用します。

  * Skype for Business にサインインする場合は、職場または学校アカウントのユーザー名とパスワードを使用します。
  * Exchange にオンラインで接続している Outlook クライアントからアドレス帳にアクセスする場合は、アプリ パスワードを使用します。

### <a name="allow-users-to-create-app-passwords"></a>ユーザーがアプリ パスワードを作成できるようにする

既定では、ユーザーはアプリ パスワードを作成できません。 アプリ パスワード機能を有効にする必要があります。 ユーザーがアプリ パスワードを作成できるようにするには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、 **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. **[サービス設定]** ページで、 **[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する]** オプションを選択します。

### <a name="create-app-passwords"></a>アプリ パスワードを作成する

ユーザーは、初回の登録時にアプリ パスワードを作成できます。 登録プロセスの最後に、アプリ パスワードを作成するためのオプションが表示されます。

ユーザーによるアプリ パスワードの作成は、登録後も可能です。 アプリ パスワードの詳細と、ユーザーが実行する手順については、「[Azure Multi-Factor Authentication のアプリ パスワードとは](../user-help/multi-factor-authentication-end-user-app-passwords.md)」を参照してください。

## <a name="trusted-ips"></a>信頼できる IP

Azure Multi-Factor Authentication の _信頼できる IP_ 機能は、マネージドテナントまたはフェデレーション テナントの管理者が使用します。 この機能では、会社のイントラネットからサインインするユーザーの 2 段階認証をバイパスします。 この機能は、Azure Multi-Factor Authentication の完全なバージョンで使用できます。管理者を対象とする無料バージョンでは使用できません。 Azure Multi-Factor Authentication の完全なバージョンを入手する方法の詳細については、[Azure Multi-Factor Authentication](multi-factor-authentication.md) に関するページを参照してください。

> [!NOTE]
> MFA の信頼できる IP と条件付きアクセスのネームド ロケーションは、IPV4 アドレスでのみ機能します。

組織がオンプレミスのアプリケーションに MFA を提供するために NPS 拡張機能をデプロイしている場合は、認証が試行されるとき、常にソース IP アドレスが NPS サーバーとして表示されます。

| Azure AD テナントの種類 | 信頼できる IP 機能のオプション |
|:--- |:--- |
| マネージド |**[Specific range of IP addresses]\(特定範囲の IP アドレス\)** : 管理者は、会社のイントラネットからサインインするユーザーの 2 段階認証をバイパスできる IP アドレスの範囲を指定します。|
| フェデレーション |**すべてのフェデレーション ユーザー**: 組織の内部からサインインするすべてのフェデレーション ユーザーは、2 段階認証をバイパスできます。 ユーザーは、Active Directory フェデレーション サービス (AD FS) によって発行される要求を使用して認証をバイパスします。<br/>**[Specific range of IP addresses]\(特定範囲の IP アドレス\)** : 管理者は、会社のイントラネットからサインインするユーザーの 2 段階認証をバイパスできる IP アドレスの範囲を指定します。 |

信頼できる IP バイパスは会社のイントラネット内からのみ機能します。 **[すべてのフェデレーション ユーザー]** オプションを選択したときに、ユーザーが会社のイントラネットの外部からサインインした場合は、2 段階認証を使用してそのユーザーを認証する必要があります。 ユーザーが AD FS 要求を提示している場合でもプロセスは同じです。 

### <a name="end-user-experience-inside-of-corpnet"></a>企業ネットワーク内のエンドユーザー エクスペリエンス

信頼できる IP 機能が無効な場合、ブラウザー フローでは 2 段階認証が必要です。 以前のリッチ クライアント アプリケーションではアプリ パスワードが必要です。

信頼できる IP 機能が有効な場合、ブラウザー フローで 2 段階認証は*不要*です。 以前のリッチ クライアント アプリケーションではアプリ パスワードは*不要*です (ユーザーがアプリ パスワードを作成していないことを条件とします)。 アプリ パスワードを使用している場合は、引き続きパスワードが必要です。 

### <a name="end-user-experience-outside-corpnet"></a>企業ネットワークの外部のエンドユーザー エクスペリエンス

信頼できる IP 機能が有効であるかどうかに関係なく、ブラウザー フローでは 2 段階認証が必要です。 以前のリッチ クライアント アプリケーションではアプリ パスワードが必要です。

### <a name="enable-named-locations-by-using-conditional-access"></a>条件付きアクセスを使用したネームド ロケーションの有効化

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、 **[Azure Active Directory]**  >  **[条件付きアクセス]**  >  **[ネームド ロケーション]** を選択します。
3. **[新しい場所]** を選択します。
4. 場所の名前を入力します。
5. **[信頼できる場所としてマークする]** を選択します。
6. IP 範囲を CIDR 表記 (**192.168.1.1/24** など) で入力します。
7. **作成** を選択します。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>条件付きアクセスを使用した信頼できる IP 機能の有効化

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、 **[Azure Active Directory]**  >  **[条件付きアクセス]**  >  **[ネームド ロケーション]** を選択します。
3. **[MFA の信頼できる IP の構成]** を選択します。
4. **[サービス設定]** ページの **[信頼できる IP]** で、次の 2 つのオプションのいずれかを選択します。

   * **[For requests from federated users originating from my intranet]\(イントラネット内のフェデレーション ユーザーから送信された要求\)** : このオプションを選択するには、チェック ボックスをオンにします。 企業ネットワークからサインインするすべてのフェデレーション ユーザーは、AD FS によって発行される要求を使用して、2 段階認証をバイパスします。 イントラネットの要求を適切なトラフィックに追加する規則が AD FS にあることを確認します。 規則が存在しない場合は、AD FS で次の規則を作成します。

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **[For requests from a specific range of public IPs]\(特定範囲の IP から送信された要求\)** : このオプションを選択するには、CIDR 表記を使用して、テキスト ボックスに IP アドレスを入力します。
      * xxx.xxx.xxx.1 から xxx.xxx.xxx.254 の範囲の IP アドレスの場合は、**xxx.xxx.xxx.0/24** などの表記を使用します。
      * 単一の IP アドレスの場合は、**xxx.xxx.xxx.xxx/32** などの表記を使用します。
      * 最大で 50 の IP アドレス範囲を入力します。 これらの IP アドレスからサインインしているユーザーは、2 段階認証をバイパスします。

5. **[保存]** を選択します。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>サービス設定を使用した信頼できる IP 機能の有効化

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側の **[Azure Active Directory]**  >  **[ユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. **[サービス設定]** ページの **[信頼できる IP]** で、次の 2 つのオプションのいずれか (または両方) を選択します。

   * **[For requests from federated users on my intranet]\(イントラネット内のフェデレーション ユーザーから送信された要求\)** :このオプションを選択するには、チェック ボックスをオンにします。 企業ネットワークからサインインするすべてのフェデレーション ユーザーは、AD FS によって発行される要求を使用して、2 段階認証をバイパスします。 イントラネットの要求を適切なトラフィックに追加する規則が AD FS にあることを確認します。 規則が存在しない場合は、AD FS で次の規則を作成します。

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **[For requests from a specified range of IP address subnets]\(特定範囲の IP アドレスのサブネットから送信された要求\)** : このオプションを選択するには、CIDR 表記を使用して、テキスト ボックスに IP アドレスを入力します。
      * xxx.xxx.xxx.1 から xxx.xxx.xxx.254 の範囲の IP アドレスの場合は、**xxx.xxx.xxx.0/24** などの表記を使用します。
      * 単一の IP アドレスの場合は、**xxx.xxx.xxx.xxx/32** などの表記を使用します。
      * 最大で 50 の IP アドレス範囲を入力します。 これらの IP アドレスからサインインしているユーザーは、2 段階認証をバイパスします。

6. **[保存]** を選択します。

## <a name="verification-methods"></a>検証方法

管理者は、ユーザーが使用できる検証方法を選択できます。 次の表で方法の概要を説明します。

ユーザーは、自分のアカウントを Azure Multi-Factor Authentication 用に登録するときに、有効になっているオプションから使用する検証方法を選択します。 登録プロセスのガイダンスについては、「[アカウントへの 2 段階認証の設定](../user-help/multi-factor-authentication-end-user-first-time.md)」を参照してください。

| Method | 説明 |
|:--- |:--- |
| 電話の呼び出し |自動音声通話を行います。 ユーザーは、呼び出しに応答し、電話のキーパッドの # を押して認証を行います。 電話番号は、オンプレミスの Active Directory には同期されません。 |
| 電話へのテキスト メッセージ |確認コードを含むテキスト メッセージを送信します。 ユーザーは、この確認コードをサインイン インターフェイスに入力するように求められます。 このプロセスを一方向の SMS といいます。 双方向の SMS は、ユーザーが特定のコードを返信する必要があることを意味します。 双方向の SMS は非推奨となり、2018 年 11 月 14 日以降はサポートされなくなります。 双方向 SMS 用に構成されているユーザーは、その時点で _電話の呼び出し_ 認証に自動的に切り替わります。|
| モバイル アプリでの通知 |電話または登録されたデバイスにプッシュ通知を送信します。 ユーザーは通知を表示し、 **[確認]** を選択して認証を完了します。 Microsoft Authenticator アプリは、[Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](https://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。 |
| モバイル アプリからの確認コードまたはハードウェア トークン |Microsoft Authenticator アプリは、30 秒ごとに新しい OATH 確認コードを生成します。 ユーザーは確認コードをサインイン インターフェイスに入力します。 Microsoft Authenticator アプリは、[Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071)、[Android](https://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](https://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。 |

### <a name="enable-and-disable-verification-methods"></a>検証方法を有効または無効にする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、 **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. **[サービスの設定]** ページの **[検証オプション]** で、ユーザーに提供する方法を選択/選択解除します。
6. **[Save]** をクリックします。

認証方法の使用の詳細については、「[認証方法とは](concept-authentication-methods.md)」を参照してください。

## <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication の記憶

ユーザーが信頼済みデバイスとブラウザーに対する _Multi-Factor Authentication の記憶_ 機能は、すべての Multi-Factor Authentication ユーザー向けの無料の機能です。 ユーザーは、Multi-Factor Authentication を使用して正常にデバイスにサインインした後、一定の日数の間、後続の確認をバイパスすることができます。 この機能により、ユーザーが同じデバイスで 2 段階認証を実行する必要がある回数を最小限に抑えることができるので、使いやすさを強化できます。

>[!IMPORTANT]
>アカウントまたはデバイスが侵害された場合、信頼済みデバイスに対する Multi-Factor Authentication の記憶はセキュリティに影響する可能性があります。 企業アカウントが侵害された場合や、信頼済みデバイスを紛失したり盗難に遭ったりした場合は、[Multi-Factor Authentication をすべてのデバイスで復元](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user)する必要があります。
>
>復元操作により、信頼された状態がすべてのデバイスから失われ、ユーザーは 2 段階認証を再度実行する必要があります。 「[2 段階認証設定の管理](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)」の手順で各自のデバイスの Multi-Factor Authentication を復元するようユーザーに指示することもできます。
>

### <a name="how-the-feature-works"></a>機能のしくみ

Multi-Factor Authentication の記憶機能では、ブラウザーでユーザーがサインイン時に **[今後 X 日間はこのメッセージを表示しない]** オプションを選択したときに永続的な Cookie を設定します。 Cookie の有効期限が切れるまでは、同じブラウザーからユーザーが再度 Multi-Factor Authentication を求められることはありません。 そのユーザーが同じデバイスで異なるブラウザーを開くか、Cookie をクリアした場合は、再度、認証が求められます。

ブラウザーではないアプリケーションでは、アプリで先進認証がサポートされているかどうかに関係なく、 **[今後 X 日間はこのメッセージを表示しない]** オプションは表示されません。 これらのアプリでは、新しいアクセス トークンが 1 時間おきに支給される _更新トークン_ が使用されます。 更新トークンの検証時に、前回の 2 段階認証が設定されている日数内に実行されたことが Azure AD によって確認されます。

この機能を使用すると、Web アプリでの認証回数 (通常は毎回プロンプトが表示される) が減ります。 この機能では、先進認証クライアントの認証の回数 (通常は 90 日ごとにプロンプトが表示される) が増えます。 条件付きアクセス ポリシーと組み合わされた場合の認証数を増やすこともできます。

>[!IMPORTANT]
>ユーザーが Azure Multi-Factor Authentication Server またはサードパーティの多要素認証ソリューションを介して AD FS の 2 段階認証を行う場合、 **[Multi-Factor Authentication を記憶する]** 機能は、AD FS の **[サインインしたままにする]** 機能とは互換性がありません。
>
>ユーザーが AD FS の **[サインインしたままにする]** を選択し、さらに Multi-Factor Authentication に対してデバイスを信頼済みとしてマークした場合、**Multi-Factor Authentication を記憶する** 日数が過ぎるとユーザーは自動的に確認されません。 Azure AD は新たに 2 段階認証を行うよう要求しますが、AD FS は、2 段階認証を再実行するのではなく、元の Multi-Factor Authentication 要求と日付を含むトークンを返します。 **その結果、Azure AD と AD FS との間で本人確認がループ状態に陥ります。**
>

### <a name="enable-remember-multi-factor-authentication"></a>Multi-Factor Authentication の記憶を有効にする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、 **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。
4. [Multi-Factor Authentication] の下で **[サービス設定]** を選択します。
5. **[サービス設定]** ページの **[manage remember multi-factor authentication]\(Multi-Factor Authentication の記憶の管理\)** で、 **[信頼済みデバイスでユーザーが多要素認証を記憶できるようにする]** オプションを選択します。
6. 信頼済みデバイスで 2 段階認証のバイパスを許可する日数を設定します。 既定値は 14 日です。
7. **[保存]** を選択します。

### <a name="mark-a-device-as-trusted"></a>デバイスを信頼済みとマークする

Multi-Factor Authentication を記憶する機能を有効にすると、ユーザーは、サインイン時に **[今後このメッセージを表示しない]** を選択することで、デバイスを信頼済みとしてマークできます。

## <a name="next-steps"></a>次の手順

[Azure AD サインイン ページのブランドを変更する](../fundamentals/customize-branding.md)
