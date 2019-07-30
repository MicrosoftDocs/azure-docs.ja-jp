---
title: Azure Active Directory の Enterprise State Roaming の設定のトラブルシューティング | Microsoft Docs
description: 設定とアプリ データの同期に関する質問とその答えを IT 管理者向けに紹介しています。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cceae17b06e8b631dd530b0408008a8222bccbf
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481854"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Azure Active Directory の Enterprise State Roaming の設定のトラブルシューティング

このトピックでは、Enterprise State Roaming の問題のトラブルシューティングと診断の方法について説明するとともに、既知の問題の一覧を示します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="preliminary-steps-for-troubleshooting"></a>トラブルシューティングのための準備作業 

トラブルシューティングを開始する前に、ユーザーとデバイスが適切に設定されており、デバイスとユーザーが Enterprise State Roaming のすべての要件に適合していることを確認します。 

1. 最新の更新プログラムがインストールされた Windows 10 であり、デバイスには最小バージョン 1511 (OS ビルド 10586 以降) がインストールされている。 
1. デバイスは Azure AD またはハイブリッド Azure AD に参加しています。 詳細については、[デバイスを Azure AD で管理する方法](overview.md)に関するページをご覧ください。
1. [Enterprise State Roaming の有効化](enterprise-state-roaming-enable.md)に関するページで説明されているとおりに、Azure AD のテナントで **Enterprise State Roaming** が有効になっていることを確認します。 すべてのユーザーまたは選択したグループのユーザーにだけローミングを有効にできます。
1. ユーザーには Azure Active Directory Premium のライセンスをあらかじめ割り当てておく必要があります。  
1. デバイスを再起動し、もう一度サインインして Enterprise State Roaming の機能にアクセスする必要があります。

## <a name="information-to-include-when-you-need-help"></a>ヘルプが必要な場合に含める情報
以下のガイダンスを使用しても問題を解決できない場合は、サポート エンジニアにお問い合わせください。 サポートにお問い合わせいただく際は、次の情報をお知らせください。

* **エラーの概要**:ユーザーにエラー メッセージは表示されましたか。 エラー メッセージが表示されなかった場合は、気が付いた予期しない動作について詳しく説明してください。 どの機能で同期が有効になっていますか。また、同期することが求められるのはどのような機能ですか。 同期しないのは複数の機能ですか。または 1 つの機能ですか。
* **影響を受けるユーザー** – 同期が機能、または機能しないのは、1 人のユーザーですか。それとも複数のユーザーですか。 ユーザー 1 人当たりに関係するデバイスは何台ですか。 すべてのデバイスが同期しませんか。それとも同期するデバイスもあれば、同期しないデバイスもありますか。
* **ユーザーに関する情報** – ユーザーはどの ID を使用してデバイスにログインしていますか。 どのような方法でデバイスにログインしていますか。 ユーザーは、同期が許可されている選択されたセキュリティ グループの一部ですか。 
* **デバイスに関する情報** – デバイスは Azure AD に参加していますか。またはドメインに参加していますか。 どのビルドがデバイスにインストールされていますか。 最新の更新プログラムは何ですか。
* **日付 / 時刻 / タイム ゾーン** – エラーが表示された正確な日時を教えてください (タイム ゾーンを含む)。

これらの情報を含めることで、迅速に問題を解決するのに役立ちます。

## <a name="troubleshooting-and-diagnosing-issues"></a>問題のトラブルシューティングと診断
このセクションでは、Enterprise State Roaming に関連した問題のトラブルシューティングと診断の方法に関する推奨事項を示します。

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>同期、および "設定の同期" の設定ページを確認する 

1. Enterprise State Roaming を許可するよう構成されたドメインに Windows 10 PC を参加させたら、職場アカウントを使ってサインオンします。 **[設定]**  >  **[アカウント]**  >  **[設定の同期]** に移動して、同期と個々の設定がオンになっており、設定ページの最上部に、職場アカウントを使って同期していることが示されていることを確認します。 **[設定]**  >  **[アカウント]**  >  **[Your Info (ユーザー情報)]** で、同じアカウントがログイン アカウントとしても使われていることを確認します。 
1. 同期元のコンピューターで変更 (タスクバーを画面の右端や上部に移動するなど) を行い、複数のコンピューター間で同期が機能することを確認します。 5 分以内に、2 つ目のコンピューターに変更が反映されることを確認します。 

   * 画面をロックまたはロック解除 (Win + L) すると、同期をトリガーできる場合があります。
   * Enterprise State Roaming はコンピューター アカウントではなくユーザー アカウントに関連付けられているため、同期が機能するには、両方の PC で同じアカウントでサインインする必要があります。

**潜在的な問題**: **[設定]** ページでコントロールを使用できず、"Windows の一部の機能は、Microsoft アカウントまたは職場アカウントを使用している場合にのみ利用できます" というメッセージが表示されます。 この問題は、デバイスをドメインに参加するよう設定して Azure AD に登録したものの、Azure AD に正常に認証されていない場合に発生することがあります。 考えられる原因としては、デバイス ポリシーを適用する必要があるものの、アプリケーションが非同期的に動作して、数時間の遅延が発生した可能性があります。 

### <a name="verify-the-device-registration-status"></a>デバイスの登録状態を確認する

Enterprise State Roaming では、デバイスを Azure AD に登録する必要があります。 Enterprise State Roaming に限ったことではありませんが、次の手順を実行すると Windows 10 クライアントが登録されていることを確認できるほか、拇印、Azure AD の設定 URL、NGC の状態、およびその他の情報を確認することができます。

1. 管理者特権を使用せずにコマンド プロンプトを開きます。 これを Windows で行う場合は、[ファイル名を指定して実行] \(Win + R) を開き、「cmd」と入力して開きます。
1. コマンド プロンプトが開いたら、「*dsregcmd.exe /status*」を入力します。
1. 期待する出力を得るには、**AzureAdJoined** フィールド値を "YES"、**WamDefaultSet** フィールド値を "YES"、**WamDefaultGUID** フィールド値を末尾が "(AzureAd)" である GUID にする必要があります。

**潜在的な問題**:**WamDefaultSet** と **AzureAdJoined** の両方のフィールド値が "NO" になっており、デバイスはドメインに参加して Azure AD に登録されていますが、同期しません。このような場合は、デバイスにポリシーが適用されるまで待機する必要があるか、Azure AD に接続するときにデバイスの認証に失敗したと考えられます。 ポリシーが適用されるまでには数時間かかる場合があります。 その他のトラブルシューティング手順としては、サインアウトしてからサインインし直してもう一度自動登録をしてみるか、タスク スケジューラでタスクを起動することなどが挙げられます。 場合によっては、管理者特権で開いたコマンド プロンプト画面で "*dsregcmd.exe /leave*" を実行して再起動し、登録し直すことが問題の解決に役立つことがあります。

**潜在的な問題**:**SettingsUrl** のフィールドが空で、デバイスが同期しません。最後にデバイスにログインしたのが、Azure Active Directory ポータルで Enterprise State Roaming が有効になる前だった可能性があります。 デバイスを再起動してログインします。 必要に応じて、ポータルの **[Azure Active Directory]**  >  **[デバイス]**  >  **[Enterprise State Roaming]** に移動し、 **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** を無効にしてから再度有効にするよう、IT 管理者に依頼してください。 その後、デバイスを再起動してログインします。 問題が解決されない場合、デバイスの証明書が不適切であれば、**SettingsUrl** が空になることがあります。 この場合、管理者特権で開いたコマンド プロンプト画面で "*dsregcmd.exe /leave*" を実行して再起動し、登録し直すと問題の解決に役立つことがあります。

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming と Multi-Factor Authentication 

Azure Multi-Factor Authentication が構成されている場合、特定の条件下で Enterprise State Roaming がデータの同期に失敗する可能性があります。 このような場合の詳細については、サポート ドキュメント [KB3193683](https://support.microsoft.com/kb/3193683) を参照してください。 

**潜在的な問題**:お使いのデバイスが Azure Active Directory ポータルで Multi-Factor Authentication を要求するように構成されている場合、パスワードを使用して Windows 10 デバイスのサインインしている状態で設定の同期が失敗することがあります。 このタイプの Multi-Factor Authentication 構成は、Azure 管理者アカウントの保護を意図したものです。 管理者ユーザーは、Microsoft Passport for Work の PIN を使用するか、他の Azure サービス (Office 365 など) にアクセスしている状態で Multi-Factor Authentication を行い、Windows 10 デバイスにサインインすることで同期を実行できる場合があります。

**潜在的な問題**:管理者が Active Directory フェデレーション サービス Multi-Factor Authentication 条件付きアクセス ポリシーを構成し、デバイスのアクセス トークンの有効期限が切れている場合、同期が失敗することがあります。 一度サインアウトしてから Microsoft Passport for Work の PIN を使用してサインインし直すか、または他の Azure サービス (Office 365 など) にアクセスしている状態で Multi-Factor Authentication を行ってください。

### <a name="event-viewer"></a>イベント ビューアー

高度なトラブルシューティング方法として、イベント ビューアーを使って特定のエラーの検出ができるものがあります。 これらについては、下の表に記載します。 [イベント ビューアー] > [アプリケーションとサービス ログ] > **[Microsoft]**  >  **[Windows]**  >  **[SettingSync-Azure]** でイベントを表示します。同期の ID 関連の問題については、 **[Microsoft]**  >  **[Windows]**  >  **[AAD]** に移動します。

## <a name="known-issues"></a>既知の問題

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>MDM ソフトウェアを使ってアプリがサイドロードされたデバイスで、同期が機能しません

Windows 10 Anniversary Update (バージョン 1607) を実行するデバイスに影響します。 イベント ビューアーの SettingSync-Azure ログに、イベント ID 6013 (エラー 80070259) が頻繁に表示されます。

**推奨される操作**  
Windows 10 バージョン 1607 のクライアントに、2016 年 8 月 23 日にリリースされた累積的な更新プログラム ([KB3176934](https://support.microsoft.com/kb/3176934) OS ビルド 14393.82) がインストールされていることを確認します。 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer のお気に入りが同期されません

Windows 10 November Update (バージョン 1511) を実行するデバイスに影響します。

**推奨される操作**  
Windows 10 バージョン 1511 のクライアントに、2016 年 7 月にリリースされた累積的な更新プログラム ([KB3172985](https://support.microsoft.com/kb/3172985) OS ビルド 10586.494) がインストールされていることを確認します。

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>テーマのほかに、Windows Information Protection で保護されたデータも同期されません 

データの漏えいを防ぐため、[Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) で保護されたデータは、Enterprise State Roaming を通じて Windows 10 Anniversary Update を使用するデバイスに同期されることはありません。

**推奨される操作**  
なし。 この問題は今後、Windows の更新プログラムで解決される可能性があります。

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>日付、時刻、地域の設定が、ドメインに参加したデバイスで同期されません 
  
ドメインに参加したデバイスでは、日付、時刻、地域の設定は同期されず、自動時刻が適用されます。 自動時刻を使用すると、他の日付、時刻、地域の設定がオーバーライドされ、これらの設定が同期されない場合があります。 

**推奨される操作**  
なし。 

---

### <a name="uac-prompts-when-syncing-passwords"></a>パスワードの同期時に UAC プロンプトが表示されます

パスワードを同期するよう構成したワイヤレス NIC が搭載された、Windows 10 November Update (バージョン 1511) を実行するデバイスに影響します。

**推奨される操作**  
Windows 10 バージョン 1511 クライアントに、累積的な更新プログラム ([KB3140743](https://support.microsoft.com/kb/3140743) OS ビルド 10586.494) がインストールされていることを確認します。

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>スマート カードをログインに使用するデバイスで、同期が機能しません

スマート カードまたは仮想スマート カードを使用して Windows デバイスにサインインしようとすると、設定の同期機能が停止します。     

**推奨される操作**  
なし。 この問題は今後、Windows の更新プログラムで解決される可能性があります。

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>ドメインに参加したデバイスが企業ネットワークから離れると、同期されません     

ドメインに参加して Azure AD に登録されたデバイスが長時間企業ネットワークから離れると、同期が失敗して、ドメイン認証が完了しない場合があります。

**推奨される操作**  
デバイスを企業ネットワークに接続し、同期を再開できるようにします。

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD 参加済みデバイスが同期されず、ユーザーのユーザー プリンシパル名に大文字と小文字が混在しています。

ユーザーの UPN に大文字と小文字が混在していて (例: username ではなく UserName )、そのユーザーが Windows 10 ビルド 10586 から 14393 にアップグレードした Azure AD 参加済みデバイスを使用している場合、ユーザーのデバイスは同期に失敗します。 

**推奨される操作**  
ユーザーはデバイスをクラウドから離し、デバイスを再度クラウドに参加させる必要があります。 これを行うには、ローカル管理者ユーザーとしてログインし、 **[設定]**  >  **[システム]**  >  **[バージョン情報]** に移動して [職場または学校からの管理または切断] を選択することで、デバイスの参加を解除します。 後述のファイルをクリーンアップし、 **[設定]**  >  **[システム]**  >  **[バージョン情報]** で [職場または学校への接続] を選択して、デバイスを再度 Azure AD に参加させます。 デバイスを Azure Active Directory に参加させ、フローを完了します。

このクリーンアップの手順では、以下のファイルがクリーンアップされます。
- 次の場所にある Settings.dat `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- 次のフォルダーの下にあるすべてのファイル `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>イベント ID 6065:80070533 このアカウントは現在無効に設定されているため、このユーザーはサインインできません  

ユーザーの資格情報の期限が切れている場合、SettingSync/デバッグ ログのイベント ビューアーにこのエラーが表示される場合があります。 しかも、この状況は、テナントで AzureRMS が自動的にプロビジョニングされなかったときに起こる可能性があります。 

**推奨される操作**  
最初のケースでは、資格情報を更新して、新しい資格情報でデバイスにログインします。 AzureRMS の問題を解決するには、[KB3193791](https://support.microsoft.com/kb/3193791) に示された手順を実行します。 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>イベント ID 1098:エラー:0xCAA5001C トークン ブローカーの操作が失敗しました  

AAD/操作ログのイベント ビューアーに、イベント 1104 とともに、"AAD クラウドの AP プラグイン呼び出しでトークンの取得がエラーを返しました:0xC000005F" というエラーが表示されることがあります。 アクセス許可や所有権の属性が欠落している場合にこの問題が起こります。  

**推奨される操作**  
[KB3196528](https://support.microsoft.com/kb/3196528) に示された手順を実行します。  

## <a name="next-steps"></a>次の手順

概要については、「[Enterprise State Roaming の概要](enterprise-state-roaming-overview.md)」を参照してください。
