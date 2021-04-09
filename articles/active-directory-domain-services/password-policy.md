---
title: Azure AD Domain Services でのパスワード ポリシーの作成と使用 | Microsoft Docs
description: Azure AD DS マネージド ドメインで、細かい設定が可能なパスワード ポリシーを使用してアカウント パスワードをセキュリティで保護し、制御する方法とその理由について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619234"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Azure Active Directory Domain Services マネージド ドメインのパスワードとアカウントのロックアウト ポリシー

Azure Active Directory Domain Services (Azure AD DS) でユーザーのセキュリティを管理するには、アカウント ロックアウトの設定や、パスワードの最小文字数、パスワードの複雑さなどを制御する細かい設定が可能なパスワード ポリシーを定義します。 既定の細かいパスワード ポリシーは、Azure AD DS マネージド ドメイン内のすべてのユーザーに対して作成され、適用されます。 きめ細かく制御し、特定のビジネスまたはコンプライアンスのニーズに対応するために、追加のポリシーを作成し、特定のユーザー グループに適用することができます。

この記事では、Active Directory 管理センターを使用して Azure AD DS に細かい設定が可能なパスワード ポリシーを作成し、構成する方法について説明します。

> [!NOTE]
> パスワード ポリシーは、Resource Manager デプロイ モデルを使用して作成されたマネージド ドメインに対してのみ使用できます。 クラシックを使用して作成された古いマネージド ドメインの場合は、[クラシック仮想ネットワーク モデルから Resource Manager に移行][migrate-from-classic]します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
  * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
  * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
  * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
  * マネージド ドメインは、Resource Manager デプロイ モデルを使用して作成されている必要があります。 必要であれば、[クラシック仮想ネットワーク モデルから Resource Manager に移行します][migrate-from-classic]。
* マネージド ドメインに参加している Windows Server 管理 VM。
  * 必要に応じて、[管理 VM を作成する][tutorial-create-management-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="default-password-policy-settings"></a>規定のパスワード ポリシー設定

細かい設定が可能なパスワード ポリシー (FGPP) を使用すると、パスワードおよびアカウント ロックアウトのポリシーに対して、ドメイン内の異なるユーザーに特定の制限を適用できます。 たとえば、特権アカウントをセキュリティで保護するには、通常の権限のないアカウントよりも厳しいアカウント ロックアウト設定を適用します。 マネージド ドメイン内に複数の FGPP を作成し、適用するユーザーの優先順位を指定できます。

パスワード ポリシーと Active Directory 管理センターの使用方法の詳細については、次の記事を参照してください。

* [細かい設定が可能なパスワード ポリシーの概要](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 管理センターを使用して細かい設定が可能なパスワード ポリシーを構成する](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

ポリシーは、マネージド ドメイン内のグループの関連付けを通じて配布されます。すべての変更が、次回のユーザーのサインイン時に適用されます。 ポリシーを変更しても、既にロックアウトされているユーザー アカウントのロックが解除されることはありません。

パスワード ポリシーの動作は、適用されるユーザー アカウントの作成方法によって若干異なります。 Azure AD DS でユーザー アカウントを作成するには、次の 2 つの方法があります。

* ユーザー アカウントは Azure AD から同期できます。 これには、Azure で直接作成されたクラウド専用のユーザー アカウントと、Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたハイブリッド ユーザー アカウントが含まれます。
    * ほぼすべての Azure AD DS のユーザー アカウントは、Azure AD からの同期プロセスを通じて作成されています。
* ユーザー アカウントはマネージド ドメインに手動で作成することができ、Azure AD には存在しません。

すべてのユーザー アカウントは、その作成方法に関わらず、Azure AD DS の既定のパスワード ポリシーによって次のアカウント ロックアウト ポリシーが適用されています。

* **アカウントのロックアウト期間:** 30
* **許可される失敗したログオン試行回数:** 5
* **失敗したログオン試行回数のカウントがリセットされるまでの時間:** 30 分
* **パスワードの有効期間:** 90 日間

このような既定の設定の場合、2 分以内に、5 回の無効なパスワード使用が行われた場合、ユーザー アカウントは 30 分間ロック アウトされます。 アカウントは、自動的に、30 分後にロック解除されます。

アカウント ロックアウトは、マネージド ドメイン内でのみ行われます。 ユーザー アカウントは、マネージド ドメインへのサインインが失敗した場合にのみ Azure AD DS のみでロックアウトされます。 Azure AD またはオンプレミスから同期されたユーザー アカウントは Azure AD DS でのみロックアウトされ、ソース ディレクトリではロックアウトされません。

90 日以上のパスワードの有効期間が指定されている Azure AD パスワードポリシーがある場合、パスワードの有効期間は Azure AD DS の既定のポリシーに適用されます。 Azure AD DS で異なるパスワードの有効期間を定義するには、カスタム パスワード ポリシーを構成します。 Azure AD またはオンプレミスの AD DS 環境で構成されているパスワードの有効期間が Azure AD DS パスワード ポリシーより短い場合は注意してください。 このシナリオでは、Azure AD またはオンプレミスの AD DS 環境でユーザー パスワードを変更するように求められる前に、パスワードの有効期限が Azure AD DS で切れる可能性があります。

マネージド ドメインで手動で作成されたユーザー アカウントの場合、次のパスワード設定も既定のポリシーから追加で適用されます。 ユーザーは Azure AD DS で直接パスワードを更新することはできないため、これらの設定は Azure AD から同期したユーザー アカウントには適用されません。

* **パスワードの最小文字数:** 7
* **複雑さの要件を満たす必要があるパスワード**

既定のパスワード ポリシーでは、アカウントのロックアウトやパスワードの設定を変更することはできません。 代わりに、次のセクションで示すように、*AAD DC Administrators* グループのメンバーはカスタムのパスワード ポリシーを作成し、既定の組み込みのポリシーをオーバーライドする (既定の組み込みのポリシーよりも優先される) ように構成することができます。

## <a name="create-a-custom-password-policy"></a>カスタムのパスワード ポリシーを作成する

Azure でアプリケーションを構築して実行するときに、カスタムのパスワード ポリシーを構成することができます。 たとえば、異なるアカウント ロックアウト ポリシー設定を設定するポリシーを作成できます。

カスタムのパスワード ポリシーは、マネージド ドメイン内のグループに適用されます。 この構成によって、事実上、既定のポリシーがオーバーライドされます。

カスタムのパスワード ポリシーを作成するには、ドメインに参加している VM から Active Directory 管理ツールを使用します。 Active Directory 管理センターを使用すると、マネージド ドメイン (OU を含む) 内のリソースを表示、編集、および作成できます。

> [!NOTE]
> マネージド ドメインでカスタムのパスワード ポリシーを作成するには、*AAD DC 管理者* グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. スタート画面で **[管理ツール]** を選択します。 [管理 VM を作成する][tutorial-create-management-vm]ためのチュートリアルでインストールされた使用可能な管理ツールの一覧が表示されます。
1. OU を作成して管理するには、管理ツールの一覧から **[Active Directory 管理センター]** を選択します。
1. 左側のペインで、マネージド ドメイン (例: *aaddscontoso.com*) を選択します。
1. **[System]/(システム/)** コンテナーを開き、 **[Password Settings Container]/(パスワード設定コンテナ―/)** を開きます。

    マネージド ドメインの組み込みのパスワード ポリシーが表示されます。 この組み込みのポリシーは変更できません。 代わりに、既定のポリシーを上書きするカスタムのパスワード ポリシーを作成します。

    ![Active Directory 管理センターからパスワード ポリシーを作成する](./media/password-policy/create-password-policy-adac.png)

1. 右側の **[タスク]** パネルで、 **[新規] > [パスワードの設定]** を選択します。
1. **[Create Password Settings]\(パスワード設定の作成\)** ダイアログで、ポリシーの名前 (*MyCustomFGPP* など) を入力します。
1. 複数のパスワード ポリシーが存在する場合は、優先順位が最も高いポリシーがユーザーに適用されます。 数値が小さいほど、優先度は高くなります。 既定のパスワード ポリシーの優先度は *200* です。

    カスタム パスワード ポリシーの優先順位を *1* などに設定して、既定値を上書きします。

1. 必要に応じて、他のパスワード ポリシー設定を編集します。 次の重要な点に注意してください。

    * パスワードの複雑さ、年齢、有効期限などの設定は、マネージド ドメインで手動で作成したユーザーに対してのみ適用されます。
    * アカウント ロックアウトの設定はすべてのユーザーに適用されますが、マネージド ドメイン内でのみ有効になり、Azure AD 自体では有効になりません。

    ![カスタムの細かい設定が可能なパスワード ポリシーを作成する](./media/password-policy/custom-fgpp.png)

1. **[誤って削除されないように保護する]** をオフにします。 このオプションをオンにすると、FGPP を保存できません。
1. **[直接の適用先]** セクションで、 **[追加]** ボタンを選択します。 **[ユーザーまたはグループの選択]** ダイアログで、 **[場所]** ボタンを選択します。

    ![パスワード ポリシーを適用するユーザーとグループを選択する](./media/password-policy/fgpp-applies-to.png)

1. パスワード ポリシーはグループにのみ適用できます。 **[場所]** ダイアログで、ドメイン名 (*aaddscontoso.com* など) を展開し、**AADDC Users** などの OU を選択します。 適用するユーザーのグループを含むカスタム OU がある場合は、その OU を選択します。

    ![グループが属する OU を選択する](./media/password-policy/fgpp-container.png)

1. ポリシーを適用するグループの名前を入力し、 **[名前の確認]** を選択して、グループが存在することを確認します。

    ![FGPP を適用するグループを検索して選択する](./media/password-policy/fgpp-apply-group.png)

1. 選択したグループの名前が **[直接の適用先]** セクションに表示されたら、 **[OK]** を選択してカスタム パスワード ポリシーを保存します。

## <a name="next-steps"></a>次のステップ

パスワード ポリシーと Active Directory 管理センターの使用方法の詳細については、次の記事を参照してください。

* [細かい設定が可能なパスワード ポリシーの概要](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 管理センターを使用して細かい設定が可能なパスワード ポリシーを構成する](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
