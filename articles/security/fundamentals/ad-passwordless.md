---
title: Azure Active Directory でのパスワードなしの環境を理解する | Microsoft Docs
description: このガイドでは、Azure Active Directory 実装にパスワードなしの認証方法を選択することに責任を持つ、CEO、CIO、CISO、チーフ ID アーキテクト、エンタープライズ アーキテクト、セキュリティおよび IT の意思決定者を支援します。
keywords: パスワードなし、azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e702a5921e785100c5971057d1652db9aab26acf
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899953"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Azure Active Directory でのパスワードなしの環境

パスワードとの関係を解消するときが来ました。 パスワードは過去には良いものでしたが、今日のデジタルワークプレースでは、ハッカーにとって比較的簡単な攻撃ベクトルとなっています。 ハッカーはパスワードが大好きです。Azure Active Directory (Azure AD) で最もよく拒否されているパスワードに、年、月、季節、地元のスポーツ チームなどの用語が含まれていることを考えれば、その理由は難しくありません。 さらに、[研究](https://aka.ms/passwordguidance)によると、長さの要件、複雑さの要件、変更の頻度など、パスワード管理に対する従来の推奨事項は、人間性に関連するさまざまな理由から逆効果であることが示されています。

ユーザー アカウントの侵害に一般的に使用される 3 種類の攻撃は、パスワード スプレイ、フィッシング、および侵害のリプレイです。 [スマート ロックアウト](../../active-directory/authentication/howto-password-smart-lockout.md)、[禁止パスワード](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)、[パスワード保護](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)などの Azure AD の機能は、このような種類の攻撃に対する保護に役立ちます。 同様に、[多要素認証](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA)、つまり 2 段階認証を実装し、2 つ目の形式の認証を要求することでセキュリティが強化されます。 ただし、長期的に見れば、最も安全な認証方法を確保するには、パスワードなしのソリューションが最適なソリューションです。

この記事は、Microsoft のパスワードなしのソリューションを理解して実装し、次の選択肢から 1 つ以上を選択するために役立つユーザー体験の始まりです。

* **Windows Hello for Business**。 Windows 10 では、Windows Hello for Business によって、PC およびモバイル デバイス上のパスワードが強力な 2 要素認証に置き換えられます。 この認証はデバイスに関連付けられた新しい種類のユーザー資格情報で構成され、生体認証または PIN が使用されます。

* **Microsoft Authenticator でのパスワードなしのサインイン**。 Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 Windows Hello for Business のテクノロジと同様、Microsoft Authenticator は、キーベースの認証を使用して、デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。

* **FIDO2 セキュリティ キー**。 FIDO2 では、すべての Web サイトで一意であり、Windows Hello や外部のセキュリティ キーなどのローカル デバイスに保存される暗号化ログイン資格情報が提供されます。 このようなセキュリティ キーは、フィッシング、パスワードの盗用、およびリプレイ攻撃のリスクに対して耐性があります。 生体認証または PIN によるユーザー検証と組み合わせることで、このソリューションは現代のセキュリティ ニーズを満たす 2 要素認証になります。

## <a name="the-future-of-passwordless-authentication"></a>パスワードなしの認証の未来

最近では、銀行、クレジット カード会社、その他の組織やオンライン サービスでは、多くの場合、パスワードを使用する 1 回、電話、テキスト、またはアプリでもう 1 回という、2 回の本人確認を必須にすることでお客様のアカウントを保護しています。 多要素認証では、パスワードの共有、盗難、または推測というセキュリティ上の問題に対処できますが、パスワードを記憶する試みという不都合な要素には対処できません。 今日のクラウド時代にユーザーや組織が望むことは、安全性が高く、"*さらに*" 便利なパスワードなしの認証方法です。

![利便性とセキュリティ](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello for Business、Microsoft Authenticator によるパスワードなしのサインイン、および FIDO2 セキュリティ キーはいずれも、非常に安全で使いやすい認証方法をユーザーに提供する、シンプルで共通のアーキテクチャを共有しています。 3 つのいずれも、公開/秘密キー テクノロジに基づいており、生体認証や PIN などのローカル ジェスチャと、単一のデバイスにバインドされ、安全に保存され、共有されない秘密キーが必要です。

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows 10 では、Windows Hello for Business によって、PC およびデバイス上のパスワードが強力な 2 要素認証に置き換えられます。 認証は、デバイスに関連付けられた新しい種類のユーザー資格情報で構成されます。また、ユーザーが Azure AD およびオンプレミスの Active Directory に対して認証を受けられるようにする生体認証ジェスチャまたは PIN が使用されます。 Windows Hello for Business を使用してデバイスにサインインすることだけであれば、簡単です。 PIN または生体認証ジェスチャ (指紋や顔認識など) を使用します。

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello for Business のシナリオ

Windows Hello for Business は、指定した Windows PC を所有しているインフォメーション ワーカーに最適です。 生体認証と資格情報はユーザーの PC に直接関連付けられるため、所有者以外のユーザーからのアクセスは防止されます。 PKI 統合およびシングル サインオン (SSO) の組み込みサポートにより、Windows Hello for Business では、オンプレミスおよびクラウドの会社のリソースにシームレスにアクセスできるシンプルで便利な方法を提供します。

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello for Business の展開に関する考慮事項

Windows Hello for Business は、デバイスの登録、プロビジョニング、および認証を実行するためにいくつかのコンポーネントを使用する分散システムです。 そのため、展開には組織内の複数のチームにわたる適切な計画が必要です。 Windows Hello for Business の[計画ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)を使用すると、Windows Hello for Business の展開の種類と、検討する必要がある選択肢を決定することができます。

Windows Hello for Business を展開するときに選択できるオプションは多数あります。 複数のオプションが用意されているので、ほぼすべての組織が Windows Hello for Business を展開できます。 サポートされている次の種類の展開を検討してください。

* [Hybrid Azure AD 参加済みキー信頼展開](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybrid Azure AD 参加済み証明書信頼展開](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 参加シングル サインオン展開ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [オンプレミス キー信頼展開](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [オンプレミス証明書信頼展開](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

多くのオプションが用意されていると、展開は複雑に見えます。 ただし、ほとんどの組織では、おそらく Windows Hello for Business の展開が依存するほとんどのインフラストラクチャが実装済みであると判断するでしょう。 いずれにせよ、Windows Hello for Business は分散システムであり、適切な計画を立てることをお勧めします。

お客様の組織に最適な展開モデルを判断するには、「[Windows Hello for Business 展開の計画](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)」を読むことをお勧めします。 次に、立てた計画に基づいて既存の環境に Windows Hello for Business を適切に展開するには、「[Windows Hello for Business 展開ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)」を参照してください。

### <a name="how-windows-hello-for-business-works"></a>Windows Hello for Business の動作

#### <a name="user-sets-up-windows-hello-for-business"></a>ユーザーが Windows Hello for Business を設定する

登録時にユーザーの最初の 2 段階認証が行われた後、Windows Hello がユーザーのデバイスに設定され、Windows からユーザーにジェスチャを設定するように求められます。これには指紋や顔認識などの生体認証、または PIN の場合があります。 設定が完了した後、ユーザーは本人確認のためにジェスチャを指定します。 次に、Windows では Windows Hello を使用してユーザーが認証されます。

Windows 10 デバイスの機能に基づいて、ハードウェア トラステッド プラットフォーム モジュール (TPM) またはソフトウェア TPM という組み込みのセキュリティで保護されたエンクレーブを利用できます。 TPM には秘密キーが保存されており、そのロック解除には、顔、指紋、または PIN のいずれかが必要です。 生体認証データはローミングされず、外部のデバイスやサーバーに送信されることはありません。 Windows Hello によってデバイス上に生体認証識別データが保存されるだけなので、攻撃者が生体認証データを盗むために侵害する可能性がある単一のコレクション ポイントはありません。

> [!TIP]
> 表面的には、PIN はパスワードのように見えますが、実際にはより安全です。 パスワードと PIN の重要な違いは、PIN は、PIN が設定されたそのデバイスのみに関連付けられる点です。 パスワードを盗んだ人は、どこからでもアカウントにサインインできます。 ただし、PIN を盗む場合は、物理的なデバイスも盗む必要があります。 さらに、PIN はデバイスに対してローカルであり、どこにも送信されないので、送信中に傍受されたり、サーバーから盗まれたりすることはありません。

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>ユーザーがサインインに Windows Hello for Business を使用する

Windows Hello for Business の生体認証および PIN は、認証に非対称 (公開/秘密キー) 暗号化を使用します。 認証時に、暗号化は TLS セッション キーに関連付けられます。これにより、中間者 (MiTM) 攻撃によって結果のセキュリティ トークンまたは成果物が盗まれ、他の場所から再生されることがないように認証プロセスがセキュリティで保護されます。

Windows Hello for Business には、Azure AD および Active Directory のリソースに対してユーザーを認証する便利なサインイン エクスペリエンスが用意されています。 Azure AD 参加済みデバイスは、サインイン時に Azure に対する認証を受け、必要に応じて Active Directory に対する認証を受けることができます。 ハイブリッド Azure Active Directory 参加済みデバイスは、サインイン時に Active Directory に対する認証を受け、バックグラウンドで Azure Active Directory に対する認証を受けます。

![ソース画像の表示](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

次の手順は、Azure Active Directory へのサインイン認証を示しています。

![Windows 10 のロック画面](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. ユーザーは、生体認証または PIN のジェスチャを使用して Windows にサインインします。 このジェスチャによって、Windows Hello for Business の秘密キーのロックが解除され、クラウド AP プロバイダーというクラウド認証セキュリティ サポート プロバイダーに送信されます。

2. クラウド AP プロバイダーから Azure Active Directory に nonce が要求されます。

3. Azure AD からは 5 分間有効な nonce が返されます。

4. クラウド AP プロバイダーでは、ユーザーの秘密キーを使用して nonce に署名され、署名済みの nonce が Azure Active Directory に返されます。

5. Azure Active Directory では、ユーザーの安全に登録された公開キーを使用して、署名済みの nonce が nonce の署名に対して検証されます。 署名の検証後、Azure AD では返された署名済み nonce が検証されます。 nonce の検証後、Azure AD ではデバイスのトランスポート キーに暗号化されたセッション キーを使用して PRT が作成され、それがクラウド AP プロバイダーに返されます。

6. クラウド AP プロバイダーは、セッション キーと共に暗号化された PRT を受け取ります。 クラウド AP プロバイダーでは、デバイスのプライベート トランスポート キーを使用してセッション キーが復号化され、デバイスの TPM を使用してセッション キーが保護されます。

7. クラウド AP プロバイダーからは成功の認証応答が Windows に返されます。その後、ユーザーは再認証を必要とせずに、Windows だけでなく、クラウドおよびオンプレミスのアプリケーションにアクセスできるようになります (SSO)。

Windows Hello for Business が関係する他のシナリオの認証プロセスの詳細については、「[Windows Hello for Business and Authentication (Windows Hello for Business と認証)](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key)」を参照してください。

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>ユーザーが Windows Hello for Business の資格情報を管理する

[Microsoft PIN リセット サービス](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)は、必要に応じてユーザーが自分の PIN をリセットできる Azure AD の機能です。 管理者は、グループ ポリシー、Microsoft Intune、または互換性のある MDM を使用して、Microsoft PIN リセット サービスを安全に使用するように Windows 10 デバイスを構成できます。これにより、ユーザーは、忘れた PIN を再登録することなく、設定またはロック画面上でリセットできるようになります。

場合によっては、ユーザーはパスワードの使用にフォールバックする必要があります。 [パスワード リセットのセルフサービス](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR) は、ユーザーが IT スタッフに連絡することなくパスワードをリセットできるようにする Azure AD のもう 1 つの機能です。 サービスを使用する前に、ユーザーはパスワード リセットのセルフサービスに登録するか、登録してもらう必要があります。 登録時に、ユーザーは組織で有効になっている 1 つまたは複数の認証方法を選択します。 SSPR を使用すると、ユーザーは場所や時間に関係なく、迅速にブロックを解除して作業を続けることができます。 ユーザーが自分でブロックを解除できるようにすることで、組織としては、生産に寄与しない時間と、パスワード関連で最も一般的な問題の高いサポート コストを、削減できます。

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Microsoft Authenticator でのパスワードなしのサインイン

Microsoft Authenticator によるパスワードなしのサインインは、電話によるサインインを使用して Azure AD アカウントにサインインするために使用できるもう 1 つのパスワードなしのソリューションです。 本人確認を行うには、自分の知っていることと持っているものを提供する必要はありますが、電話によるサインインを使用すると、パスワードの入力をスキップし、指紋、顔、または PIN を使用してモバイル デバイスですべての本人確認を実行できます。

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator のパスワードなしのシナリオ

Microsoft Authenticator アプリを使用すると、ユーザーは本人確認を行い、職場用または個人用のアカウントに対して認証を受けることができます。 これは、ワンタイム パスコードやプッシュ通知を使用してパスワードを補強したり、パスワードの必要性を完全に置き換えたりするためにも使用できます。 パスワードを使用する代わりに、ユーザーは携帯電話を使用し、指紋スキャン、顔または虹彩の認識、または PIN を介して本人確認を行います。 このツールは、Windows Hello が使用しているものと同様のセキュリティで保護されたテクノロジに基づいて構築されており、モバイル デバイス上のシンプルなアプリにパッケージ化されているので、ユーザーにとって便利な選択肢です。 Microsoft Authenticator アプリは、Android および iOS で利用できます。

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator の展開に関する考慮事項

Microsoft Authenticator アプリを使用して Azure AD にパスワードなしでサインインするための前提条件は次のとおりです。

* エンド ユーザーの Azure Multi-Factor Authentication が有効になっている

* アプリを容易に展開できるように、ユーザーには Microsoft Intune またはサードパーティのモバイル デバイス管理 (MDM) ソリューションを使用して自分のデバイスを登録することをお勧めします

これらの要件が満たされていると想定して、管理者がテナントでパスワードなしの電話によるサインインを有効にするには、[Windows PowerShell](../../active-directory/authentication/howto-authentication-passwordless-phone.md) を使用します。 テナントで電話によるサインインが有効になった後、エンド ユーザーが自分の電話を使用したサインインを選択するには、アプリの **[アカウント]** 画面で職場または学校アカウントを選択し、 **[電話によるサインインを有効にする]** を選択します。

管理者がパスワードなしのサインインを有効にしていると想定すると、エンド ユーザーは次の要件を満たす必要があります。

* Azure Multi-Factor Authentication への登録

* iOS 8.0 以降、または Android 6.0 以降を実行しているデバイスにインストールされている最新バージョンの Microsoft Authenticator

* アプリにプッシュ通知が追加されている職場または学校のアカウント

アカウントがロックアウトされたり、新しいデバイス上でアカウントの再作成が必要になったりする可能性を回避するには、Microsoft Authenticator を使用してクラウドに[アカウントの資格情報をバックアップ](../../active-directory/user-help/user-help-auth-app-backup-recovery.md)することをお勧めします。 バックアップ後に、アプリを使用して情報を新しいデバイスに復旧することもできます。これにより、ロックアウトやアカウントの再作成の必要性を回避できる可能性があります。

ユーザーの多くはパスワードのみを使用した認証に慣れているため、組織はこのプロセスについてすべてのユーザーを教育することが重要です。 認知によって、ユーザーが Microsoft Authenticator アプリを使用したサインインに関連する[問題](../../active-directory/authentication/howto-authentication-passwordless-phone.md)をヘルプ デスクに問い合わせる可能性が低くなります。

> [!NOTE]
> このソリューションの潜在的な障害点は、ローミング ユーザーがインターネットに接続できない場所にいるときです。 FIDO2 セキュリティ キーと Windows Hello for Business に同じ制限は適用されません。

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Microsoft Authenticator でのパスワードなしのサインインのしくみ

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>ユーザーが Microsoft Authenticator でのパスワードなしのサインインを設定する

Microsoft Authenticator アプリをパスワードなしのソリューションとして使用して Azure AD アカウントにサインインする前に、管理者とエンド ユーザーの両方が手順を実行する必要があります。

まず、管理者は Windows PowerShell を使用して、テナント内で[資格情報としてのアプリの使用を有効にする](../../active-directory/authentication/howto-authentication-passwordless-phone.md)必要があります。 管理者は、Azure Multi-Factor Authentication (Azure MFA) のエンド ユーザーを有効にし、[確認方法](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods)の 1 つとして Microsoft Authenticator アプリを構成する必要もあります。

エンド ユーザーは Microsoft Authenticator アプリを[ダウンロードしてインストール](../../active-directory/user-help/user-help-auth-app-download-install.md)し、確認方法の 1 つとして Microsoft Authenticator アプリを使用するように[アカウントを設定する](../../active-directory/user-help/security-info-setup-auth-app.md)必要があります。

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>ユーザーがパスワードなしのサインインに Microsoft Authenticator を使用する

Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 Windows 10 のロック画面にサインイン オプションとして Microsoft Authenticator アプリは表示されませんが、ユーザーはユーザー名を入力し、モバイル デバイスでプッシュ通知を受信してプレゼンスを確認できます。 ユーザーはサインイン画面で番号を照合して自分のプレゼンスを確認し、次に顔のスキャン、指紋、または PIN を提供して秘密キーのロックを解除し、認証を完了します。 この多要素認証方法はパスワードよりも安全であり、パスワードとコードを入力するよりも便利です。

![Authenticator のサインイン](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Microsoft Authenticator を使用したパスワードなしの認証は、Windows Hello for Business と同じ基本パターンに従いますが、使用されている Microsoft Authenticator アプリのバージョンを Azure AD で検出できるようにユーザーを識別する必要があるため、もう少し複雑です。

![Authenticator のプロセス](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. ユーザーは自分のユーザー名を入力します。

2. Azure AD では、ユーザーが強力な資格情報を持っていることが検出され、強力な資格情報フローが開始されます。

3. iOS デバイス上では Apple Push Notification Service (APNS) を介して、Android デバイス上では Firebase Cloud Messaging (FCM) を介して、通知がアプリに送信されます。

4. ユーザーはプッシュ通知を受け取り、アプリを開きます。

5. アプリでは Azure AD が呼び出され、プレゼンス証明のチャレンジと nonce を受け取ります。

6. ユーザーは、自分の生体認証または PIN を入力して秘密キーのロックを解除することでチャレンジを完了します。

7. nonce は秘密キーで署名され、Azure AD に返送されます。

8. Azure AD では公開キーと秘密キーの検証が実行され、トークンが返されます。

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>ユーザーが Microsoft Authenticator の資格情報を使用してパスワードなしのサインインを管理する

[統合された登録](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)を使用すると、ユーザーは Azure Multi-Factor Authentication とパスワード リセットのセルフサービスの両方を登録してそのメリットを得ることができます。 ユーザーは自分の [[マイ プロファイル] ページ](https://aka.ms/mysecurityinfo)に移動して、これらの設定を登録および管理します。 SSPR を有効にすることに加えて、統合された登録では複数の認証方法とアクションをサポートします。

## <a name="fido2-security-keys"></a>FIDO2 セキュリティ キー

FIDO2 は、FIDO Alliance 標準の最新バージョンであり、W3C の Web 認証 (WebAuthN) 標準と、それに対応する FIDO Alliance の Client-to-Authenticator プロトコル (CTAP2) という 2 つのコンポーネントで構成されています。 FIDO2 標準により、ユーザーはハードウェア、モバイル、および生体認証ベースの認証システムを利用して、モバイルおよびデスクトップ環境の多くのアプリや Web サイトで簡単に認証できます。

Microsoft と業界のパートナーは、共有デバイス上で簡単でセキュリティで保護された認証を実現するために、Windows Hello 用の FIDO2 セキュリティ デバイスを共同開発しています。 FIDO2 セキュリティ キーを使用すると、自分の資格情報を持ち運び、組織に属する [Azure AD](https://aka.ms/azuread418) 参加済み Windows 10 デバイスの認証を安全に受けられます。

WebAuthN には、Web アプリおよびサービスによる強力なパスワードなしの認証の開発と実装を可能にする API が定義されています。 CTAP プロトコルを使用すると、FIDO 準拠のセキュリティ キーなどの外部デバイスが WebAuthN と連携して認証システムとして機能することができます。 Web 認証を使用すると、ユーザーは自分の顔、指紋、PIN、または携帯可能な FIDO2 セキュリティ キーを使用してオンライン サービスにサインインし、パスワードの代わりに強力な公開キーの資格情報を利用できます。 現在、WebAuthN は Microsoft Edge でサポートされており、Chrome と Firefox のサポートは開発中です。

FIDO2、WebAuthN、および CTAP プロトコルに準拠したデバイスとトークンによって、パスワードを使用せずに強力な認証を行うクロスプラットフォーム ソリューションが実現します。 Microsoft パートナーは、USB セキュリティ キーや NFC 対応のスマート カードなど、さまざまなセキュリティ キー フォーム ファクターに取り組んでいます。

### <a name="fido2-security-keys-scenarios"></a>FIDO2 セキュリティ キーのシナリオ

FIDO2 セキュリティ キーを Azure AD へのサインインに使用するには、Windows 10 のロック画面でセキュリティ キーを資格情報プロバイダーとして選択します。 ユーザー名やパスワードは必要ないので、複数のユーザーで PC を共有する第一線のワーカーにとって理想的なソリューションです。 また、ユーザーの資格情報をデバイスから物理的に分離する必要があると企業のポリシーで規定している場合にも、これらは優れた認証オプションです。 ユーザーは、Windows 10 バージョン 1809 以降の Microsoft Edge ブラウザー内で FIDO2 セキュリティ キーを使用して Web サイトにサインインすることもできます。

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 セキュリティ キーの展開に関する考慮事項

管理者は Azure AD で FIDO2 のサポートを有効にして、その機能をユーザーまたはグループに割り当てることができます。 キーのプロビジョニング方法のポリシーも作成できます。また、ポリシーで、特定のハードウェア セキュリティ キーのセットを許可またはブロックするなどの制限を構成できます。 キーはエンド ユーザーに物理的に配布する必要があります。

**FIDO2 セキュリティ キーを使用した Azure AD および Web サイトへのパスワードなしのサインインを有効にするための要件は次のとおりです。**

* Azure AD

* Azure Multi-Factor Authentication

* 統合された登録のプレビュー

* FIDO2 セキュリティ キーのプレビューには、互換性のある FIDO2 セキュリティ キーが必要です

* Web 認証 (WebAuthN) には、Windows 10 バージョン 1809 以降上に Microsoft Edge が必要です。

* FIDO2 ベースの Windows サインインには、Azure AD 参加済み Windows 10 バージョン 1809 以降 (最適なエクスペリエンスのためには Windows 10 バージョン 1903 以降) が必要です

FIDO2 準拠のフォーム ファクターには、USB、NFC、および Bluetooth デバイスがあります。 一部のプラットフォームやブラウザーはまだ FIDO2 に準拠していないため、お客様の特定のニーズを満たすフォーム ファクターを選択することをお勧めします。

セキュリティ キーを紛失したり盗まれたりした場合にユーザーおよび管理者が従うべきプロトコルを各組織で作成することもお勧めします。 管理者またはユーザーが自分のセキュリティ キーをユーザーのプロファイルから削除して新しいものをプロビジョニングできるように、ユーザーは紛失または盗難にあったキーを報告する必要があります。

### <a name="how-fido2-security-keys-works"></a>FIDO2 セキュリティ キーのしくみ

#### <a name="user-sets-up-fido2-security-key"></a>ユーザーが FIDO2 セキュリティ キーを設定する

管理者は[手動でキーをプロビジョニング](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)し、エンド ユーザーに配布することができますが、Windows 10 のロック画面で FIDO2 資格情報プロバイダーをプロビジョニングして有効にすることは、[Intune](https://docs.microsoft.com/intune/windows-enrollment-methods) を介してサポートされます。 管理者は、パスワードなしの認証方法としてハードウェア トークン デバイスを有効にするには、[Azure portal](https://portal.azure.com/) も使用する必要があります。

FIDO2 セキュリティ キーを展開するには、ユーザーが[統合された登録](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)を使用して自分のキーを登録する必要もあります。 統合された登録では、ユーザーは一度登録するだけで、Azure Multi-Factor Authentication とシングル サインオン パスワード リセット (SSPR) の両方のメリットを受けられます。

既定の多要素認証方法としてハードウェア トークンを選択するだけでなく、追加の検証オプションも選択することをお勧めします。

* Microsoft Authenticator -- 通知

* Authenticator アプリまたはハードウェア トークン -- コード

* 音声通話

* テキスト メッセージ

#### <a name="user-using-fido2-security-key-for-sign-in"></a>ユーザーがサインインに FIDO2 セキュリティ キーを使用する

FIDO2 によって、認証システムとして使用されているフォーム ファクターと、組み込みのプラットフォーム認証システムを有効にするための公開/秘密キー暗号化との間の抽象レイヤーが提供され、Windows Hello やセキュリティ キーなどの組み込みのプラットフォーム認証システムで秘密キーに解決し、外部リソースにアクセスする識別子として使用できる公開キーを配布できるようになります。 FIDO2 セキュリティ キーには、秘密キーを保存し、ロックの解除には生体認証または PIN を必要とする、独自のセキュリティで保護されたエンクレーブが組み込まれています。 資格情報は、サービス間で再利用、再生、または共有できず、フィッシング攻撃、MiTM 攻撃、またはサーバー侵害の影響を受けません。

![FIDO2 のサインイン](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2 セキュリティ キーは、フォーム ファクターに関係なく、セキュリティで保護された認証を提供します。 セキュリティ キーには資格情報が保持されているので、Windows セキュリティ サインインで入力する (セキュリティ キーに統合されている) 指紋や PIN などの追加の 2 番目の要素で保護する必要があります。 Microsoft パートナーは、さまざまなセキュリティ キー フォーム ファクターに取り組んでいます。 例としては、USB セキュリティ キー、NFC 対応スマート カードなどがあります。

> [!NOTE]
> セキュリティ キーでは、[Microsoft との互換性を維持するために](https://aka.ms/fido2securitykeys)、FIDO2 CTAP プロトコルの一部の機能と拡張機能を実装する必要があります。 Microsoft は、Windows 10 および Azure Active Directory との互換性についてこれらのソリューションをテストしました。

![FIDO2 のサインイン プロセス](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. ユーザーは FIDO2 デバイスをコンピューターに接続します。

2. Windows で FIDO2 セキュリティ キーが検出されます。

3. Windows から認証要求が送信されます。

4. Azure AD から nonce が返送されます。

5. ユーザーはジェスチャを完了して、FIDO2 セキュリティ キーのセキュリティで保護されたエンクレーブに保存されている秘密キーのロックを解除します。

6. FIDO2 セキュリティ キーでは、秘密キーを使用して nonce に署名されます。

7. 署名された nonce を含む PRT トークン要求が Azure AD に送信されます。

8. Azure AD で、FIDO2 公開キーを使用して署名済みの nonce が検証されます。

9. Azure AD から PRT が返され、オンプレミスのリソースにアクセスできるようになります。

#### <a name="user-manages-their-fido2-security-key-credentials"></a>ユーザーが自分の FIDO2 セキュリティ キー資格情報を管理する

Microsoft Authenticator アプリと同様に、FIDO2 セキュリティ キーの資格情報管理は、エンド ユーザーの統合された登録エクスペリエンスに依存しています。

## <a name="deciding-a-passwordless-method"></a>パスワードなしの方法を決定する

これら 3 つのパスワードなしオプションのいずれを選択するかは、会社のセキュリティ、プラットフォーム、およびアプリの要件によって変わります。

Microsoft のパスワードなしテクノロジを選択する際には、考慮する必要がある要素がいくつかあります。

||**Windows Hello for Business**|**Microsoft Authenticator アプリでのパスワードなしのサインイン**|**FIDO2 セキュリティ キー**|
|:-|:-|:-|:-|
|**前提条件**| Windows 10 バージョン 1809 以降<br>Azure Active Directory| Microsoft Authenticator アプリ<br>電話 (iOS デバイスおよび Android 6.0 以降を実行している Android デバイス)|Windows 10 バージョン 1809 以降<br>Azure Active Directory|
|**モード**|プラットフォーム|ソフトウェア|ハードウェア|
|**システムとデバイス**|トラステッド プラットフォーム モジュール (TPM) が組み込まれた PC<br>PIN と生体認証の認識 |電話での PIN と生体認証の認識|Microsoft 互換の FIDO2 セキュリティ デバイス|
|**ユーザー エクスペリエンス**|Windows デバイスで PIN または生体認証の認識 (顔、虹彩、または指紋) を使用してサインインします。<br>Windows Hello の認証はデバイスに関連付けられています。ユーザーが会社のリソースにアクセスするには、デバイスと、PIN や生体認証要素などのサインイン コンポーネントの両方が必要です。|指紋スキャン、顔または虹彩の認識、または PIN を使用して携帯電話を使用してサインインします。<br>ユーザーは自分の PC または携帯電話から職場または個人用のアカウントにサインインします。|FIDO2 セキュリティ デバイス (生体認証、PIN、および NFC) を使用してサインインします<br>ユーザーは組織の管理に基づいてデバイスにアクセスし、PIN、USB セキュリティ キーや NFC 対応のスマート カードなどのデバイスを使用した生体認証、キー、またはウェアラブル デバイスに基づいて認証できます。|
|**有効なシナリオ**| Windows デバイスでのパスワードなしのエクスペリエンス。<br>デバイスやアプリケーションへのシングル サインオン機能を使用する専用の作業用 PC に適用できます。|携帯電話を使用するパスワードなしの任意の場所のソリューション。<br>任意のデバイスから Web 上の仕事用または個人用アプリケーションにアクセスするために適しています。|生体認証、PIN、および NFC を使用するワーカー向けのパスワードなしのエクスペリエンス。<br>共有の PC や携帯電話が実行可能な選択肢ではない場合 (ヘルプ デスク担当者、公共のキオスク、病院のチームなど) に適用できます|

次の表を使用して、お客様の要件とユーザーをサポートする方法を選択します。

|ペルソナ|シナリオ|環境|パスワードなしのテクノロジ|
|:-|:-|:-|:-|
|**管理者**|管理タスク用デバイスへのセキュリティで保護されたアクセス|割り当てられた Windows 10 デバイス|Windows Hello for Business、FIDO2 セキュリティ キー、またはその両方|
|**管理者**|Windows 以外のデバイスでの管理タスク| モバイルまたは Windows 以外のデバイス|Microsoft Authenticator アプリでのパスワードなしのサインイン|
|**インフォメーション ワーカー**|業務効率化作業|割り当てられた Windows 10 デバイス|Windows Hello for Business、FIDO2 セキュリティ キー、またはその両方|
|**インフォメーション ワーカー**|業務効率化作業| モバイルまたは Windows 以外のデバイス|Microsoft Authenticator アプリでのパスワードなしのサインイン|
|**現場のワーカー**|工場、プラント、小売店、またはデータ入力でのキオスク|共有 Windows 10 デバイス|FIDO2 セキュリティ キー|

## <a name="getting-started"></a>使用の開始

パスワードなしの認証は次代を担うものであり、より安全な環境へとつながる道です。 組織はこの変化に備えて計画を立て、パスワードへの依存度を減らすことをお勧めします。 まず次の目標を考慮します。

* ユーザーの MFA + Microsoft Authenticator アプリ + 条件付きアクセスを有効にします。

* Azure AD のパスワード保護 + 更新ポリシーをロールアウトします。

* 統合された登録を使用してユーザーの SSPR を有効にします。

* モビリティのために Microsoft Authenticator アプリを展開します。

* Windows Hello for Business を展開します (1903: 最新の状態を維持します)。

* 電話を使用できないユーザー用に FIDO2 デバイスを配置します。

* 可能であれば、パスワードベースの認証を無効にします。

これらの目標を達成するために、次のアプローチをお勧めします。

1. Azure Active Directory で、Azure MFA や条件付きアクセスなどの機能を最大限に活用できるようにします。

2. 多要素認証を有効にして保護を強化します。

3. ユーザーがパスワードの使用にフォールバックする必要がある場合は、パスワード リセットのセルフサービスを有効にします。

4. モビリティを強化するために、Microsoft Authenticator の電話によるサインインを展開します。

5. Windows Hello for Business をすべての Windows 10 デバイスに展開します。

6. FIDO2 セキュリティ キーを準備します。

> [!NOTE]
> パスワードなしの方法のライセンス要件の詳細については、Azure Active Directory の[ライセンスのページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="conclusion"></a>まとめ

過去数年間にわたり、Microsoft はパスワードなしの環境を可能にする取り組みを続けてきました。 Microsoft は、Windows 10 で Windows Hello for Business を導入しました。これは、Azure Active Directory および Active Directory へのシングル サインオンを可能にする、ハードウェアで保護された強力な 2 要素資格情報です。 Windows Hello for Business のテクノロジと同様、Microsoft Authenticator アプリは、キーベースの認証を使用して、モバイル デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。 FIDO2 セキュリティ キーを使用すると、Windows 10 のロック画面でセキュリティ キーを資格情報プロバイダーとして選択することで、資格情報を携帯して Azure AD にサインインできるようになります。 これら 3 つのパスワードなしソリューションのいずれでも、フィッシング、パスワード スプレイ、およびリプレイ攻撃のリスクが軽減され、ユーザーはどこからでもセキュリティで保護された便利な方法でサインインしてデータにアクセスできます。

生体認証や公開キーの暗号化などの最新の多要素認証テクノロジを広くアクセス可能なデバイスに採用することは、企業の ID リスクを有意に減らすことができる最も影響度の高い手順の 1 つです。 パスワードなしへの移行は、セキュリティで保護された認証のための長期的なアプローチであり、現在も進化中です。 新たな要件が生じても、組織は、パスワードなしのテクノロジへの移行を開始する計画を立てることで備えることができます。

## <a name="next-steps"></a>次の手順

* 「[what is passwordless? (パスワードなしとは)](../../active-directory/authentication/concept-authentication-passwordless.md)」の概要
* [Azure AD でパスワードなしを有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
