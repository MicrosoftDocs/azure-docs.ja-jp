---
title: Azure AD B2C での電話ベースの MFA のセキュリティ保護
titleSuffix: Azure AD B2C
description: Azure Monitor Log Analytics レポートおよびアラートを使用して、Azure AD B2C テナントの電話ベースの多要素認証 (MFA) をセキュリティで保護するためのヒントについて説明します。 ブックを使用して、不正な電話認証を特定し、不正なサインアップを軽減します。 =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033556"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>電話ベースの多要素認証 (MFA) のセキュリティ保護

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory (Azure AD) 多要素認証 (MFA) によって、ユーザーは、検証用に登録した電話番号で自動音声通話を受信するように選択できます。 悪意のあるユーザーは、複数のアカウントを作成し、MFA 登録プロセスを実行することなく電話をかけることで、この方法を利用できます。 こうした大量の失敗したサインアップにより、許可されたサインアップ試行が使い果たされ、他のユーザーが Azure AD B2C テナントの新しいアカウントにサインアップすることが妨げられる可能性があります。 これらの攻撃から保護するために、Azure Monitor を使用して、電話認証の失敗を監視し、不正なサインアップを軽減できます。

## <a name="prerequisites"></a>前提条件

始める前に、[Log Analytics ワークスペース](azure-monitor.md)を作成します。

## <a name="create-a-phone-based-mfa-events-workbook"></a>電話ベースの MFA イベント ブックを作成する

GitHub の [Azure AD B2C レポートとアラート](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) リポジトリには、Azure AD B2C ログに基づいてレポート、アラート、およびダッシュボードを作成して、公開するために使用できる成果物が格納されています。 次に示すドラフトのブックは、電話関連のエラーを強調表示しています。

### <a name="overview-tab"></a>[概要] タブ

**[概要]** タブには、次の情報が表示されます。

- 失敗の理由 (特定の理由ごとの失敗した電話認証の合計数)
- 評判が悪いためにブロックされた
- 電話認証が失敗した IP アドレス (特定の IP アドレスごとの失敗した電話認証の合計数)
- IP アドレス付きの電話番号 - 失敗した電話認証
- ブラウザー (クライアント ブラウザーごとの電話認証の失敗)
- オペレーティング システム (クライアント オペレーティング システムごとの電話認証の失敗)

![[概要] タブ](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>[詳細] タブ

**[詳細]** タブでは、次の情報が報告されます。

- Azure AD B2C ポリシー - 失敗した電話認証
- 電話番号別の電話認証の失敗 - 時間グラフ (調整可能タイムライン)
- Azure AD B2C ポリシー別の電話認証の失敗 - 時間グラフ (調整可能タイムライン)
- IP アドレス別の電話認証の失敗 - 時間グラフ (調整可能タイムライン)
- 失敗の詳細を表示する電話番号の選択 (失敗の詳細な一覧については、電話番号を選択)

![[詳細] タブ 1/3](media/phone-based-mfa/details-tab-1.png)

![[詳細] タブ 2/3](media/phone-based-mfa/details-tab-2.png)

![[詳細] タブ 3/3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>ブックを使用して不正なサインアップを特定する

ブックを使用して、電話ベースの MFA イベントを確認し、テレフォニー サービスの悪意のある可能性がある使用を特定できます。

1. 次の質問に答えることで、テナントの通常の状態を確認します。

   - 電話ベースの MFA が予想されるリージョンはどこですか。
   - 失敗した電話ベースの MFA 試行について表示された理由を調べます。これらは通常または予想されたものですか。

2. 不正なサインアップの特性を認識します。

   - **場所ベース**:ユーザーのサインアップを予期していない場所に関連付けられているアカウントについて、**IP アドレス別の電話認証の失敗** を調べます。

   > [!NOTE]
   > 提供される IP アドレスは、おおよそのリージョンです。

   - **ベロシティベース**:**長時間の失敗した電話認証 (1 日あたり)** を確認します。これは、1 日あたりの失敗した電話認証試行が異常な回数になっている電話番号を最高 (左) から最低 (右) の順序で示しています。

3. 次のセクションの手順に従って、不正なサインアップを軽減します。
 

## <a name="mitigate-fraudulent-sign-ups"></a>不正なサインアップを軽減する

次のアクションを実行して、不正なサインアップの軽減に役立てます。

- ユーザー フローの **推奨される** バージョンを使用して、次の操作を実行します。
     
   - MFA の[電子メール ワンタイム パスコード機能 (OTP) を有効にします](phone-authentication-user-flows.md) (サインアップ フローとサインイン フローの両方に適用されます)。
   - 場所に基づくサインインをブロックするように、[条件付きアクセス ポリシーを構成します](conditional-access-user-flow.md) (サインイン フローのみに適用され、サインアップ フローには適用されません)。
   - API コネクタを使用して、[reCAPTCHA などのアンチボット ソリューションと統合します](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (サインアップ フローに適用されます)。

- ユーザーが電話番号を検証するドロップダウン メニューから、組織に関係のない国番号を削除します (この変更は今後のサインアップに適用されます)。
    
   1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com) にサインインします。

   2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。

   3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。

   4. ユーザー フローを選択し、 **[言語]** を選択します。 組織の地理的な場所の言語を選択して、言語の詳細パネルを開きます。 (この例では、米国の場合の **[英語 en]** を選択します)。 **[多要素認証] ページ** を選択し、 **[既定値のダウンロード (en)]** を選択します。
 
      ![新しいオーバーライドをアップロードして既定値をダウンロードする](media/phone-based-mfa/download-defaults.png)

   5. 前の手順でダウンロードした JSON ファイルを開きます。 ファイルで `DEFAULT` を検索し、行を `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` に置き換えます。 `Overrides` を `true` に設定してください。

   > [!NOTE]
   > `countryList` 要素で許可されている国番号の一覧をカスタマイズできます (「[電話ファクター認証ページの例](localization-string-ids.md#phone-factor-authentication-page-example)」を参照してください)。

   7. JSON ファイルを保存します。 言語の詳細パネルの、 **[新しいオーバーライドのアップロード]** で、変更済みの JSON ファイルを選択してアップロードします。

   8. パネルを閉じ、 **[ユーザー フローを実行します]** を選択します。 この例では、 **[米国]** が、ドロップダウンで使用できる唯一の国番号であることを確認します。
 
      ![国番号のドロップダウン](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C の Identity Protection と条件付きアクセス](conditional-access-identity-protection-overview.md)について確認します 

- [Azure Active Directory B2C のユーザー フローに条件付きアクセス](conditional-access-user-flow.md)を適用します