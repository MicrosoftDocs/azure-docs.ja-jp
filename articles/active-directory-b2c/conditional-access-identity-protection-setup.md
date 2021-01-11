---
title: Azure AD B2C で Identity Protection と条件付きアクセスを設定する
description: Azure AD B2C テナントに対して Identity Protection と条件付きアクセスを構成して、危険なサインインやその他のリスク イベントを表示し、リスク検出に基づいてポリシーを作成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5554cfcde9aba1b0e5c9c8b60e2e6a7e9a8ba378
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270713"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Azure AD B2C で Identity Protection と条件付きアクセスを設定する

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection は、ご自分の Azure AD B2C テナントに継続的なリスク検出を提供するものです。 Azure AD B2C テナントの価格レベルが Premium P2 の場合、Azure portal で Identity Protection の詳細なリスク イベントを表示できます。 これらのリスク検出に基づく[条件付きアクセス](../active-directory/conditional-access/overview.md) ポリシーを使用して、アクションを決定し、組織のポリシーを適用することもできます。

## <a name="prerequisites"></a>前提条件

- 対象の Azure AD B2C テナントが [Azure AD サブスクリプションにリンクされている](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)必要があります。
- サインインとユーザーのリスクベースの条件付きアクセスを使用するには、Azure AD B2C Premium P2 が必要です。 必要に応じて、[Azure AD B2C の価格レベルを Premium P2 に変更](https://aka.ms/exid-pricing-tier)してください。 
- 対象の B2C テナントで Identity Protection と条件付きアクセスを管理するには、全体管理者の役割またはセキュリティ管理者の役割が割り当てられているアカウントが必要です。
- 対象のテナントでこれらの機能を使用するには、最初に Azure AD B2C Premium P2 価格レベルに切り替える必要があります。

## <a name="set-up-identity-protection"></a>Identity Protection を設定する

Identity Protection は既定でオンになっています。 Azure AD B2C テナントで Identity Protection のリスク イベントを表示できるようにするには、対象の Azure AD B2C テナントを Azure AD サブスクリプションにリンクし、Azure AD B2C Premium P2 価格レベルを選択するだけで済みます。 Azure portal で詳細なリスク イベント レポートを表示できます。

### <a name="supported-identity-protection-risk-detections"></a>サポートされている Identity Protection のリスク検出

Azure AD B2C では、現在、次のリスク検出がサポートされています。  

|リスク検出の種類  |説明  |
|---------|---------|
| 特殊な移動     | ユーザーの最近のサインインに基づき特殊と判断された場所からのサインイン。        |
|匿名 IP アドレス     | 匿名の IP アドレスからのサインイン (例:Tor Browser、Anonymizer VPN)        |
|通常とは異なるサインイン プロパティ     | 指定されたユーザーで最近観察されていないプロパティを使用したサインイン。        |
|マルウェアにリンクした IP アドレス     | マルウェアにリンクした IP アドレスからのサインイン         |
|Azure AD 脅威インテリジェンス     | Microsoft の内部および外部の脅威インテリジェンス ソースが既知の攻撃パターンを特定しました        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントのリスク イベントを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。

1. **[セキュリティ]** で、 **[危険なユーザー (プレビュー)]** を選択します。

   ![危険なユーザー](media/conditional-access-identity-protection-setup/risky-users.png)

1. **[セキュリティ]** で、 **[リスク検出 (プレビュー)]** を選択します。

   ![リスク検出](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>条件付きアクセス ポリシーを追加する 

Identity Protection のリスク検出に基づいて条件付きアクセス ポリシーを追加するには、対象の Azure AD B2C テナントに対するセキュリティの既定値群が無効になっていることを確認してから、条件付きアクセス ポリシーを作成します。

### <a name="to-disable-security-defaults"></a>セキュリティの既定値群を無効にするには

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

3. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

4. **[プロパティ]** を選択し、次に **[セキュリティの既定値群の管理]** を選択します。

   ![セキュリティの既定値群を無効にする](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. [セキュリティの既定値群の有効化] で、[いいえ] を選択します。 

   ![[セキュリティの既定値群の有効化] トグルを [いいえ] に設定する](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成するには

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。

1. **[セキュリティ]** で、 **[条件付きアクセス (プレビュー)]** を選択します。 **[条件付きアクセス ポリシー]** ページが開きます。 

1. **[新しいポリシー]** を選択し、Azure AD 条件付きアクセスのドキュメントに従って新しいポリシーを作成します。 次に例を示します。

   - [サインイン リスクベースの条件付きアクセス: 条件付きアクセス ポリシーを有効にする](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > ポリシーを適用するユーザーを選択する際に、 **[すべてのユーザー]** を単独で選択しないでください。選択すると、自分がサインインできなくなる可能性があります。

## <a name="test-the-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

1. 上記の条件付きアクセス ポリシーを次の設定で作成します。
   
   - **[ユーザーとグループ]** で、テスト ユーザーを選択します。 **[すべてのユーザー]** を選択しないでください。選択すると、自分がサインインできなくなります。
   - **[クラウド アプリまたは操作]** で、 **[アプリの選択]** を選択し、次に証明書利用者アプリケーションを選択します。
   - [条件] で、 **[サインイン リスク]** を選択し、 **[高]** 、 **[中]** 、 **[低]** のリスク レベルを選択します。
   - **[付与]** で、 **[アクセスをブロックする]** を選択します。

      ![[アクセスをブロックする] を選択する](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. **[作成]** を選択して、テスト用条件付きアクセス ポリシーを有効にします。

1. [Tor Browser](https://www.torproject.org/download/) を使用して、危険なサインインをシミュレートします。 

1. 試行されたサインインに対応する jwt.ms のデコードされたトークンで、サインインがブロックされたことがわかります。

   ![ブロックされたサインインをテストする](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>監査レポートで条件付きアクセスの結果を確認する

条件付きアクセス イベントの結果を確認するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

3. Azure portal で、 **[Azure AD B2C]** を検索して選択します。

4. **[活動]** で、 **[監査ログ]** を選択します。

5. **[カテゴリ]** を **[B2C]** に設定し、 **[アクティビティのリソースの種類]** を **[IdentityProtection]** に設定することで、監査ログをフィルター処理します。 次に、**[適用]** を選択します。

6. 最大で過去 7 日間の監査アクティビティを確認します。 次の種類のアクティビティが含まれています。

   - **[Evaluate conditional access policies]\(条件付きアクセス ポリシーを評価する\)** : この監査ログ エントリは、認証中に条件付きアクセスの評価が実行されたことを示しています。
   - **[ユーザーを修復する]** : このエントリは、エンド ユーザーが条件付きアクセス ポリシーの許可または要件を満たしていたことを示します。このアクティビティは、ユーザーのリスクを軽減 (緩和) するためにリスク エンジンに報告されています。

7. 一覧で **[Evaluate conditional access policies]\(条件付きアクセス ポリシーを評価する\)** ログ エントリを選択して、 **[アクティビティの詳細: 監査ログ]** ページを開きます。このページでは、監査ログ識別子と共に次の情報が **[追加の詳細]** セクションに表示されます。

   - ConditionalAccessResult: 条件付きポリシーの評価に必要な許可。
   - AppliedPolicies: 条件が満たされ、ポリシーがオンになっているすべての条件付きアクセス ポリシーの一覧。
   - ReportingPolicies: レポート専用モードに設定され、条件が満たされていた条件付きアクセス ポリシーの一覧。

## <a name="next-steps"></a>次のステップ

[ユーザー フローに条件付きアクセスを追加する](conditional-access-user-flow.md)。
