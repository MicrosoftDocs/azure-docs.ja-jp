---
title: Azure Active Directory デバイス管理の FAQ | Microsoft Docs
description: Azure Active Directory デバイス管理の FAQ。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8802f9e5c84078725675d961ada7f8183c91c0ec
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481750"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory デバイス管理の FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q:最近、デバイスを登録しました。 Azure portal のユーザー情報にデバイスが表示されないのはなぜですか? または、ハイブリッド Azure Active Directory (Azure AD) 参加済みデバイスのデバイス所有者が N/A とマークされるのはなぜですか?

**A:** ハイブリッド Azure AD 参加済みの Windows 10 デバイスは、 **[ユーザー デバイス]** には表示されません。
Azure portal の **[すべてのデバイス]** ビューを使用してください。 PowerShell の [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) コマンドレットを使用することもできます。

**[ユーザー デバイス]** には、次のデバイスだけが表示されます。

- ハイブリッド Azure AD 参加済みではないすべての個人用デバイス。 
- Windows 10 または Windows Server 2016 以外のすべてのデバイス。
- Windows 以外のすべてのデバイス。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q:クライアントのデバイスの登録状態はどうすればわかりますか?

**A:** Azure portal で **[すべてのデバイス]** に移動します。 デバイス ID を使用してデバイスを検索します。 [結合の種類] 列の値を確認します。 場合によっては、デバイスがリセットまたは再イメージ化されていることがあります。 そのため、デバイスでもデバイス登録状態を確認することが不可欠です。

- Windows 10 および Windows Server 2016 以降のデバイスの場合は、`dsregcmd.exe /status` を実行します。
- ダウンレベルの OS バージョンの場合は、`%programFiles%\Microsoft Workplace Join\autoworkplace.exe` を実行します。

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q:Azure portal の [ユーザー情報] にデバイス レコードが表示され、 デバイスに状態が登録済みと表示されます。 条件付きアクセスを使用するように正しく設定されていますか?

**A:** **deviceID** に表示されたデバイスの参加状態は、Azure AD での状態と一致しており、条件付きアクセスの評価基準を満たしている必要があります。 詳細については、[条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する方法](../conditional-access/require-managed-devices.md)に関するページを参照してください。

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>Q:Azure portal で、または Windows PowerShell を使用してデバイスを削除しました。 しかし、デバイスのローカル状態にはまだ登録済みと表示されます。

**A:** この操作は設計によるものです。 デバイスは、クラウドのリソースにアクセスできません。 

再登録する場合は、デバイスで手動操作を実行する必要があります。 

オンプレミスの Active Directory ドメインに参加済みの Windows 10 および Windows Server 2016 の参加状態をクリアするには、次の手順を実行します。

1. 管理者としてコマンド プロンプトを開きます。
1. 「 `dsregcmd.exe /debug /leave` 」を入力します。
1. サインアウトしてからサインインして、デバイスを Azure AD に再登録するスケジュール済みタスクをトリガーします。 

オンプレミスの Active Directory ドメインに参加済みのダウンレベルの Windows OS バージョンの場合は、次の手順を実行します。

1. 管理者としてコマンド プロンプトを開きます。
1. 「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` 」を入力します。
1. 「 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` 」を入力します。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q:Azure portal に重複するデバイス エントリが表示されるのはなぜですか?

**A:**

- Windows 10 および Windows Server 2016 の場合、同じデバイスの参加を解除し、再度参加させる操作を繰り返すと、エントリの重複が発生することがあります。 
- **[職場または学校アカウントを追加]** を使用する各 Windows ユーザーは、同じデバイス名で新しいデバイス レコードを作成します。
- オンプレミスの Azure Directory ドメインに参加しているダウンレベルの Windows OS バージョンでは、自動登録によって、デバイスにサインインするドメイン ユーザーごとに、同じデバイス名で新しいデバイス レコードが作成されます。 
- Azure AD 参加済みコンピューターをワイプして再インストールし、同じ名前で再度参加させると、同じデバイス名で別のレコードとして表示されます。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q:Azure AD での Windows 10 デバイス登録では、FIPS モードの TPM はサポートされますか?

**A:** いいえ。現在、Windows 10 デバイス登録では、すべてのデバイスの状態 (Hybrid Azure AD 参加、Azure AD 参加、および Azure AD 登録済み) について、FIPS モードの TPM はサポートされません。 Azure AD に正常に参加または登録するには、それらのデバイスで FIPS モードを無効にする必要があります。

---

**Q:Azure portal で無効にしたデバイスからユーザーがリソースに引き続きアクセスできるのはなぜですか?**

**A:** 取り消しが適用されるまで最大 1 時間かかります。

>[!NOTE] 
>登録済みデバイスの場合は、ユーザーがリソースにアクセスできないように、デバイスのワイプを実行することをお勧めします。 詳細については、「[デバイス登録とは](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)」を参照してください。 

---

## <a name="azure-ad-join-faq"></a>Azure AD Join の FAQ

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q:デバイス上の Azure AD 参加済みデバイスをローカルで参加解除するにはどうすればよいですか?

**A:** 
- ハイブリッド Azure AD 参加済みデバイスの場合は、必ず自動登録をオフにしてください。 その後、スケジュールされたタスクはデバイスを再登録しません。 次に、管理者としてコマンド プロンプトを開き、「`dsregcmd.exe /debug /leave`」と入力します。 または、このコマンドを複数のデバイスに対するスクリプトとして実行し、一括で参加を解除します。
- 純粋な Azure AD 参加済みデバイスの場合、オフラインのローカル管理者アカウントを持っているか、作成する必要があります。 Azure AD ユーザーの資格情報ではサインインできません。 次に、 **[設定]**  >  **[アカウント]**  >  **[職場または学校にアクセスする]** に移動します。 アカウントを選択し、 **[切断]** を選択します。 画面の指示に従い、入力を求められたらローカル管理者の資格情報を入力します。 デバイスを再起動して、参加の解除プロセスを完了します。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q:ユーザーは Azure AD で削除されたか無効にされた Azure AD 参加済みデバイスにサインインできますか?

**A:** はい。 Windows には、以前にサインインしたユーザーがネットワークに接続していなくてもすばやくデスクトップにアクセスできるようにする、キャッシュされたユーザー名とパスワードの機能があります。 

Azure AD でデバイスが削除または無効化されても、Windows デバイスにはわかりません。 そのため、以前にサインインしたユーザーは、引き続きキャッシュされたユーザー名とパスワードを使ってデスクトップにアクセスします。 しかし、デバイスは削除または無効化されているため、ユーザーはデバイスベースの条件付きアクセスによって保護されているリソースにアクセスできません。 

以前にサインインしたことのないユーザーは、デバイスにはアクセスできません。 そのようなユーザーには、キャッシュされたユーザー名とパスワードが有効になっていません。 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q:無効にされたか削除されたユーザーは、Azure AD 参加済みデバイスにサインインできますか?

**A:** はい、ただし限られた期間だけです。 Azure AD でユーザーが削除または無効化されても、Windows デバイスはそのことをすぐには認識しません。 そのため、以前にサインインしたユーザーは、キャッシュされたユーザー名とパスワードを使ってデスクトップにアクセスできます。 

通常、デバイスが認識するユーザーの状態は、4 時間以内の状態です。 その後は、Windows がそれらのユーザーのデスクトップへのアクセスをブロックします。 ユーザーが Azure AD で削除または無効化されると、すべてのトークンが取り消されます。 そのため、ユーザーはリソースにアクセスできなくなります。 

削除または無効化されたユーザーのうち、以前にサインインしたことのないユーザーは、デバイスにはアクセスできません。 そのようなユーザーには、キャッシュされたユーザー名とパスワードが有効になっていません。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q:UPN の変更後、Azure AD 参加済みデバイスでユーザーに問題が生じるのはなぜですか?

**A:** 現在、Azure AD 参加済みデバイスでの UPN の変更は完全にはサポートされていません。 そのため、UPN の変更後に Azure AD での認証が失敗します。 その結果、ユーザーのデバイスで SSO と条件付きアクセスの問題が生じます。 現時点では、この問題を解決するために、ユーザーは新しい UPN を使用して [他のユーザー] タイルから Windows にサインインする必要があります。 現在、この問題の対策に取り組んでいます。 なお、この問題は Windows Hello for Business でサインインしているユーザーには影響しません。 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q:ユーザーが Azure AD 参加済みデバイスからプリンターを検索できません。 どうすればそれらのデバイスからの印刷を有効にできますか?

**A:** Azure AD 参加済みデバイス用にプリンターをデプロイするには、[事前認証を使用した Windows Server ハイブリッド クラウド印刷のデプロイ](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)に関するページを参照してください。 ハイブリッド クラウド印刷には、Windows Server がオンプレミスで必要です。 現在、クラウドベースの印刷サービスは利用できません。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q:Azure AD に参加しているリモート デバイスに接続する方法はありますか?

**A:** 「[Azure Active Directory に参加しているリモート PC への接続](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)」をご覧ください。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q:ユーザーに "*ここからアクセスすることはできません*" というメッセージが表示されるのはなぜですか?

**A:** 特定の条件付きアクセス規則を、特定のデバイスの状態を必要とするように構成しましたか? デバイスが条件を満たしていない場合は、ユーザーがブロックされて、そのメッセージが表示されます。 条件付きアクセス ポリシー規則を評価してください。 このメッセージが表示されないようにするには、デバイスが条件を満たしていることを確認してください。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q:一部のユーザーに、Azure AD 参加済みデバイスで Azure Multi-Factor Authentication のプロンプトが表示されないのはなぜですか?

**A:** ユーザーが Multi-Factor Authentication を使用してデバイスを Azure AD に参加または登録している可能性があります。 それにより、デバイス自体がそのユーザーにとっての信頼された 2 つ目の要素になります。 同じユーザーがデバイスにサインインしてアプリケーションにアクセスするときは常に、Azure AD はデバイスを 2 つ目の要素と見なします。 これによりそのユーザーは、Multi-Factor Authentication の追加のプロンプトが表示されることなく、アプリケーションにシームレスにアクセスできます。 

この動作は、

- Azure AD 参加済みデバイスと Azure AD 登録済みデバイスに適用できますが、ハイブリッド Azure AD 参加済みデバイスには適用できません。
- そのデバイスにサインインするその他のユーザーには適用されません。 そのため、そのデバイスにアクセスする他のすべてのユーザーは、Multi-Factor Authentication で必要な情報を取得します。 その後、Multi-Factor Authentication を必要とするアプリケーションにアクセスできるようになります。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q:Azure AD に参加したばかりのデバイスに対して、"*ユーザー名またはパスワードが正しくありません*" というメッセージが表示されるのはなぜですか?

**A:** このシナリオの一般的な理由は次のとおりです。

- ユーザーの資格情報が有効ではなくなっています。
- コンピューターが Azure Active Directory と通信できません。 ネットワーク接続の問題を確認してください。
- フェデレーション サインインでは、有効になっていてアクセスできる WS-Trust エンドポイントがフェデレーション サーバーでサポートされている必要があります。 
- パススルー認証が有効になっています。 そのため、サインインするときに一時パスワードを変更する必要があります。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q:PC を Azure AD に参加させようとしたときに *[申し訳ありません。エラーが発生しました]* ダイアログが表示されるのはなぜですか?

**A:** このエラーは、Intune への Azure Active Directory の登録を設定するときに発生します。 Azure AD への参加を試みているユーザーに、適切な Intune ライセンスが割り当てられていることを確認してください。 詳細については、「[Windows デバイスの登録をセットアップする](https://docs.microsoft.com/intune/windows-enroll)」をご覧ください。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q:エラー情報が表示されなかったにもかかわらず、PC を Azure AD に参加させることができなかったのはなぜですか?

**A:** ユーザーがローカルのあらかじめ登録された Administrator アカウントを使用してデバイスにサインインしていることが原因と考えられます。 Azure Active Directory Join を使用してセットアップを完了する前に、別のローカル アカウントを作成してください。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q: Windows 10 デバイスに存在する MS-Organization-P2P-Access 証明書とは何ですか?

**A:** MS-Organization-P2P-Access 証明書は、Azure AD によって Azure AD 参加済みデバイスとハイブリッド Azure AD 参加済みデバイスの両方に発行されます。 これらの証明書を使用して、リモート デスクトップのシナリオで同じテナント内のデバイス間の信頼を可能にします。 1 つの証明書はデバイスに発行され、もう 1 つはユーザーに発行されます。 デバイス証明書は `Local Computer\Personal\Certificates` 内に存在し、1 日間有効です。 この証明書は、デバイスが Azure AD でアクティブなままの場合、(新しい証明書を発行することで) 更新されます。 ユーザー証明書は `Current User\Personal\Certificates` 内に存在します。この証明書も 1 日間有効ですが、ユーザーが別の Azure AD 参加済みデバイスへのリモート デスクトップ セッションを試みたときに、オンデマンドで発行されます。 これは期限切れ時に更新されません。 これらの証明書は、両方とも、`Local Computer\AAD Token Issuer\Certificates` 内に存在する MS-Organization-P2P-Access を使用して発行されます。 この証明書は、デバイスの登録時に Azure AD によって発行されます。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q: Windows 10 デバイス上に MS-Organization-P2P-Access によって発行された有効期限切れの証明書が複数表示されるのはなぜですか? どうすれば削除できますか?

**A:** Windows 10 バージョン 1709 以前では、暗号化の問題のために、有効期限切れの MS-Organization-P2P-Access 証明書が引き続き存在するという問題が確認されました。 多数の有効期限切れの証明書を処理できない VPN クライアント (Cisco AnyConnect など) を使用している場合、ユーザーにはネットワーク接続に関する問題が発生する可能性がありました。 この問題は、Windows 10 1803 リリースで、MS-Organization-P2P-Access 証明書が自動的に削除されるように修正されました。 この問題は、デバイスを Windows 10 1803 に更新することで解決できます。 更新できない場合は、悪影響を及ぼすことなく、これらの証明書を削除できます。  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD Join の FAQ

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q:Hybrid Azure AD Join のエラーの診断に関するトラブルシューティング情報はどこにありますか?

**A:** トラブルシューティング情報については、次の記事をご覧ください。

- [Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q:ハイブリッド Azure AD 参加済みの Windows 10 デバイスが、Azure AD デバイスの一覧に Azure AD 登録済みデバイスとして重複して表示されるのはなぜですか?

**A:** ユーザーがドメイン参加済みデバイス上のアプリに各自のアカウントを追加すると、 **[Add account to Windows?]\(アカウントを Windows に追加しますか?\)** というプロンプトが表示されることがあります。 このプロンプトで **[はい]** と入力すると、デバイスが Azure AD に登録されます。 信頼の種類は Azure AD 登録済みとしてマークされます。 ご自身の組織で Hybrid Azure AD Join を有効にすると、デバイスは、ハイブリッド Azure AD 参加済みになります。 それにより、同じデバイスに対して、2 つのデバイスの状態が表示されます。 

Hybrid Azure AD Join は、Azure AD 登録済み状態よりも優先されます。 したがって、すべての認証と条件付きアクセス評価では、デバイスはハイブリッド Azure AD 参加済みと見なされます。 Azure AD ポータルから Azure AD 登録済みデバイスのレコードを安全に削除できます。 [Windows 10 コンピューターでこの二重状態を回避またはクリーンアップする](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)方法を確認してください。 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q:UPN の変更後、Windows 10 のハイブリッド Azure AD 参加済みデバイスでユーザーに問題が生じるのはなぜですか?

**A:** 現在、ハイブリッド Azure AD 参加済みデバイスでの UPN の変更は完全にはサポートされていません。 UPN の変更後、ユーザーはデバイスにサインインして、オンプレミス アプリケーションにアクセスできますが、Azure AD での認証は失敗します。 その結果、ユーザーのデバイスで SSO と条件付きアクセスの問題が生じます。 現時点では、この問題を解決するには、Azure AD からデバイスの参加を解除 (昇格された特権を使用して "dsregcmd /leave" を実行) し、再度参加する (自動的に行われる) 必要があります。 現在、この問題の対策に取り組んでいます。 なお、この問題は Windows Hello for Business でサインインしているユーザーには影響しません。 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q:Windows 10 のハイブリッド Azure AD 参加済みデバイスには、クラウド リソースにアクセスするためのドメイン コントローラーへの通信経路が必要ですか?

**A:** ユーザーのパスワードが変更された場合を除いて、必要ありません。 Windows 10 のハイブリッド Azure AD の参加が完了した後に、ユーザーが少なくとも 1 回サインインすると、そのデバイスにはクラウド リソースにアクセスするためのドメイン コントローラーへの通信経路は不要になります。 Windows 10 では、インターネット接続があればどこからでも Azure AD アプリケーションへのシングル サインオンが実現できますが、パスワードが変更された場合は例外です。 Windows Hello for Business でサインインしているユーザーは、パスワード変更後、ドメイン コントローラーへの通信経路がなくても、引き続き Azure AD アプリケーションへのシングル サインオンを利用できます。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q:ユーザーがパスワードを変更し、企業ネットワークの外部から Windows 10 のハイブリッド Azure AD 参加済みデバイスへログインしようとするとどうなりますか?

**A:** パスワードを企業ネットワークの外部で (たとえば、Azure AD SSPR を使用して) 変更した場合、新しいパスワードを使用したユーザー サインインは失敗します。 ハイブリッド Azure AD 参加済みデバイスの場合、オンプレミスの Active Directory がプライマリ機関です。 デバイスがドメイン コントローラーに対する通信経路を持たない場合、そのデバイスは新しいパスワードを検証できません。 このため、新しいパスワードでデバイスにサインインするためには、ユーザーはドメイン コントローラーとの接続を (VPN 経由または企業ネットワーク内のいずれかから) 確立する必要があります。 そうしないと、Windows でのキャッシュされたサインインの機能により、古いパスワードでしかサインインできません。 ただし、古いパスワードはトークン要求時に Azure AD によって無効にされるため、シングル サインオンができなくなり、デバイスベースの条件付きアクセス ポリシーが失敗します。 この問題は、Windows Hello for Business を使用する場合は生じません。 

---

## <a name="azure-ad-register-faq"></a>Azure AD の登録の FAQ

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q:Android デバイスや iOS BYOD デバイスを登録できますか?

**A:** はい。ただし、ハイブリッドのお客様が Azure デバイス登録サービスを利用してのみ可能となります。 Active Directory フェデレーション サービス (AD FS) のオンプレミス デバイス登録サービスではサポートされていません。

### <a name="q-how-can-i-register-a-macos-device"></a>Q:macOS デバイスを登録するにはどうしたらよいですか?

**A:** 次の手順を実行します。

1.  [コンプライアンス ポリシーを作成する](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [macOS デバイスの条件付きアクセス ポリシーを定義する](../active-directory-conditional-access-azure-portal.md) 

**解説:**

- 条件付きアクセス ポリシーに含まれているユーザーがリソースにアクセスするには、[macOS でサポートされているバージョンの Office](../conditional-access/technical-reference.md#client-apps-condition) が必要です。 
- 最初のアクセス試行中に、ユーザーは、会社のポータルを使用してデバイスを登録するよう求められます。

## <a name="next-steps"></a>次の手順

- [Azure AD 登録済みデバイス](concept-azure-ad-register.md)について学習する
- [Azure AD 参加済みデバイス](concept-azure-ad-join.md)について学習する
- [ハイブリッド Azure AD 参加済みデバイス](concept-azure-ad-join-hybrid.md)について学習する
