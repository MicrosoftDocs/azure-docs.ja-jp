---
title: Azure Active Directory のセルフサービス パスワード リセットのデプロイに関する考慮事項
description: Azure AD のセルフサービス パスワード リセットの実装を成功させるためのデプロイに関する考慮事項と戦略について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55fc4c0b5f3e2bbf57c972ad4a7fd2c9bdd3810e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579197"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Azure Active Directory のセルフサービス パスワード リセットのデプロイを計画する

> [!IMPORTANT]
> このデプロイ計画は、Azure AD セルフサービス パスワード リセット (SSPR) をデプロイするためのガイダンスとベスト プラクティスを提供します。
>
> **自分がエンド ユーザーであり、自分のアカウントに戻す必要がある場合は、[https://aka.ms/sspr](https://aka.ms/sspr)** にアクセスしてください。

Azure Active Directory の機能である[セルフサービス パスワード リセット (SSPR)](https://www.youtube.com/watch?v=pS3XwfxJrMo) を使用すると、ユーザーは自分のパスワードをリセットすることができ、IT スタッフにヘルプを依頼する必要はありません。 ユーザーは場所や時間に関係なく、自分ですぐにブロックを解除して作業を続けることができます。 従業員が自分自身のブロックを解除できるようにすることで、組織としては、パスワード関連のほとんどの一般的な問題に対する非生産的な時間と高いサポート コストを削減できます。

SSPR の主な機能は次のとおりです。

* セルフサービスを使用すると、エンド ユーザーは、管理者またはヘルプデスクにサポートを求めなくても、期限切れまたは期限切れではないパスワードをリセットできます。
* [パスワード ライトバック](./concept-sspr-writeback.md)を使用すると、クラウドを介してオンプレミスのパスワードを管理し、アカウントのロックアウトを解決することができます。
* パスワード管理アクティビティ レポートでは、組織内で発生したパスワードのリセットおよび登録アクティビティの詳細が管理者に提供されます。

このデプロイ ガイドでは、SSPR のロールアウトを計画してテストする方法について説明します。

まず、SSPR の動作の概要を確認してから、デプロイに関するその他の考慮事項について説明します。

> [!div class="nextstepaction"]
> [セルフサービス パスワード リセット (SSPR) を有効にする](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>SSPR の詳細

SSPR の詳細を参照してください。 「[動作のしくみ:Azure AD のセルフサービス パスワード リセット のクイック スタート](./concept-sspr-howitworks.md)に関する記事をご覧ください。

### <a name="key-benefits"></a>主な利点

SSPR を有効にする主な利点は次のとおりです。

* **コスト管理**。 SSPR は、ユーザーが自分でパスワードをリセットできるようにすることで、IT サポート コストを削減します。 また、パスワードの紛失やロックアウトによって失われる時間も短縮されます。 

* **直感的なユーザー エクスペリエンス**。 ユーザーがパスワードをリセットし、任意のデバイスや場所から要求時にアカウントのブロックを解除できるようにする、直感的なワンタイム ユーザー登録プロセスを提供します。 SSPR を使用すると、ユーザーは迅速に作業を再開し、生産性を高めることができます。

* **柔軟性とセキュリティ**。 SSPR を使用すると、企業はクラウド プラットフォームが提供するセキュリティと柔軟性にアクセスできます。 管理者は、新しいセキュリティ要件に合わせて設定を変更し、サインインを中断せずにこれらの変更をユーザーにロールアウトすることができます。

* **堅牢な監査と使用状況追跡**。 組織は、ユーザーが自分のパスワードをリセットしている間も、ビジネス システムが安全であることを保証できます。 堅牢な監査ログには、パスワード リセット プロセスの各手順の情報が含まれます。 これらのログは API から入手でき、これによりユーザーは、選択したセキュリティ インシデントおよびイベント監視 (SIEM) システムにデータをインポートできます。

### <a name="licensing"></a>ライセンス

Azure Active Directory はユーザーごとのライセンスであり、機能を利用するには、各ユーザーに適切なライセンスが必要です。 SSPR にはグループベースのライセンスが推奨されます。 

エディションと機能を比較し、グループベースまたはユーザーベースのライセンスを有効にする場合は、「[Azure AD のセルフサービス パスワード リセットのライセンス要件](./concept-sspr-licensing.md)」をご覧ください。

価格の詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

### <a name="prerequisites"></a>前提条件

* 少なくとも試用版ライセンスが有効になっている、動作している Azure AD テナント。 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* グローバル管理者特権を持つアカウント。


### <a name="training-resources"></a>トレーニング リソース

| リソース| リンクと説明 |
| - | - |
| ビデオ| [IT のスケーラビリティ向上によるユーザーの支援](https://youtu.be/g9RpRnylxS8) 
| |[セルフサービス パスワード リセットとは](https://youtu.be/hc97Yx5PJiM)|
| |[セルフサービス パスワード リセットのデプロイ](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Azure AD で SSPR を有効にして構成する方法](https://www.youtube.com/watch?v=rA8TvhNcCvQ)|
| |[Azure AD でユーザーにセルフサービスのパスワード リセットを構成する方法](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Azure Active Directory のセキュリティ情報を登録する [ユーザーを準備する] 方法](https://youtu.be/gXuh0XS18wA) |
| オンライン コース|[Microsoft Azure Active Directory での ID の管理](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) SSPR を使用して、ユーザーに最新の保護されたエクスペリエンスを提供します。 特に、「[Azure Active Directory のユーザーとグループの管理](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)」モジュールを参照してください。 |
|Pluralsight の有料コース |[ID およびアクセス管理の問題](https://www.pluralsight.com/courses/identity-access-management-issues) 組織内で認識しておく必要がある IAM とセキュリティの問題について説明します。 特に、「その他の認証方法」モジュールを参照してください。|
| |[Microsoft Enterprise Mobility Suite の概要](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 認証、承認、暗号化、およびセキュリティで保護されたモバイル エクスペリエンスを実現する方法で、オンプレミスの資産をクラウドに拡張するためのベスト プラクティスについて説明します。 特に、「Microsoft Azure Active Directory Premium の高度な機能の構成」モジュールを参照してください。
|チュートリアル |[Azure AD のセルフサービス パスワード リセット のパイロット展開を完了する](./tutorial-enable-sspr.md) |
| |「[パスワード ライトバックを有効にする](./tutorial-enable-sspr-writeback.md)」を使用して、パスワード ライトバックを有効にする |
| |[Windows 10 のログイン画面からの Azure AD パスワード リセット](./howto-sspr-windows.md) |
| よく寄せられる質問|[パスワード管理に関するよく寄せられる質問 (FAQ)](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次の例では、一般的なハイブリッド環境向けのパスワード リセット ソリューションのアーキテクチャについて説明しています。

![ソリューション アーキテクチャの図](./media/howto-sspr-deployment//solutions-architecture.png)

ワークフローの説明

パスワードをリセットするためには、ユーザーは[パスワードのリセット用ポータル](https://aka.ms/sspr)にアクセスします。 ユーザーは、以前に登録した認証方法 (1 つまたは複数) を使用して、身元を証明する必要があります。 パスワードが正常にリセットされると、リセット プロセスが開始されます。

* クラウド専用ユーザーの場合、SSPR では新しいパスワードが Azure AD に格納されます。 

* ハイブリッド ユーザーの場合、SSPR では、パスワードは Azure AD Connect サービスを介してオンプレミスの Active Directory にライトバックされます。 

注:[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) が無効になっているユーザーの場合、SSPR では、パスワードはオンプレミスの Active Directory にのみ格納されます。

### <a name="best-practices"></a>ベスト プラクティス

SSPR と共に別の一般的なアプリケーションまたはサービスを組織にデプロイすることで、ユーザーがすばやく登録できるようにすることができます。 このアクションでは、大量のサインインが生成され、登録が促進されます。

SSPR をデプロイする前に、各パスワード リセット呼び出しの数と平均コストを決定することも選択できます。 このデプロイ後のデータを使用して、SSPR によって組織にもたらされる価値を示すことができます。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>SSPR と MFA に対する統合された登録を有効にする

組織では SSPR と多要素認証に対する統合された登録エクスペリエンスを有効にすることをお勧めします。 この統合された登録エクスペリエンスを有効にすると、ユーザーは登録情報を 1 回選択するだけで両方の機能を有効にすることができます。

統合された登録エクスペリエンスでは、SSPR と Azure AD Multi-Factor Authentication の両方を有効にする必要はありません。 登録を組み合わせると、組織ではより優れたユーザー エクスペリエンスを実現できます。 詳細については、[統合されたセキュリティ情報の登録](concept-registration-mfa-sspr-combined.md)に関する記事を参照してください

## <a name="plan-the-deployment-project"></a>デプロイ プロジェクトを計画する

お客様の環境でこのデプロイの戦略を決定するときは、お客様の組織のニーズを考慮してください。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者を関与させ](../fundamentals/active-directory-deployment-plans.md)、利害関係者およびそのプロジェクトでの入力と説明責任を文書化することで、プロジェクトでの利害関係者の役割をよく理解させます。

#### <a name="required-administrator-roles"></a>必要な管理者ロール


| ビジネス ロール/ペルソナ| Azure AD ロール (必要な場合) |
| - | - |
| レベル 1 ヘルプデスク| パスワード管理者 |
| レベル 2 ヘルプデスク| ユーザー管理者 |
| SSPR 管理者| 全体管理者 |


### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡する必要があります。 エンド ユーザー コミュニケーション戦略の計画方法に関するアイデアについては、[Microsoft ダウンロード センターにあるセルフサービス パスワード リセット のロールアウト資料](https://www.microsoft.com/download/details.aspx?id=56768)を確認してください。

### <a name="plan-a-pilot"></a>パイロットを計画する

SSPR の初期構成はテスト環境で行うことをお勧めします。 組織内のユーザーのサブセットに対して SSPR を有効にすることで、パイロット グループから始めてください。 「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md)」を参照してください。

グループを作成するには、[Azure Active Directory でグループを作成し、メンバーを追加する](../fundamentals/active-directory-groups-create-azure-portal.md)方法を参照してください。 

## <a name="plan-configuration"></a>構成を計画する

推奨値で SSPR を有効にするには、次の設定が必要です。

| 領域 | 設定 | 値 |
| --- | --- | --- |
| **SSPR のプロパティ** | セルフサービス パスワード リセット が有効 | パイロットの場合は **[選択済み]** グループ/運用環境の場合は **[すべて]** |
| **認証方法** | Authentication methods required to register (登録に必要な認証方法) | リセットのため必要な数より常に 1 つ多い値 |
|   | Authentication methods required to reset (リセットに必要な認証方法) | 1 つまたは 2 つ |
| **登録** | [サインイン時にユーザーに登録を求めますか] | はい |
|   | [ユーザーが認証情報を再確認するように求められるまでの日数] | 90 – 180 日 |
| **通知** | [パスワードのリセットについてユーザーに通知しますか] | はい |
|   | [他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知しますか] | はい |
| **カスタマイズ** | ヘルプデスク リンクのカスタマイズ | はい |
|   | カスタム ヘルプデスクの電子メールまたは URL | サポート サイトまたはメール アドレス |
| **オンプレミスの統合** | オンプレミスの AD へのパスワードの書き戻し | はい |
|   | パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可する | はい |

### <a name="sspr-properties"></a>SSPR のプロパティ

SSPR を有効にする場合は、パイロット環境で適切なセキュリティ グループを選択します。

* すべてのユーザーに SSPR 登録を適用する場合は、 **[すべて]** オプションを使用することをお勧めします。
* それ以外の場合は、適切な Azure AD または AD セキュリティ グループを選択してください。

### <a name="authentication-methods"></a>認証方法

SSPR が有効になっている場合、ユーザーが自分のパスワードをリセットできるのは、管理者が有効にしている認証方法にデータがある場合のみです。 方法には、電話、Authenticator アプリの通知、セキュリティの質問などがあります。詳細については、「[認証方法とは](./concept-authentication-methods.md)」を参照してください。

次の認証方法の設定が推奨されます。

* **[Authentication methods required to register]\(登録に必要な認証方法\)** を、リセットに必要な数より少なくとも 1 つ多い数に設定します。 複数の認証を許可すると、リセットが必要なときのユーザーの柔軟性が増します。

* **[リセットのために必要な方法の数]** を、組織に適したレベルに設定します。 1 つでは最小限の手間が必要ですが、2 つではセキュリティ ポスチャが増す可能性があります。 

注:ユーザーには、「[Azure Active Directory のパスワード ポリシーと制限](./concept-sspr-policy.md)」で構成されている認証方法が必要です。

### <a name="registration-settings"></a>登録設定

**[サインイン時にユーザーに登録を求めますか]** を **[はい]** に設定します。 この設定では、サインイン時にユーザーに登録を求めることで、すべてのユーザーが確実に保護されるようにします。

組織で短い期間が必要でない限り、 **[ユーザーが認証情報を再確認するように求められるまでの日数]** を **90** から **180** 日の範囲に設定します。

### <a name="notifications-settings"></a>通知の設定

**[パスワードのリセットについてユーザーに通知しますか]** と **[他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知しますか]** の両方を、 **[はい]** に構成します。 両方で **[はい]** を選択すると、パスワードがリセットされたことをユーザーが確実に認識することで、セキュリティが向上します。 また、1 人の管理者がパスワードを変更したときに、すべての管理者が認識できるようにもします。 ユーザーまたは管理者は、通知を受け取ったときに変更を開始していない場合は、すぐにセキュリティの問題の可能性を報告できます。

### <a name="customization-settings"></a>カスタマイズ設定

問題が発生したユーザーがすぐにヘルプを受けられるよう、ヘルプデスクのメールまたは URL をカスタマイズすることが重要です。 このオプションを、ユーザーがよく知っている一般的なヘルプデスクのメール アドレスまたは Web ページに設定します。 

詳細については、「[セルフサービス パスワード リセットのための Azure AD 機能のカスタマイズ](./howto-sspr-customization.md)」を参照してください。

### <a name="password-writeback"></a>パスワード ライトバック

**パスワード ライトバック** は、[Azure AD Connect](../hybrid/whatis-hybrid-identity.md) で有効になっていて、クラウドでのパスワード リセットを既存のオンプレミスのディレクトリにリアルタイムで書き戻します。 詳しくは、「[パスワード ライトバックとは](./concept-sspr-writeback.md)」をご覧ください

次の設定が推奨されます。

* **[オンプレミスの Active Directory へのパスワードの書き戻し]** が **[はい]** に設定されていることを確認します。 
* **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可する]** を **[はい]** に設定します。

既定では、Azure AD はパスワード リセットを実行するときにアカウントをロック解除します。

### <a name="administrator-password-setting"></a>管理者パスワード設定

管理者のアカウントは、アクセス許可が引き上げられています。 オンプレミスのエンタープライズ管理者またはドメイン管理者は、SSPR で自分のパスワードをリセットできません。 オンプレミスの管理者アカウントには、次の制限があります。

* オンプレミス環境でのみ、自分のパスワードを変更できます。
* パスワードをリセットする方法として、秘密の質問と回答を使用できません。

オンプレミスの Active Directory 管理者アカウントは Azure AD と同期させないことをお勧めします。

### <a name="environments-with-multiple-identity-management-systems"></a>複数の ID 管理システムがある環境

環境によっては、複数の ID 管理システムが存在する場合があります。 Oracle AM や SiteMinder などのオンプレミスの ID マネージャーでは、パスワードについて AD との同期が必要です。 これは、Microsoft Identity Manager (MIM) を使用したパスワード変更通知サービス (PCNS) のようなツールを使用することで実行できます。 このより複雑なシナリオについては、「[ドメイン コントローラーに MIM パスワード変更通知サービスを展開する](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)」をご覧ください。

## <a name="plan-testing-and-support"></a>テストとサポートを計画する

初期のパイロット グループから組織全体に至るまでのデプロイの各段階で、確実に期待どおりの結果が得られるようにします。

### <a name="plan-testing"></a>テストを計画する

デプロイが意図したとおりに動作することを確認するには、実装を検証にするテスト ケースのセットを計画します。 テスト ケースにアクセスするには、パスワードを持つ非管理者テスト ユーザーが必要です。 ユーザーを作成する必要がある場合は、[Azure Active Directory への新しいユーザーの追加](../fundamentals/add-users-azure-active-directory.md)に関するページを参照してください。

次の表では、ポリシーに基づいて組織で予想される結果を文書化するために使用できる有用なテスト シナリオを示します。
<br>


| ビジネス ケース| 予想される結果 |
| - | - |
| 企業ネットワーク内から SSPR ポータルにアクセスできる| 組織によって決定される |
| 企業ネットワーク外から SSPR ポータルにアクセスできる| 組織によって決定される |
| ユーザーがパスワード リセットを有効にされていないときに、ブラウザーからユーザーのパスワードをリセットする| ユーザーはパスワード リセット フローにアクセスできない |
| ユーザーがパスワード リセットに登録されていないときに、ブラウザーからユーザーのパスワードをリセットする| ユーザーはパスワード リセット フローにアクセスできない |
| パスワード リセットの登録が強制されているときに、ユーザーがサインインする| ユーザーにセキュリティ情報を登録するよう求める |
| パスワード リセットの登録が完了したら、ユーザーがサインインする| ユーザーにセキュリティ情報を登録するよう求める |
| ユーザーがライセンスを持っていないときに、SSPR ポータルにアクセスできる| アクセスできる |
| Windows 10 Azure AD 参加済みまたはハイブリッド Azure AD 参加済みデバイスのロック画面からユーザーのパスワードをリセットする| ユーザーはパスワードをリセットできる |
| SSPR の登録と使用状況のデータを、管理者がほぼリアルタイムで使用できる| 監査ログを介して利用できる |

[Azure AD のセルフサービス パスワード リセットのパイロット展開の完了](./tutorial-enable-sspr.md)に関するページも参照してください。 このチュートリアルでは、SSPR を組織にパイロット展開できるようにし、管理者以外のアカウントを使用してテストします。

### <a name="plan-support"></a>サポートを計画する

通常、SSPR ではユーザーの問題は発生しませんが、発生する可能性のある問題に対応できるようサポート スタッフを準備することが重要です。 管理者は Azure AD ポータルでエンド ユーザーのパスワードをリセットできますが、セルフサービス サポート プロセスを通して問題の解決を支援することをお勧めします。

お客様のサポート チームが成功できるように、ユーザーから受け取った質問に基づいて、FAQ を作成できます。 次に例をいくつか示します。

| シナリオ| 説明 |
| - | - |
| ユーザーに使用可能な登録済み認証方法がない| ユーザーは、自分のパスワードをリセットしようとしていますが、使用可能な登録済みの認証方法がありません (例: 携帯電話が自宅にあり、メールにアクセスできない) |
| ユーザーはオフィスまたは携帯電話でテキストまたは通話を受け取っていない| ユーザーは、テキストまたは通話により身元を確認しようとしていますが、テキスト/通話を受け取っていません。 |
| ユーザーはパスワード リセット ポータルにアクセスできない| ユーザーは、パスワードのリセットを望んでいますが、パスワードのリセットが有効になっておらず、パスワード更新ページにアクセスできません。 |
| ユーザーは新しいパスワードを設定できない| ユーザーは、パスワード リセット フローで検証を完了しましたが、新しいパスワードを設定できません。 |
| ユーザーの Windows 10 デバイスに [パスワードのリセット] リンクが表示されない| ユーザーは、Windows 10 のロック画面からパスワードをリセットしようとしていますが、デバイスが Azure AD に参加していないか、または Intune デバイス ポリシーが有効になっていません |

### <a name="plan-rollback"></a>ロールバックを計画する

デプロイをロールバックするには:

* 1 人のユーザーの場合は、セキュリティ グループからユーザーを削除します 

* グループの場合は、SSPR 構成からグループを削除します

* 全員の場合は、Azure AD テナントに対して SSPR を無効にします

## <a name="deploy-sspr"></a>SSPR をデプロイする

デプロイの前に、次の操作を完了済みであることを確認します。

1. [コミュニケーション計画](#plan-communications)を作成し、実行を開始した。

1. 適切な[構成設定](#plan-configuration)を決定した。

1. [パイロット](#plan-a-pilot)環境と運用環境のユーザーとグループを特定した。

1. 登録とセルフサービス用の[構成設定を決定](#plan-configuration)した。

1. ハイブリッド環境がある場合は、[パスワード ライトバック](#password-writeback)を構成した。


**これで、SSPR をデプロイする準備が整いました。**

次の領域の構成の詳細な手順については、「[セルフサービス パスワード リセットを有効にする](./tutorial-enable-sspr.md#enable-self-service-password-reset)」を参照してください。

1. [認証方法](./concept-authentication-methods.md)

1. [登録設定](./concept-registration-mfa-sspr-combined.md)

1. [通知設定](#notifications-settings)

1. [カスタマイズ設定](./howto-sspr-customization.md)

1. [オンプレミスの統合](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Windows で SSPR を有効にする
Windows 7、8、8.1、および 10 を実行中のコンピューターでは、[Windows のサインイン画面でユーザーが自分のパスワードをリセットできるように設定する](./howto-sspr-windows.md)ことができます

## <a name="manage-sspr"></a>SSPR を管理する

Azure AD では、監査とレポートによって SSPR のパフォーマンスに関する追加情報を提供できます。

### <a name="password-management-activity-reports"></a>パスワード管理アクティビティ レポート 

Azure portal で構築済みのレポートを使用して、SSPR のパフォーマンスを測定できます。 適切にライセンスを付与されている場合は、カスタム クエリを作成することもできます。 詳しくは、「[Azure AD のパスワード管理に関するレポート オプション](./howto-sspr-reporting.md)」を参照してください

> [!NOTE]
>  ユーザーは[グローバル管理者](../roles/permissions-reference.md)であること、および組織のためにこのデータを収集できるようにオプトインすることが必要です。 オプトインするには、Azure portal の [レポート] タブまたは監査ログに少なくとも 1 回アクセスする必要があります。 それまでは、ご自分の組織のデータは収集されません。

登録とパスワード リセットに関する監査ログは、30 日間利用できます。 企業内でのセキュリティ監査をもっと長い期間保有する必要がある場合は、ログをエクスポートし、[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk、ArcSight などの SIEM ツールに取り込む必要があります。

![SSPR レポートのスクリーンショット](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>認証方法 - 使用状況と分析情報

[使用状況と分析情報](./howto-authentication-methods-activity.md)を使うと、Azure AD MFA や SSPR などの機能の認証方法が組織内でどのように機能しているかについて理解を深めることができます。 このレポート機能は、組織がどの方法で登録を行い、それらをどのように使用しているかを把握するための手段となるものです。

### <a name="troubleshoot"></a>トラブルシューティング

* 「[セルフサービスのパスワードのリセットのトラブルシューティング](./troubleshoot-sspr.md)」を参照してください 

* 「[パスワード管理に関するよく寄せられる質問 (FAQ)](./active-directory-passwords-faq.md)」に従ってください 

### <a name="helpful-documentation"></a>役に立つドキュメント

* [認証方法とは](./concept-authentication-methods.md)

* [動作のしくみ: Azure AD のセルフサービスによるパスワードのリセット](./concept-sspr-howitworks.md)

* [セルフサービス パスワード リセットのための Azure AD 機能のカスタマイズ](./howto-sspr-customization.md)

* [Azure Active Directory のパスワード ポリシーと制限](./concept-sspr-policy.md)

* [パスワード ライトバックとは](./concept-sspr-writeback.md)

## <a name="next-steps"></a>次のステップ

* SSPR のデプロイを開始するには、[Azure AD のセルフサービス パスワード リセット を有効にする](tutorial-enable-sspr.md)に関するページを参照してください

* [Azure AD のパスワード保護の実装を検討する](./concept-password-ban-bad.md)

* [Azure AD スマート ロックアウトの実装を検討する](./howto-password-smart-lockout.md)