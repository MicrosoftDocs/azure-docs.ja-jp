---
title: Azure Active Directory でのリスクベースのユーザー サインイン保護
description: このチュートリアルでは、ユーザーのアカウントで危険なサインイン動作が検出されたときに Azure Identity Protection を有効にしてユーザーを保護する方法について学習します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741340"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>チュートリアル:ユーザー サインインのリスク検出を使用して、Azure AD Multi-Factor Authentication またはパスワードの変更をトリガーする

ユーザーを保護するために、危険な動作に自動的に対処するリスクベースのポリシーを Azure Active Directory (Azure AD) で構成することができます。 Azure AD Identity Protection ポリシーを使用すると、サインイン試行を自動的にブロックしたり、パスワードの変更の要求や Azure AD Multi-Factor Authentication の要求といった追加のアクションを要求したりできます。 これらのポリシーは、既存の Azure AD 条件付きアクセス ポリシーと連携して、組織の追加の保護レイヤーとして機能します。 ユーザーがこれらのポリシーのいずれかで危険な動作をトリガーすることは決してないと思われますが、セキュリティを侵害しようとする試みがあった場合、組織は保護されます。

> [!IMPORTANT]
> このチュートリアルでは、リスクベースの Azure AD Multi-Factor Authentication を有効にする方法を管理者に示します。
>
> IT チームが Azure AD Multi-Factor Authentication を使用する機能を有効にしていない場合、またはサインイン時に問題が発生した場合は、ヘルプデスクに連絡して追加のサポートを依頼してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD Identity Protection で使用可能なポリシーについて理解する
> * Azure AD Multi-Factor Authentication の登録を有効にする
> * リスクベースのパスワードの変更を有効にする
> * リスクベースの Multi-Factor Authentication を有効にする
> * ユーザー サインイン試行に対するリスクベースのポリシーをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 少なくとも Azure AD Premium P2 または試用版ライセンスが有効になっている、動作している Azure AD テナント。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* "*グローバル管理者*" 特権を持つアカウント。
* セルフサービス パスワード リセット用に構成された Azure AD と Azure AD Multi-Factor Authentication
    * 必要に応じて、[Azure AD SSPR を有効にするチュートリアルを完了してください](tutorial-enable-sspr.md)。
    * 必要に応じて、[Azure AD Multi-Factor Authentication を有効にするチュートリアルを完了してください](tutorial-enable-azure-mfa.md)。

## <a name="overview-of-azure-ad-identity-protection"></a>Azure AD Identity Protection の概要

Microsoft では、ユーザーのサインイン試行の一環として、毎日、何兆もの匿名化されたシグナルを収集して分析しています。 これらのシグナルは、適切なユーザー サインイン動作のパターンを構築し、潜在的に危険なサインイン試行を識別するのに役立ちます。 Azure AD Identity Protection では、ユーザーのサインイン試行を確認し、不審な動作がある場合に追加のアクションを実行できます。

次のアクションのいくつかは、Azure AD Identity Protection リスク検出をトリガーする可能性があります。

* 資格情報が漏洩したユーザー。
* 匿名の IP アドレスからのサインイン。
* 特殊な場所へのあり得ない移動。
* 感染しているデバイスからのサインイン。
* 不審なアクティビティのある IP アドレスからのサインイン。
* 未知の場所からのサインイン。

Azure AD Identity Protection には、ユーザーを保護し、不審なアクティビティに対処するために、次の 3 つのポリシーが用意されています。 ポリシーの適用をオンまたはオフにすることも、ポリシーを適用するユーザーまたはグループを選択することもできます。また、サインイン時にアクセスをブロックするか、追加のアクションを要求するかを決定することもできます。

* ユーザー リスクのポリシー
    * 資格情報が侵害された可能性のあるユーザー アカウントを識別して対処します。 ユーザーに新しいパスワードの作成を促すことができます。
* サインイン リスク ポリシー
    * 不審なサインイン試行を識別して対処します。 Azure AD Multi-Factor Authentication を使用して追加の検証フォームを提供するようユーザーに求めることができます。
* MFA 登録ポリシー
    * ユーザーが Azure AD Multi-Factor Authentication に登録されていることを確認します。 サインイン リスク ポリシーによって MFA を要求する場合は、ユーザーが Azure AD Multi-Factor Authentication に既に登録されている必要があります。

ポリシー ユーザーまたはサインイン リスク ポリシーを有効にする場合、リスク レベルのしきい値 ( *[低以上]* 、 *[中以上]* 、または *[高]* ) を選択することもできます。 この柔軟性により、不審なサインイン イベントの制御をどの程度積極的に行うかを決定できます。

Azure AD Identity Protection の詳細については、「[Azure Active Directory Identity Protection とは](../identity-protection/overview-identity-protection.md)」を参照してください

## <a name="enable-mfa-registration-policy"></a>MFA 登録ポリシーを有効にする

Azure AD Identity Protection には、ユーザーを Azure AD Multi-Factor Authentication に登録するのに役立つ既定のポリシーが用意されています。 追加のポリシーを使用してサインイン イベントを保護する場合は、ユーザーが MFA に既に登録されている必要があります。 このポリシーを有効にすると、ユーザーはサインイン イベントごとに MFA を実行する必要がなくなります。 このポリシーは、ユーザーの登録状態を確認するのみで、必要に応じて事前登録することを求めます。

追加の Azure AD Identity Protection ポリシーに対して有効にするユーザーについては、MFA 登録ポリシーを有効にすることをお勧めします。 このポリシーを有効にするには、次の手順を実行します。

1. グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択します。 **[セキュリティ]** を選択し、 *[保護]* メニュー見出しの下の **[Identity Protection]** を選択します。
1. 左側のメニューから **[MFA 登録ポリシー]** を選択します。
1. 既定では、ポリシーは *[すべてのユーザー]* に適用されます。 必要に応じて、 **[割り当て]** を選択し、ポリシーを適用するユーザーまたはグループを選択します。
1. *[制御]* で、 **[アクセス]** を選択します。 *[Azure AD MFA への登録を必須とする]* のオプションがオンになっていることを確認し、 **[選択]** を選択します。
1. **[ポリシーの適用]** を *[オン]* にして、 **[保存]** を選択します。

    ![Azure portal で MFA への登録をユーザーに要求する方法のスクリーンショット](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>ユーザー リスク ポリシーを有効にしてパスワード変更を求める

Microsoft では、研究者、法執行機関、Microsoft のさまざまなセキュリティ チーム、その他の信頼できる情報源と協力して、ユーザー名とパスワードのペアを調査しています。 それらのペアのいずれかが環境内のアカウントに一致すると、リスクベースのパスワード変更を要求できます。 このポリシーとアクションでは、以前に流出した資格情報を無効にするために、ユーザーは、サインインする前にパスワードを更新する必要があります。

このポリシーを有効にするには、次の手順を実行します。

1. 左側のメニューから **[ユーザー リスク ポリシー]** を選択します。
1. 既定では、ポリシーは *[すべてのユーザー]* に適用されます。 必要に応じて、 **[割り当て]** を選択し、ポリシーを適用するユーザーまたはグループを選択します。
1. *[条件]* で、 **[条件の選択] > [リスク レベルの選択]** を選択し、 *[中以上]* を選択します。
1. **[選択]** 、 **[完了]** の順に選択します。
1. *[アクセス]* で、 **[アクセス]** を選択します。 **[アクセスを許可]** と *[パスワードの変更を必須とする]* のオプションがオンになっていることを確認し、 **[選択]** を選択します。
1. **[ポリシーの適用]** を *[オン]* にして、 **[保存]** を選択します。

    ![Azure portal でユーザー リスク ポリシーを有効にする方法のスクリーンショット](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>サインイン リスク ポリシーを有効にして MFA を求める

ほとんどのユーザーは、追跡可能な通常の動作を行うものです。 ユーザーがその通常動作から外れた場合、そのユーザーに正常なサインインを許可すると、危険である可能性があります。 代わりに、そのユーザーをブロックするか、多要素認証を実行するよう要求することができます。 ユーザーが MFA チャレンジを正常に完了した場合は、それを有効なサインイン試行と見なして、アプリケーションまたはサービスへのアクセスを許可することができます。

このポリシーを有効にするには、次の手順を実行します。

1. 左側のメニューから **[サインイン リスク ポリシー]** を選択します。
1. 既定では、ポリシーは *[すべてのユーザー]* に適用されます。 必要に応じて、 **[割り当て]** を選択し、ポリシーを適用するユーザーまたはグループを選択します。
1. *[条件]* で、 **[条件の選択] > [リスク レベルの選択]** を選択し、 *[中以上]* を選択します。
1. **[選択]** 、 **[完了]** の順に選択します。
1. *[アクセス]* で、 **[コントロールを選択してください]** を選択します。 **[アクセスを許可する]** および *[多要素認証が必要]* のオプションがオンになっていることを確認し、 **[選択]** を選択します。
1. **[ポリシーの適用]** を *[オン]* にして、 **[保存]** を選択します。

    ![Azure portal でサインイン リスク ポリシーを有効にする方法のスクリーンショット](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>危険なサインイン イベントをテストする

ほとんどのユーザー サインイン イベントからは、前の手順で構成したリスクベースのポリシーがトリガーされることはありません。 MFA の追加やパスワードのリセットを求めるプロンプトがユーザーに表示されることは決してないでしょう。 ユーザーの資格情報が安全に保たれ、その動作が一貫している場合、そのユーザーのサインイン イベントは成功します。

前の手順で作成した Azure AD Identity Protection ポリシーをテストするには、危険な動作または潜在的な攻撃をシミュレートする手段が必要です。 これらのテストを実行する手順は、検証する Azure AD Identity Protection ポリシーによって異なります。 シナリオと手順の詳細については、[Azure AD Identity Protection でのリスク検出のシミュレーション](../identity-protection/howto-identity-protection-simulate-risk.md)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

テストを完了し、リスクベースのポリシーを有効にする必要がなくなった場合は、無効にする各ポリシーに戻り、 **[ポリシーの適用]** を *[オフ]* に設定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure AD Identity Protection のリスクベースのユーザー ポリシーを有効にしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure AD Identity Protection で使用可能なポリシーについて理解する
> * Azure AD Multi-Factor Authentication の登録を有効にする
> * リスクベースのパスワードの変更を有効にする
> * リスクベースの Multi-Factor Authentication を有効にする
> * ユーザー サインイン試行に対するリスクベースのポリシーをテストする

> [!div class="nextstepaction"]
> [Azure AD Identity Protection の詳細情報](../identity-protection/overview-identity-protection.md)
