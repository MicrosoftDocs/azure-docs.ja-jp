---
title: Azure AD Domain Services でのパスワード ポリシーの作成と使用 | Microsoft Docs
description: Azure AD DS マネージド ドメインで、細かい設定が可能なパスワード ポリシーを使用してアカウント パスワードをセキュリティで保護し、制御する方法とその理由について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617202"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー

Azure Active Directory Domain Services (Azure AD DS) でアカウントのセキュリティを管理するには、パスワードの最小文字数、パスワードの有効期限、パスワードの複雑さなどの設定を制御する細かい設定が可能なパスワード ポリシーを定義します。 既定のパスワード ポリシーは、Azure AD DS マネージド ドメイン内のすべてのユーザーに適用されます。 きめ細かく制御し、特定のビジネスまたはコンプライアンスのニーズに対応するために、追加のポリシーを作成し、特定のユーザー グループに適用することができます。

この記事では、Active Directory 管理センターを使用して細かい設定が可能なパスワード ポリシーを作成し、構成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
  * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
  * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
  * 必要であれば、[Azure Active Directory Domain Services インスタンスを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了してください。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
  * 必要に応じて、[管理 VM を作成する][tutorial-create-management-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="fine-grained-password-policies-fgpp-overview"></a>細かい設定が可能なパスワード ポリシー (FGPP) の概要

細かい設定が可能なパスワード ポリシー (FGPP) を使用すると、パスワードおよびアカウント ロックアウトのポリシーに対して、ドメイン内の異なるユーザーに特定の制限を適用できます。 たとえば、特権アカウントをセキュリティで保護するために、通常の特権のないアカウントよりも厳格なパスワード設定を適用できます。 複数の FGPP を作成して、Azure AD DS マネージド ドメイン内でパスワード ポリシーを指定することができます。

FGPP を使用して、次のパスワード設定を構成できます。

* パスワードの最小文字数
* パスワードの履歴
* 複雑さの要件を満たす必要があるパスワード
* パスワードの変更禁止期間
* パスワードの有効期間
* アカウント ロックアウト ポリシー
  * アカウントのロックアウト期間
  * 許可される失敗したログオン試行回数
  * 失敗したログオン試行回数のカウントがリセットされるまでの時間

FGPP は、Azure AD DS で作成されたユーザーにのみ影響します。 Azure AD から Azure AD DS マネージド ドメインに同期されたクラウド ユーザーとドメイン ユーザーは、パスワード ポリシーの影響を受けません。

ポリシーは、Azure AD DS マネージド ドメイン内のグループの関連付けを通じて配布されます。すべての変更が、次回のユーザーのサインイン時に適用されます。 ポリシーを変更しても、既にロックアウトされているユーザー アカウントのロックが解除されることはありません。

## <a name="default-fine-grained-password-policy-settings"></a>既定の細かい設定が可能なパスワード ポリシー設定

Azure AD DS マネージド ドメインでは、次のパスワード ポリシーが既定で構成され、すべてのユーザーに適用されます。

* **パスワードの最小文字数:** 7
* **パスワードの有効期間:** 90 日間
* **複雑さの要件を満たす必要があるパスワード**

既定では、次のアカウント ロックアウト ポリシーが構成されます。

* **アカウントのロックアウト期間:** 30
* **許可される失敗したログオン試行回数:** 5
* **失敗したログオン試行回数のカウントがリセットされるまでの時間:** 30 分

このような既定の設定の場合、2 分以内に、5 回の無効なパスワード使用が行われた場合、ユーザー アカウントは 30 分間ロック アウトされます。 アカウントは、自動的に、30 分後にロック解除されます。

既定の組み込みの細かい設定が可能なパスワード ポリシーは変更または削除できません。 代わりに、次のセクションで示すように、*AAD DC Administrators* グループのメンバーはカスタムの FGPP を作成し、既定の組み込みの FGPP をオーバーライドする (既定の組み込みの FGPP よりも優先される) ように構成することができます。

## <a name="create-a-custom-fine-grained-password-policy"></a>カスタムの細かい設定が可能なパスワード ポリシーを作成する

Azure でアプリケーションを構築するときに、カスタムの FGPP を構成することができます。 カスタムの FGPP を作成する必要がある例として、別のアカウント ロックアウト ポリシーを設定する場合や、マネージド ドメインの既定のパスワード有効期間設定を構成する場合があります。

カスタムの FGPP を作成し、Azure AD DS マネージド ドメイン内の特定のグループに適用することができます。 この構成によって、事実上、既定の FGPP がオーバーライドされます。 カスタムの細かい設定が可能なパスワード ポリシーを作成し、Azure AD DS マネージド ドメイン上で作成したカスタム OU に適用することもできます。

細かい設定が可能なパスワード ポリシーを作成するには、ドメインに参加している VM から Active Directory 管理ツールを使用します。 Active Directory 管理センターを使用すると、OU など、Azure AD DS マネージド ドメイン内のリソースを表示、編集、および作成することができます。

> [!NOTE]
> Azure AD DS マネージド ドメインで細かい設定が可能なパスワード ポリシーを作成するには、*AAD DC 管理者*グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. スタート画面で **[管理ツール]** を選択します。 [管理 VM を作成する][tutorial-create-management-vm]チュートリアルでインストールした使用可能な管理ツールの一覧が表示されます。
1. OU を作成および管理するには、管理ツールの一覧から **[Active Directory 管理センター]** を選択します。
1. 左側のウィンドウで、*contoso.com* などの Azure AD DS マネージド ドメインを選択します。
1. 右側の **[タスク]** パネルで、 **[新規] > [パスワードの設定]** を選択します。
1. **[Create Password Settings]\(パスワード設定の作成\)** ダイアログで、ポリシーの名前 (*MyCustomFGPP* など) を入力します。 優先順位 (*1* など) を適切に設定して、既定の FGPP (*200*) をオーバーライドします。

    必要に応じて他のパスワード ポリシー設定を編集します。たとえば、以前の *24* 個のパスワードとは異なるパスワードを作成することをユーザーに要求する **[パスワードの履歴を記録する]** などです。

    ![カスタムの細かい設定が可能なパスワード ポリシーを作成する](./media/how-to/custom-fgpp.png)

1. **[誤って削除されないように保護する]** をオフにします。 このオプションをオンにすると、FGPP を保存できません。
1. **[直接の適用先]** セクションで、 **[追加]** ボタンを選択します。 **[ユーザーまたはグループの選択]** ダイアログで、 **[場所]** ボタンをクリックします。

    ![パスワード ポリシーを適用するユーザーとグループを選択する](./media/how-to/fgpp-applies-to.png)

1. 細かい設定が可能なパスワード ポリシーはグループにのみ適用できます。 **[場所]** ダイアログで、ドメイン名 (*contoso.com* など) を展開し、**AADDC Users** などの OU を選択します。 適用するユーザーのグループを含むカスタム OU がある場合は、その OU を選択します。

    ![グループが属する OU を選択する](./media/how-to/fgpp-container.png)

1. ポリシーを適用するグループの名前を入力し、 **[名前の確認]** を選択して、グループが存在することを確認します。

    ![FGPP を適用するグループを検索して選択する](./media/how-to/fgpp-apply-group.png)

1. 選択したグループの名前が **[直接の適用先]** セクションに表示されたら、 **[OK]** を選択してカスタム パスワード ポリシーを保存します。

## <a name="next-steps"></a>次の手順

細かい設定が可能なパスワード ポリシーと Active Directory 管理センターの使用方法の詳細については、次の記事を参照してください。

* [細かい設定が可能なパスワード ポリシーの概要](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 管理センターを使用して細かい設定が可能なパスワード ポリシーを構成する](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
