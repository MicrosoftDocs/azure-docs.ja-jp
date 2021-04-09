---
title: SSPR と Azure AD Multi-Factor Authentication のための統合された登録 - Azure Active Directory
description: ユーザーが Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの両方に登録できるようになる Azure Active Directory の統合された登録エクスペリエンスについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112ad0714c84cd3be08788b3277f52372f6d0373
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938473"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory での統合されたセキュリティ情報の登録の概要

統合された登録の前、ユーザーは Azure AD Multi-Factor Authentication (MFA) とセルフサービス パスワード リセット (SSPR) の認証方法を別々に登録しました。 ユーザーは Multi-Factor Authentication と SSPR に同様の方法が使用されることに困惑しましたが、どちらの機能も登録する必要がありました。 現在では、統合された登録を使用することで、ユーザーは 1 回登録して Multi-Factor Authentication と SSPR の両方の利点を得ることができます。

> [!NOTE]
> 2020 年 8 月 15 日以降は、すべての新しい Azure AD テナントで、統合された登録が自動的に有効になります。 

この記事では、統合されたセキュリティ登録の概要について説明します。 統合されたセキュリティ登録の使用を開始するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [結合されたセキュリティ登録を有効にする](howto-registration-mfa-sspr-combined.md)

![ユーザーの登録済みのセキュリティ情報を示しているマイ アカウント](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

新しいエクスペリエンスを有効にする前に、この管理者対象のドキュメントとユーザー対象のドキュメントを確認して、この機能とその影響を確実に理解するようにしてください。 [ユーザー ドキュメント](../user-help/security-info-setup-signin.md)に基づいたトレーニングによってユーザーが新しいエクスペリエンスに対して準備できるようにし、ロールアウトの成功に役立ててください。

Azure AD の結合されたセキュリティ情報の登録は、Azure Germany や Azure China 21Vianet などの各国のクラウドでは現在利用できません。 Azure US Government では使用できます。

> [!IMPORTANT]
> 元のプレビューと拡張版の両方の統合された登録エクスペリエンスが有効になっているユーザーには、新しい動作が示されます。 両方のエクスペリエンスが有効になっているユーザーには [マイ アカウント] エクスペリエンスのみが表示されます。 *[マイ アカウント]* は統合された登録の外観と統一されており、ユーザーにシームレスなエクスペリエンスを提供します。 ユーザーは、[https://myaccount.microsoft.com](https://myaccount.microsoft.com) に移動することによって [マイ アカウント] を表示できます。
>
> セキュリティ情報オプションにアクセスしようとすると、「申し訳ございません。サインインできませんでした」などのエラー メッセージが表示される場合があります。 サードパーティの Cookie をブロックする構成またはグループ ポリシー オブジェクトが Web ブラウザーに設定されていないことを確認します。

*[マイ アカウント]* ページは、そのページにアクセスしているコンピューターの言語設定に基づいてローカライズされます。 Microsoft は、ページへの以降のアクセスの試みが引き続き最後に使用された言語でレンダリングされるように、利用された最新の言語をブラウザー キャッシュに格納します。 キャッシュをクリアすると、ページは再レンダリングされます。

強制的に特定の言語にする場合は、URL の末尾に `?lng=<language>` を追加することができます。`<language>` は、レンダリングする言語のコードです。

![SSPR またはその他のセキュリティ検証方法をセットアップする](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>統合された登録で使用できる方法

統合された登録は、次の認証方法とアクションをサポートしています。

| Method | [登録] | Change | 削除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | はい (最大 5) | いいえ | はい |
| その他の認証アプリ | はい (最大 5) | いいえ | はい |
| ハードウェア トークン | いいえ | いいえ | はい |
| Phone | はい | はい | はい |
| Alternate phone | はい | はい | はい |
| 会社電話 | はい | はい | はい |
| Email | はい | はい | はい |
| セキュリティの質問 | はい | いいえ | はい |
| アプリ パスワード | はい | いいえ | はい |
| FIDO2 セキュリティ キー<br />*[[セキュリティ情報]](https://mysignins.microsoft.com/security-info) ページからの管理モードのみ*| はい | はい | はい |

> [!NOTE]
> アプリ パスワードは、Multi-Factor Authentication が適用されているユーザーのみが使用できます。 条件付きアクセス ポリシーによって Multi-Factor Authentication が有効になっているユーザーはアプリ パスワードを使用できません。

ユーザーは、既定の Multi-Factor Authentication 方法として、次のオプションのいずれかを設定できます。

- Microsoft Authenticator – 通知。
- 認証アプリまたはハードウェア トークン – コード。
- 音声通話。
- テキスト メッセージ。

今後も引き続き Azure AD にさらに多くの認証方法を追加していくため、統合された登録でこれらの方法を使用できるようになります。

## <a name="combined-registration-modes"></a>統合された登録のモード

統合された登録には、中断と管理の 2 つのモードがあります。

- **中断モード** は、ウィザードに似たエクスペリエンスであり、ユーザーがサインイン時に自分のセキュリティ情報を登録または更新するときにユーザーに表示されます。
- **管理モード** は、ユーザーのプロファイルの一部であり、ユーザーが自分のセキュリティ情報を管理できるようにします。

どちらのモードでも、Multi-Factor Authentication に使用できる方法を既に登録しているユーザーが自分のセキュリティ情報にアクセスするには、Multi-Factor Authentication を実行する必要があります。 ユーザーは、以前に登録したメソッドの使用を続ける前に、自分の情報を確認する必要があります。 

### <a name="interrupt-mode"></a>中断モード

統合された登録は、Multi-Factor Authentication と SSPR の両方のポリシーに従います (テナントで両方が有効になっている場合)。 これらのポリシーは、ユーザーがサインイン中に登録を中断されるかどうか、および登録にどの方法を使用できるかを制御します。

ユーザーが自分のセキュリティ情報を登録または更新するよう求められる可能性があるサンプル シナリオを次に示します。

- *Identity Protection によって Multi-Factor Authentication の登録が強制されている:* ユーザーは、サインイン中に登録するよう求められます。 ユーザーは Multi-Factor Authentication 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
- *ユーザーごとの Multi-Factor Authentication によって Multi-Factor Authentication の登録が強制されている:* ユーザーは、サインイン中に登録するよう求められます。 ユーザーは Multi-Factor Authentication 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
- *条件付きアクセス ポリシーまたはその他のポリシーによって Multi-Factor Authentication の登録が強制されている:* ユーザーは、Multi-Factor Authentication を必要とするリソースを使用するときに登録するよう求められます。 ユーザーは Multi-Factor Authentication 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
- *SSPR の登録が強制されている:* ユーザーは、サインイン中に登録するよう求められます。 ユーザーは SSPR 方法のみを登録します。
- *SSPR の更新が強制されている:* ユーザーは、管理者によって設定された間隔で自分のセキュリティ情報を確認する必要があります。ユーザーには自分の情報が表示され、現在の情報を確認するか、または必要に応じて変更を行うことができます。

登録が適用されると、ユーザーには、Multi-Factor Authentication と SSPR の両方のポリシーに準拠するために必要な最小数の方法が安全性の高い順に表示されます。

次のシナリオ例について考えてみます。

- ユーザーが SSPR に対して有効になっています。 SSPR ポリシーはリセットするための 2 つの方法を必要とし、モバイル アプリ コード、電子メール、および電話を有効にしています。
- このユーザーは 2 つの方法を登録する必要があります。
   - 既定では、ユーザーには認証アプリと電話が表示されます。
   - ユーザーは、認証アプリまたは電話の代わりに電子メールを登録することを選択できます。

次のフローチャートは、サインイン中に登録を中断されたときにユーザーにどの方法が表示されるかを示しています。

![結合されたセキュリティ情報のフローチャート](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Multi-Factor Authentication と SSPR の両方が有効になっている場合は、Multi-Factor Authentication の登録を適用することをお勧めします。

SSPR ポリシーでユーザーが定期的に自分のセキュリティ情報を確認する必要がある場合、ユーザーはサインイン中に中断され、自分が登録したすべての方法が表示されます。 ユーザーは、現在の情報が最新かどうかを確認することも、必要な場合は変更することもできます。 ユーザーは、このページにアクセスするときに多要素認証を実行する必要があります。

### <a name="manage-mode"></a>管理モード

ユーザーは [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動するか、または [マイ アカウント] から **[セキュリティ情報]** を選択することによって管理モードにアクセスできます。 そこから、ユーザーは方法の追加、既存の方法の削除または変更、既定の方法の変更などを実行できます。

## <a name="key-usage-scenarios"></a>主な使用シナリオ

### <a name="set-up-security-info-during-sign-in"></a>サインイン中にセキュリティ情報を設定する

管理者が登録を適用しています。

ユーザーは、必要なすべてのセキュリティ情報をまだ設定していない状態で Azure portal に移動します。 ユーザー名とパスワードを入力した後、ユーザーはセキュリティ情報を設定するよう求められます。 ユーザーは次に、ウィザードに示されている手順に従って、必要なセキュリティ情報を設定します。 ユーザーは、設定によって許可される場合、既定で表示されるもの以外の方法を設定することを選択できます。 ウィザードが完了した後、ユーザーは、設定した方法と Multi-Factor Authentication の既定の方法を確認します。 設定プロセスを完了するために、ユーザーは情報を確認し、Azure Portal に進みます。

### <a name="set-up-security-info-from-my-account"></a>[マイ アカウント] からセキュリティ情報を設定する

管理者は登録を適用していません。

必要なすべてのセキュリティ情報をまだ設定していないユーザーが [https://myaccount.microsoft.com](https://myaccount.microsoft.com) に移動します。 ユーザーは左側のウィンドウで **[セキュリティ情報]** を選択します。 そこから、ユーザーは方法を追加することを選択し、自分が使用できるいずれかの方法を選択した後、手順に従ってその方法を設定します。 完了すると、設定された方法が [セキュリティ情報] ページに表示されます。

### <a name="delete-security-info-from-my-account"></a>[マイ アカウント] からセキュリティ情報を削除する

少なくとも 1 つの方法を以前に設定しているユーザーが [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動します。 ユーザーは、以前に登録された方法のいずれかを削除することを選択します。 完了すると、その方法は [セキュリティ情報] ページに表示されなくなります。

### <a name="change-the-default-method-from-my-account"></a>[マイ アカウント] から既定の方法を変更する

Multi-Factor Authentication に使用できる少なくとも 1 つの方法を以前に設定しているユーザーが [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動します。 ユーザーは、現在の既定の方法を別の既定の方法に変更します。 完了すると、新しい既定の方法が [セキュリティ情報] ページに表示されます。

## <a name="next-steps"></a>次のステップ

最初に、[セルフサービス パスワード リセットを有効にする](tutorial-enable-sspr.md)チュートリアルと、[Azure AD Multi-Factor Authentication を有効にする](tutorial-enable-azure-mfa.md)チュートリアルを参照してください。

[テナントでの統合された登録を有効にする](howto-registration-mfa-sspr-combined.md)方法、または[ユーザーに認証方法の再登録を強制する](howto-mfa-userdevicesettings.md#manage-user-authentication-options)方法について説明します。

また、[Azure AD Multi-Factor Authentication と SSPR で使用可能な方法](concept-authentication-methods.md)を確認することもできます。
