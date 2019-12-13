---
title: Azure AD を使用してパスワードなしの認証のデプロイを行う
description: Azure Active Directory のパスワードなしの認証のデプロイを行います
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eb8398decd1a447d0676195d6369cdc7e791e40
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848495"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>パスワードなしの認証のデプロイを行う

ほとんどのサイバー攻撃は、組織内のユーザーから盗まれたユーザー名とパスワードを使用して始められます。 

組織は、次のものを使用するようにユーザーに要求して、脅威に対抗しようとします。

- 長いパスワード
- 複雑なパスワード
- パスワードの頻繁な変更
- Multi-Factor Authentication (MFA)

これらの取り組みによってユーザーはイライラし、サポート コストが跳ね上がることが、Microsoft の[調査で示されています](https://aka.ms/passwordguidance)。 詳細については、「[パスワードは関係ない](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)」を参照してください。

パスワードを使用しない認証をデプロイすると、次のような利点があります。

- セキュリティの強化。 攻撃対象としてのパスワードをなくすことで、フィッシングやパスワード スプレー攻撃のリスクが軽減します。
- ユーザー エクスペリエンスの向上。 ユーザーは、どこからでもデータにアクセスできる便利な手段を提供され、Outlook、OneDrive、Office などにモバイルから簡単にアクセスできるようになります。
- 堅牢な分析情報。 堅牢なログ記録と監査を使用して、ユーザーのパスワードなしのアクティビティに対する分析情報が得られます。

パスワードは、自分が持っているものと、自分が備えているものや知っていることに置き換えられます。 たとえば、Windows Hello for Business では、顔や指紋などの生体認証ジェスチャや、ネットワーク経由で送信されないデバイス固有の PIN などがあります。

Microsoft では、多くのシナリオに対応する 3 つのパスワードなし認証オプションが提供されています。 これらの方法は、同時に使用することができます。 

- [Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) は、専用の Windows コンピューターを使用するユーザーに最適です。
- [FIDO2 セキュリティ キー](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)でのセキュリティ キー サインインは、キオスクなどの共有コンピューターにサインインするユーザー、電話の使用が制限されている状況、および高い特権を持つ ID に対して特に便利です。
- [Microsoft Authenticator アプリ](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)での電話サインインは、モバイル デバイスを使用するユーザーにパスワードなしのオプションを提供する場合に便利です。 ユーザーが任意のプラットフォームまたはブラウザーにサインインできるようにすることで、iOS または Android の電話が強力でパスワードなしの資格情報に変換されます。 ユーザーは電話で通知を受け取り、画面に表示されている番号を電話の番号と照合し、生体認証データまたは PIN を使用することで、サインインします。

## <a name="compare-passwordless-authentication-methods"></a>パスワードなしの認証方法を比較する

Microsoft のパスワードなし認証方法では、さまざまなシナリオを実現できます。 組織のニーズ、前提条件、および各認証方法の機能を考慮して、パスワードなし認証戦略を選択します。 Windows 10 デバイスを使用するすべての組織に、Windows Hello for Business を使用することをお勧めします。 その後、追加のシナリオのために、(Microsoft Authenticator アプリでの) 電話サインインまたはセキュリティ キーを追加します。

### <a name="passwordless-authentication-scenarios"></a>パスワードなし認証のシナリオ

| シナリオ | 電話認証 | セキュリティ キー | Windows Hello for Business |
| --- | --- | --- | --- |
| **コンピューターでのサインイン**: <br> 割り当てられた Windows 10 デバイスから | **いいえ** | **はい** <br> 生体認証、PIN を使用 | **はい**<br>生体認証および PIN を使用 |
| **コンピューターでのサインイン**: <br> 共有 Windows 10 デバイスから | **いいえ** | **はい** <br> 生体認証、PIN を使用  | **いいえ** |
| **Web アプリでのサインイン**: <br>‎ ユーザー専用コンピューターから | **はい** | **はい** <br> コンピューターのサインインによってアプリへのシングル サインオンが有効になっている場合 | **はい**<br> コンピューターのサインインによってアプリへのシングル サインオンが有効になっている場合 |
| **Web アプリでのサインイン**: <br> モバイル デバイスまたは Windows 以外のデバイスから | **はい** | **いいえ** | **いいえ** |
| **コンピューターでのサインイン**: <br> Windows 以外のコンピューター | **いいえ** | **いいえ** | **いいえ** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの技術的な考慮事項

**AD FS 統合** - ユーザーが Microsoft Authenticator のパスワードなしの資格情報を有効にすると、そのユーザーの認証では、既定で承認のための通知が送信されます。 ハイブリッド テナントのユーザーは、[代わりにパスワードを使用する] を選択しない限り、サインインのために ADFS に送られることはありません。 このプロセスでは、オンプレミスの条件付きアクセス ポリシーとパススルー認証フローもバイパスされます。 ただし、login_hint が指定されている場合、ユーザーは AD FS に送られ、パスワードなしの資格情報を使用するオプションはバイパスされます。

**Azure MFA サーバー** - 組織のオンプレミスの Azure MFA サーバーを介した MFA が有効になっているエンド ユーザーは、単一のパスワードなしの電話サインイン資格情報を引き続き作成し、使用することができます。 ユーザーが資格情報で Microsoft Authenticator の複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。

**デバイス登録** - Authenticator アプリをパスワードなし認証に使用するには、デバイスを Azure AD テナントに登録する必要があり、共有デバイスは使用できません。 デバイスは 1 つのテナントにのみ登録できます。 この制限は、Authenticator アプリを使用する電話サインインに対しては、1 つの職場または学校アカウントのみがサポートされることを意味します。

## <a name="prerequisites"></a>前提条件

組織では、パスワードなしのデプロイを始める前に、次の前提条件を満たす必要があります。

| 前提条件 | Authenticator アプリ | FIDO2 セキュリティ キー |
| --- | --- | --- |
| [Azure MFA とパスワード リセットのセルフサービス (SSPR) の統合された登録](howto-registration-mfa-sspr-combined.md)が有効になっている (プレビュー機能) | √ | √ |
| [ユーザーが Azure MFA を実行できる](howto-mfa-getstarted.md) | √ | √ |
| [ユーザーが Azure MFA と SSPR に登録している](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [ユーザーがモバイル デバイスを Azure Active Directory に登録している](../devices/overview.md) | √ |   |
| Microsoft Edge や Mozilla Firefox などのサポートされているブラウザーを使用している Windows 10 バージョン 1809 以降 <br> (バージョン 67 以降)。 <br> *ネイティブ サポートには 1903 以降のバージョンをお勧めします*。 |   | √ |
| 互換性のある FIDO2 セキュリティ キー。 [Microsoft でテストおよび検証済み](howto-authentication-passwordless-enable.md)の FIDO2 セキュリティ デバイスまたは他の互換性のある FIDO2 セキュリティ デバイスを使用していることを確認します。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello for Business の前提条件

Windows Hello の前提条件は、オンプレミス、ハイブリッド、クラウドのみのどの構成にデプロイするかに大きく依存します。 詳細については、[Windows Hello for Business の前提条件の完全な一覧](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)を参照してください。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

ユーザーは、Azure MFA 登録フローの一部として、パスワードなしの方法を登録します。 ユーザー名とパスワードによる多要素認証と別の登録済み方法を、一部のシナリオで電話またはセキュリティ キーを使用できない場合の代替手段として使用できます。

### <a name="security-key-lifecycle"></a>セキュリティ キーのライフサイクル

セキュリティ キーを使用するとリソースにアクセスすることができるので、それらの物理デバイスの管理を計画する必要があります。

1. キーの配布: 組織にキーをプロビジョニングする方法を計画します。 集中的なプロビジョニング プロセスを使用することも、エンド ユーザーが FIDO 2.0 と互換性のあるキーを購入できるようにすることもできます。
1. キーのアクティブ化: エンド ユーザーは、自分でセキュリティ キーをアクティブ化する必要があります。 エンド ユーザーは、[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) でセキュリティ キーを登録し、最初の使用時に 2 番目の要素 (PIN または生体認証) を有効にします。
1. キーの無効化: セキュリティ キー機能がプレビュー段階の間は、管理者がユーザー アカウントからキーを削除する方法はありません。 ユーザーが削除する必要があります。 キーをなくしたり盗まれたりした場合:
   1. パスワードなし認証が有効になっているすべてのグループからユーザーを削除します。
   1. ユーザーが認証方法としてのキーを削除したことを確認します。
   1. 新しいキーを発行します。
1. キーの交換: ユーザーは、2 つのセキュリティ キーを同時に有効にすることができます。 セキュリティ キーを交換するときは、ユーザーも交換されるキーを削除したことを確認します。

### <a name="enable-windows-10-support"></a>Windows 10 のサポートを有効にする

FIDO2 セキュリティ キーを使用して Windows 10 のサインインを有効にするには、Windows 10 で資格情報プロバイダー機能を有効にする必要があります。 有効にするには次の 2 つの方法があります。

- [Intune を使用して資格情報プロバイダーを有効にする](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune のデプロイが推奨されるオプションです。
- [プロビジョニング パッケージを使用して資格情報プロバイダーを有効にする](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Intune のデプロイを使用できない場合は、管理者が各コンピューターにパッケージをデプロイして、資格情報プロバイダー機能を有効にする必要があります。 パッケージのインストールは、次のいずれかのオプションを使用して実行できます。
      - グループ ポリシーまたは System Center Configuration Manager (SCCM)
      - Windows 10 コンピューターへのローカル インストール

### <a name="register-security-keys"></a>セキュリティ キーを登録する

ユーザーは、Azure Active Directory に参加している各 Windows 10 マシンにセキュリティ キーを登録する必要があります。

詳細については、「[FIDO2 セキュリティ キーのユーザー登録と管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)」を参照してください。

### <a name="licensing-for-passwordless-authentication"></a>パスワードなしの認証のライセンス

パスワードなしの認証に追加料金がかかることはありませんが、一部の前提条件では Premium サブスクリプションが必要になる場合があります。 機能とライセンスの詳細については、[Azure Active Directory のライセンスに関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="develop-a-plan"></a>プランを開発する

各認証方法に対するビジネス ニーズとユース ケースを検討します。 その後、ニーズに最適な方法を選択します。

### <a name="use-cases"></a>ユース ケース

次の表では、このプロジェクトの間に実装するユース ケースの概要を示します。

| 領域 | 説明 |
| --- | --- |
| **Access (アクセス)** | パスワードなしのサインインは、企業ネットワーク内外の企業または個人のデバイスから使用できます。 |
| **監査** | 管理者は、使用状況データを使用して、ほぼリアルタイムで監査を行うことができます。 <br> 使用状況データは、少なくとも 29 日に一度、企業システムにダウンロードされるか、または SIEM ツールを使用します。 |
| **ガバナンス** | 適切な認証方法および関連するグループへのユーザー割り当てのライフサイクルを定義して監視します。 |
| **セキュリティ** | 適切な認証方法へのアクセスは、ユーザーとグループの割り当てによって制御されます。 <br> 承認されたユーザーだけが、パスワードなしのサインインを使用できます。 |
| **パフォーマンス** | アクセス割り当ての伝達タイムラインが文書化され、監視されます。 <br> 使いやすいようにサインイン時間が測定されます。 |
| **ユーザー エクスペリエンス** | ユーザーはモバイルの互換性を理解しています。 <br> ユーザーは、Authenticator アプリのパスワードなしサインインを構成できます。 |
| **サポート** | ユーザーは、パスワードなしサインインの問題に対するサポートを見つける方法を認識しています。 |

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。

### <a name="organization-communications"></a>組織のコミュニケーション

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザーのエクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、事前に連絡します。

エンド ユーザーには、次のことを伝える必要があります。

- [統合されたセキュリティ登録エクスペリエンスの有効化](howto-authentication-passwordless-phone.md)
- [Microsoft Authenticator アプリのダウンロード](../user-help/user-help-auth-app-download-install.md)
- [Microsoft Authenticator アプリでの登録](howto-authentication-passwordless-phone.md)
- [電話を使用したサインイン](../user-help/user-help-auth-app-sign-in.md)

Microsoft では、伝達方法の原案として役立つ MFA [通信テンプレート](https://aka.ms/mfatemplates)、パスワード リセットのセルフサービス (SSPR) [通信テンプレート](https://www.microsoft.com/download/details.aspx?id=56768)、[エンド ユーザー文書](../user-help/security-info-setup-signin.md)が用意されています。 ユーザーに [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) にアクセスさせ、そのページのセキュリティ情報リンクを選択して直接登録させることができます。

### <a name="testing-passwordless"></a>パスワードなしのテスト

デプロイの各段階で、結果が期待どおりであることをテストします。

#### <a name="testing-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのテスト

次に示すのは、Microsoft Authenticator アプリでのパスワードなし認証のサンプル テスト ケースです。

| シナリオ | 予想される結果 |
| --- | --- |
| ユーザーは Microsoft Authenticator アプリを登録できる | ユーザーは aka.ms/mysecurityinfo からアプリを登録できます |
| ユーザーは電話によるサインインを有効にできる | 職場アカウント用に構成された電話サインイン |
| ユーザーは電話でサインインしてアプリにアクセスできる | ユーザーは、電話でのサインイン フローを通過し、指定されたアプリケーションに到達します。 |
| Azure Active Directory ポータルの認証方法画面で Microsoft Authenticator のパスワードなしサインインをオフにして、電話サインイン登録へのロールバックをテストする | 前に有効にしたユーザーが、Microsoft Authenticator からパスワードなしのサインインを使用できなくなります。 |
| Microsoft Authenticator アプリから電話によるサインインを削除する | Microsoft Authenticator で職場アカウントが使用できなくなります |

#### <a name="testing-security-keys"></a>セキュリティ キーのテスト

次に示すのは、セキュリティ キーでのパスワードなし認証のサンプル テスト ケースです。

**Azure Active Directory に参加している Windows 10 デバイスへのパスワードなし FIDO サインイン**

| シナリオ | 予想される結果 |
| --- | --- |
| ユーザーは FIDO2 デバイス (1809) を登録できる | ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] を使用して、FIDO2 デバイスを登録できます |
| ユーザーは FIDO2 デバイス (1809) をリセットできる | ユーザーは、製造元のソフトウェアを使用して FIDO2 デバイスをリセットできます |
| ユーザーは FIDO2 デバイス (1809) を使用してサインインできる | ユーザーはサインイン ウィンドウからセキュリティ キーを選択し、正常にサインインできます。 |
| ユーザーは FIDO2 デバイス (1903) を登録できる | ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] で、FIDO2 デバイスを登録できます |
| ユーザーは FIDO2 デバイス (1903) をリセットできる | ユーザーは、[設定] > [アカウント] > サインイン オプション > [セキュリティ キー] で、FIDO2 デバイスをリセットできます |
| ユーザーは FIDO2 デバイス (1903) を使用してサインインできる | ユーザーはサインイン ウィンドウからセキュリティ キーを選択し、正常にサインインできます。 |

**Azure AD Web アプリへのパスワードなしの FIDO サインイン**

| シナリオ | 予想される結果 |
| --- | --- |
| ユーザーは Microsoft Edge を使用して aka.ms/mysecurityinfo で FIDO2 デバイスを登録できる | 登録は成功します |
| ユーザーは Firefox を使用して aka.ms/mysecurityinfo で FIDO2 デバイスを登録できる | 登録は成功します |
| ユーザーは Microsoft Edge を使用して FIDO2 デバイスで OneDrive Online にサインインできる | サインインは成功します |
| ユーザーは Firefox を使用して FIDO2 デバイスで OneDrive Online にサインインできる | サインインは成功します |
| Azure Active Directory ポータルの [認証方法] ブレード内で FIDO2 セキュリティ キーをオフにすることにより、FIDO2 デバイス登録のロールバックをテストする | ユーザーはセキュリティ キーを使用してサインインするように求められ、ログに正常に記録されて、エラーが表示されます: "会社のポリシーでは、別の方法を使用してサインインする必要があります"。 その後、ユーザーは別の方法を選択して正常にサインインできるようになります。 ウィンドウを閉じてもう一度サインインし、同じエラー メッセージが表示されないことを確認します。 |

### <a name="auditing-passwordless"></a>パスワードなしの監査

Azure AD には、技術やビジネスの分析情報を提供するレポートがあります。 ビジネスおよび技術アプリケーションの所有者に、組織の要件に基づいてこれらのレポートの所有権を引き受けさせ、レポートを使用させます。

管理者は、Azure Active Directory ポータルの [認証方法] セクションで、パスワードなしの資格情報の設定を有効にし、管理することができます。

Azure AD では、次の場合に監査ログにエントリが追加されます。

- 管理者は [認証方法] セクションで変更を行います。
- ユーザーは、Azure Active Directory で資格情報に対して任意の種類の変更を行うことができます。

次の表は、一般的なレポート シナリオの例を示しています。

|   | リスクの管理 | 生産性の向上 | ガバナンスとコンプライアンス |
| --- | --- | --- | --- |
| **レポートの種類** | 認証方法 - 統合されたセキュリティ登録に登録されているユーザー | 認証方法 – アプリ通知に登録されているユーザー | サインイン: テナントにアクセスしているユーザーとその方法を確認する |
| **潜在的なアクション** | 対象ユーザーがまだ登録されていない | Microsoft Authenticator アプリまたはセキュリティ キーの導入を推進する | アクセスを取り消すか、管理者に対して追加のセキュリティ ポリシーを適用する |

**Azure AD ではほとんどの監査データが 30 日間保持され**、Azure 管理ポータルまたは API でデータを分析システムにダウンロードして利用できます。 さらに長い期間保持する必要がある組織では、ログをエクスポートし、[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk、Sumo Logic などの SIEM ツールに取り込む必要があります。 [アクセスおよび使用状況レポートの表示の詳細を確認してください](../reports-monitoring/overview-reports.md)。

ユーザーは、[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動して、資格情報を登録および管理できます。 このリンクにより、ユーザーは、SSPR/MFA の統合によって有効になったエンド ユーザー資格情報管理エクスペリエンスに移動します。 FIDO2 セキュリティ デバイスの登録またはユーザーによる認証方法の変更は、Azure Active Directory 監査ログに記録されます。

ユーザーが、セキュリティ キーのアカウントを有効または無効にするか、Windows 10 コンピューターでセキュリティ キーの 2 番目の要素をリセットすると、次のイベント ID でエントリがセキュリティ ログに追加されます: 4670、5382。

### <a name="plan-for-rollback"></a>ロールバックのための計画

パスワードなし認証は軽量の機能であり、エンド ユーザーへの影響は最小限に抑えられますが、ロールバックが必要になる場合があります。

ロールバックを行うには、管理者が Azure Active Directory ポータルにサインインし、目的の強力な認証方法を選択して、有効にするオプションを [いいえ] に変更する必要があります。 このプロセスにより、すべてのユーザーのパスワードなし機能が無効になります。

FIDO2 セキュリティ デバイスを既に登録しているユーザーには、次回のサインイン時に、セキュリティ デバイスの使用を求めるメッセージが表示され、次のエラーが表示されます。

![別のサインイン方法を選択する](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>パイロットを計画する

パスワードなしの認証をデプロイするときは、最初に 1 つ以上のパイロット グループを有効にする必要があります。 この目的のためだけに[グループを作成する](../fundamentals/active-directory-groups-create-azure-portal.md)ことができます。 パイロットに参加するユーザーをグループに追加します。 その後、選択したグループに対して、新しいパスワードなしの認証方法を有効にします。

グループは、オンプレミスのディレクトリまたは Azure AD から同期できます。 パイロットの結果に満足できたら、すべてのユーザーをパスワードなし認証に切り替えることができます。

デプロイの計画に関するページの「[パイロットのベスト プラクティス](https://aka.ms/deploymentplans)」を参照してください。

## <a name="deploy-passwordless-authentication"></a>パスワードなしの認証をデプロイする

選択した方法に適した後の手順に従います。

### <a name="required-administrative-roles"></a>必要な管理者ロール

| Azure AD ロール | 説明 |
| --- | --- |
| 認証管理者 | 認証方法を実装および管理できる最小限の特権を持つロール |
| User | デバイスで Authenticator アプリを構成するか、Web または Windows 10 でのサインイン用のセキュリティ キー デバイスを登録するための、最小限の特権を持つロール。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリでの電話によるサインインをデプロイする

記事「[Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)」の手順に従って、組織内でのパスワードなしの認証方法として Microsoft Authenticator アプリを有効にします。

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 セキュリティ キーのサインインをデプロイする

記事「[Azure AD へのパスワードなしのセキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md)」の手順に従って、組織でのパスワードなしの認証方法として FIDO2 セキュリティ キーを有効にします。

### <a name="troubleshoot-phone-sign-in"></a>電話でのサインインのトラブルシューティング

| シナリオ | 解決策 |
| --- | --- |
| ユーザーが統合された登録を実行できない | [統合された登録](concept-registration-mfa-sspr-combined.md)が有効になっていることを確認します。 |
| ユーザーは Authenticator アプリで電話サインインを有効にできない | ユーザーがデプロイのスコープ内にいることを確認します |
| ユーザーはパスワードなし認証のスコープ内にいないが、パスワードなしサインインのオプションを表示され、操作を完了できない。 | このシナリオは、ポリシーが作成される前に、ユーザーがアプリケーションで電話によるサインインを有効にした場合に発生します。 <br> サインインを有効にするには: パスワードなしのサインインが有効になっているユーザーのスコープにユーザーを追加します。 <br> サインインをブロックするには: ユーザーにそのアプリケーションから資格情報を削除させます。 |

### <a name="troubleshoot-security-key-sign-in"></a>セキュリティ キー サインインのトラブルシューティング

| シナリオ | 解決策 |
| --- | --- |
| ユーザーが統合された登録を実行できない | [統合された登録](concept-registration-mfa-sspr-combined.md)が有効になっていることを確認します。 |
| ユーザーが[セキュリティの設定](https://aka.ms/mysecurityinfo)でセキュリティ キーを追加できない | [セキュリティ キー](howto-authentication-passwordless-security-key.md)が有効になっていることを確認します。 |
| ユーザーが Windows 10 のサインイン オプションでセキュリティ キーを追加できない | [Windows サインイン用のセキュリティ キーを確認します](howto-authentication-passwordless-enable.md) |
| **エラー メッセージ**:We detected that this browser or OS does not support FIDO2 security keys. (このブラウザーまたは OS では FIDO2 セキュリティ キーがサポートされていないことが検出されました。) | パスワードなしの FIDO2 セキュリティ デバイスは、Windows 10 バージョン 1809 以降のサポートされているブラウザー (Microsoft Edge、Firefox バージョン 67) でのみ登録できます。 |
| **エラー メッセージ**:会社のポリシーでは、別の方法を使用してサインインする必要があります。 | テナントでセキュリティ キーが有効になっているかどうか不明です。 |
| ユーザーが Windows 10 バージョン 1809 でセキュリティ キーを管理できない | バージョン 1809 では、FIDO2 キー ベンダーによって提供されるセキュリティ キー管理ソフトウェアを使用する必要があります。 ベンダーにサポートについてお問い合わせください。 |
| FIDO2 のセキュリティ キーが破損しているらしい — どうすればテストできるか | [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) に移動し、テスト アカウントの資格情報を入力し、問題のあるセキュリティ キーをプラグインし、画面の右上にある [+] ボタンをクリックし、作成をクリックして、作成プロセスを実行します。 このシナリオが失敗する場合、デバイスに欠陥がある可能性があります。 |

## <a name="next-steps"></a>次の手順

- [Azure AD へのパスワードなしのセキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md)
- [Microsoft Authenticator アプリでパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)
- [認証方法の使用状況と分析情報の詳細を確認する](howto-authentication-methods-usage-insights.md)
