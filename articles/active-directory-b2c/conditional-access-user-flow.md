---
title: Azure AD B2C のユーザー フローに条件付きアクセスを追加する
description: Azure AD B2C のユーザー フローに条件付きアクセスを追加する方法について説明します。 ユーザー フローで多要素認証 (MFA) 設定と条件付きアクセス ポリシーを構成して、ポリシーを適用し、危険なサインインを修復します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033915"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローに条件付きアクセスを追加する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

対象の Azure Active Directory B2C (Azure AD B2C) ユーザー フローまたはカスタム ポリシーに条件付きアクセスを追加して、アプリケーションへの危険なサインインを管理できます。 Azure Active Directory (Azure AD) 条件付きアクセスは、シグナルをまとめ、決定を行い、組織のポリシーを適用するために Azure AD B2C によって使用されるツールです。

![条件付きアクセスのフロー](media/conditional-access-user-flow/conditional-access-flow.png)

ポリシー条件を使用してリスク評価を自動化すると、危険なサインインがただちに特定され、その後、修復またはブロックされます。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>サービスの概要

Azure AD B2C では、各サインイン イベントが評価され、すべてのポリシーの要件が満たされていることを確認したうえで、ユーザー アクセスが許可されます。 この **評価** フェーズでは、条件付きアクセス サービスによって、サインイン イベント時に Identity Protection のリスク検出で収集されたシグナルが評価されます。 この評価プロセスの結果は、サインインを許可するかブロックするかを示す一連の要求です。 Azure AD B2C ポリシーでは、これらの要求を使用して、ユーザー フロー内のアクションを実行します。たとえば、アクセスをブロックしたり、多要素認証 (MFA) などの特定の修復策を使用するようにユーザーに促したりします。 "アクセスのブロック" は、他のすべての設定よりも優先されます。

::: zone pivot="b2c-custom-policy"
サインインの脅威を評価するために使用される条件付きアクセス技術プロファイルの例を次に示します。

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

次の **修復** フェーズでは、ユーザーは MFA を求められます。 完了すると、Azure AD B2C によって、特定されたサインインの脅威が修復されたこととその方法が Identity Protection に通知されます。 この例では、Azure AD B2C によって、ユーザーが多要素認証の要求を正常に完了したことが通知されます。 

::: zone pivot="b2c-custom-policy"

特定された脅威を修復するために使用される条件付きアクセス技術プロファイルの例を次に示します。

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>ソリューションのコンポーネント

Azure AD B2C で条件付きアクセスを利用できるようにするコンポーネントを次に示します。

- **ユーザー フロー** また **カスタム ポリシー** - サインインおよびサインアップ プロセスでユーザーを案内します。
- **条件付きアクセス ポリシー** - シグナルをまとめ、決定を行い、組織のポリシーを適用します。 ユーザーが Azure AD B2C ポリシーを使用してアプリケーションにサインインすると、条件付きアクセス ポリシーでは Azure AD Identity Protection のシグナルを使用して危険なサインインが特定され、適切な修復アクションが提示されます。
- **登録済みのアプリケーション** - 適切な Azure AD B2C ユーザー フローまたはカスタム ポリシーにユーザーを誘導します。
- [TOR Browser](https://www.torproject.org/download/) - 危険なサインインをシミュレートします。

## <a name="service-limitations-and-considerations"></a>サービスの制限と考慮事項

Azure AD の条件付きアクセスを使用する場合は、次の点を考慮してください。

- Identity Protection は、ローカル ID と、Google や Facebook などのソーシャル ID の両方で使用できます。 ソーシャル ID の場合は、条件付きアクセスを手動でアクティブ化する必要があります。 ソーシャル アカウントの資格情報が外部の ID プロバイダーによって管理されているため、検出が制限されます。
- Azure AD B2C のテナントでは、[Azure AD 条件付きアクセス](../active-directory/conditional-access/overview.md) ポリシーのサブセットのみを使用できます。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Pricing tier

危険なサインインのポリシーを作成するには、Azure AD B2C **Premium 2** が必要です。 **Premium P1** のテナントでは、場所、アプリケーション、ユーザーベース、またはグループベースのポリシーに基づいてポリシーを作成できます。 詳細については、「[Azure AD B2C 価格レベルを変更する](billing.md#change-your-azure-ad-pricing-tier)」を参照してください

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを準備する

条件付きアクセス ポリシーを追加するには、セキュリティの既定値を無効にします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
3. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
4. **[プロパティ]** を選択し、次に **[セキュリティの既定値群の管理]** を選択します。

   ![セキュリティの既定値群を無効にする](media/conditional-access-user-flow/disable-security-defaults.png)

5. **[セキュリティの既定値群を有効にする]** で、 **[いいえ]** を選択します。

   ![[セキュリティの既定値群の有効化] トグルを [いいえ] に設定する](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>条件付きアクセス ポリシーを追加する

条件付きアクセス ポリシーとは、割り当てとアクセス制御の if then ステートメントです。 条件付きアクセス ポリシーでは、シグナルをまとめ、決定を行い、組織のポリシーを適用します。 割り当て間の論理演算子は *AND* です。 各割り当ての演算子は *OR* です。

![条件付きアクセスの割り当て](media/conditional-access-user-flow/conditional-access-assignments.png)

条件付きアクセス ポリシーを追加するには、次の手順を実行します。

1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[セキュリティ]** で、 **[条件付きアクセス (プレビュー)]** を選択します。 **[条件付きアクセス ポリシー]** ページが開きます。
1. **[新しいポリシー]** を選択します。
1. ポリシーの名前を入力します (例: "*危険なサインインのブロック*")。
1. **[割り当て]** で **[ユーザーとグループ]** を選択し、次のサポートされている構成のいずれかを選択します。

    |包含  |ライセンス | Notes  |
    |---------|---------|---------|
    |**すべてのユーザー** | P1、P2 |**[すべてのユーザー]** を含めることを選択した場合、このポリシーはすべてのユーザーに影響します。 自分自身をロックしないようにするには、 **[除外]** を選択し、 **[ディレクトリ ロール]** を選択してから、一覧で **[全体管理者]** を選択して、自分の管理者アカウントを除外します。 **[ユーザーとグループ]** を選択し、 **[対象外とするユーザーの選択]** の一覧で自分のアカウントを選択することもできます。  | 
 
1. **[クラウド アプリまたはアクション]** を選択し、 **[アプリを選択]** を選択します。 [証明書利用者アプリケーション](tutorial-register-applications.md)を参照します。

1. **[条件]** を選択し、次の条件から選択します。 たとえば、 **[サインイン リスク]** を選択し、 **[高]** 、 **[中]** 、 **[低]** のリスク レベルを選択します。
    
    |条件  |ライセンス  |Notes  |
    |---------|---------|---------|
    |**ユーザー リスク**|P2|ユーザー リスクは、特定の ID またはアカウントが侵害されているおそれがあることを表します。|
    |**[サインイン リスク]**|P2|サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。|
    |**デバイス プラットフォーム**|サポートされていません| デバイスで実行されているオペレーティング システムによって分類されます。 詳細については、「[デバイス プラットフォーム](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)」を参照してください。|
    |**場所**|P1、P2|名前付きの場所には、パブリック IPv4 ネットワーク情報、国、リージョンだけでなく、特定の国やリージョンにマップされていない不明な領域が含まれる場合もあります。 詳細については、「[場所](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations)」を参照してください。 |
 
1. **[アクセス制御]** で **[許可]** を選択します。 次に、アクセスをブロックするか許可するかを選択します。
    
    |オプション  |ライセンス |Note  |
    |---------|---------|---------|
    |**アクセスをブロックする**|P1、P2| この条件付きアクセス ポリシーに指定されている条件に基づいて、アクセスできないようにします。|
    |**[アクセス権の付与]** ( **[多要素認証を要求する]** を指定)|P1、P2|この条件付きアクセス ポリシーに指定されている条件に基づいて、ユーザーは Azure AD B2C の多要素認証を行うように要求されます。|

1. **[ポリシーの有効化]** で、次のいずれかを選択します。
    
    |オプション  |ライセンス |Note  |
    |---------|---------|---------|
    |**レポート専用**|P1、P2| レポート専用を使用すると、管理者は、環境で条件付きアクセス ポリシーを有効にする前に、その影響を評価できます。 この状態でポリシーを確認し、多要素認証の要求やユーザーのブロックを行うことなくエンド ユーザーへの影響を判断することをお勧めします。 詳細については、「[監査レポートで条件付きアクセスの結果を確認する](#review-conditional-access-outcomes-in-the-audit-report)」を参照してください|
    | **オン**| P1、P2| アクセス ポリシーが評価されますが、適用されません。 |
    | "**オフ**" | P1、P2| アクセス ポリシーはアクティブ化されず、ユーザーには影響しません。 |

1. **[作成]** を選択して、テスト用条件付きアクセス ポリシーを有効にします。

## <a name="add-conditional-access-to-a-user-flow"></a>ユーザー フローに条件付きアクセスを追加する

Azure AD 条件付きアクセス ポリシーを追加した後、ユーザー フローまたはカスタム ポリシーで条件付きアクセスを有効にします。 条件付きアクセスを有効にするときに、ポリシー名を指定する必要はありません。

複数の条件付きアクセス ポリシーは、いつでも個々のユーザーに適用される可能性があります。 この場合、最も厳格なアクセス制御ポリシーが優先されます。 たとえば、あるポリシーでは多要素認証 (MFA) を要求し、別のポリシーではアクセスをブロックする場合、ユーザーはブロックされます。

## <a name="enable-multi-factor-authentication-optional"></a>多要素認証を有効にする (オプション)

ユーザー フローに条件付きアクセスを追加する場合は、**多要素認証 (MFA)** の使用を検討してください。 ユーザーは、SMS または音声によるワンタイム コードか、メールによるワンタイム パスワードを多要素認証に使用できます。 MFA の設定は、条件付きアクセスの設定とは別になっています。 MFA を **[常にオン]** に設定すると、条件付きアクセスの設定に関係なく MFA が常に必須になります。 または、MFA を **[条件付き]** に設定すると、アクティブな条件付きアクセス ポリシーで MFA が求められる場合にのみ MFA が必須になります。

> [!IMPORTANT]
> 条件付きアクセス ポリシーで MFA によるアクセスが許可されていても、ユーザーが電話番号を登録していない場合は、そのユーザーがブロックされることがあります。

::: zone pivot="b2c-user-flow"

ユーザー フローの条件付きアクセスを有効にするには、条件付きアクセスがご使用のバージョンでサポートされていることを確認します。 これらのユーザー フローのバージョンには、"**推奨**" というラベルが付けられています。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。

1. **[ポリシー]** で **[ユーザー フロー]** を選択します。 次に、ユーザー フローを選択します。

1. **[プロパティ]** を選択し、このユーザー フローで条件付きアクセスがサポートされていることを確認します。そのためには、 **[条件付きアクセス]** というラベルの設定を探します。
 
   ![[プロパティ] で MFA と条件付きアクセスを構成する](media/conditional-access-user-flow/add-conditional-access.png)

1. **[多要素認証]** セクションで、目的の **MFA メソッド** を選択し、 **[MFA の適用]** で **[条件付き (推奨)]** を選択します。
 
1. **[条件付きアクセス]** セクションで、 **[Enforce conditional access policies]\(条件付きアクセス ポリシーを適用する\)** チェック ボックスをオンにします。

1. **[保存]** を選択します。


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>ポリシーに条件付きアクセスを追加する

1. 条件付きアクセスポリシーの例については、[GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access) を参照してください。
1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。
1. ポリシー ファイルをアップロードします。

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. `B2C_1A_signup_signin_with_ca` または `B2C_1A_signup_signin_with_ca_whatif` ポリシーを選択して、その概要ページを開きます。 次に、 **[ユーザー フローの実行]** を選択します。 **[アプリケーション]** で、 *[webapp1]* を選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フロー エンドポイントを実行]** の URL をコピーします。

1. 危険なサインインをシミュレートするには、[Tor Browser](https://www.torproject.org/download/) を開き、前の手順でコピーした URL を使用して登録済みのアプリにサインインします。

1. サインイン ページに要求された情報を入力し、サインインを試みます。 トークンが `https://jwt.ms` に返され、表示されます。 jwt.ms のデコードされたトークンで、サインインがブロックされたことがわかります。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開いてから、 **[ユーザー フローの実行]** を選択します。 **[アプリケーション]** で、 *[webapp1]* を選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。

1. **[ユーザー フロー エンドポイントを実行]** の URL をコピーします。

1. 危険なサインインをシミュレートするには、[Tor Browser](https://www.torproject.org/download/) を開き、前の手順でコピーした URL を使用して登録済みのアプリにサインインします。

1. サインイン ページに要求された情報を入力し、サインインを試みます。 トークンが `https://jwt.ms` に返され、表示されます。 jwt.ms のデコードされたトークンで、サインインがブロックされたことがわかります。

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>監査レポートで条件付きアクセスの結果を確認する

条件付きアクセス イベントの結果を確認するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

3. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。

4. **[活動]** で、 **[監査ログ]** を選択します。

5. **[カテゴリ]** を **[B2C]** に設定し、 **[アクティビティのリソースの種類]** を **[IdentityProtection]** に設定することで、監査ログをフィルター処理します。 次に、**[適用]** を選択します。

6. 最大で過去 7 日間の監査アクティビティを確認します。 次の種類のアクティビティが含まれています。

   - **[Evaluate conditional access policies]\(条件付きアクセス ポリシーを評価する\)** : この監査ログ エントリは、認証中に条件付きアクセスの評価が実行されたことを示しています。
   - **[ユーザーを修復する]** : このエントリは、エンド ユーザーが条件付きアクセス ポリシーの許可または要件を満たしていたことを示します。このアクティビティは、ユーザーのリスクを軽減 (リスクの緩和) するためにリスク エンジンに報告されています。

7. 一覧で **[Evaluate conditional access policies]\(条件付きアクセス ポリシーを評価する\)** ログ エントリを選択して、 **[アクティビティの詳細: 監査ログ]** ページを開きます。このページでは、監査ログ識別子と共に次の情報が **[追加の詳細]** セクションに表示されます。

   - **ConditionalAccessResult**: 条件付きポリシーの評価に必要な許可。
   - **AppliedPolicies**: 条件が満たされ、ポリシーがオンになっているすべての条件付きアクセス ポリシーの一覧。
   - **ReportingPolicies**: レポート専用モードに設定され、条件が満たされていた条件付きアクセス ポリシーの一覧。

## <a name="next-steps"></a>次のステップ

[Azure AD B2C ユーザー フローのユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)
