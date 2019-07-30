---
title: リスクベースの MFA および SSPR と Azure Identity Protection
description: このチュートリアルでは、Multi-Factor Authentication とセルフサービスによるパスワードのリセットに対して Azure Identity Protection 統合を有効にして、危険な動作を減らします。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17cea353df0337b062b89cd440f79f7869450f8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113200"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>チュートリアル:リスク イベントを使用して多要素認証とパスワード変更をトリガーする

このチュートリアルでは、Azure Active Directory (Azure AD) Identity Protection の機能を有効にします。この Azure AD Premium P2 機能は、単なる監視およびレポート ツール以上のものです。 組織の ID を保護するために、危険な動作に自動的に対処するリスクベースのポリシーを構成することができます。 これらのポリシーによって、自動的にブロックしたり、パスワードの変更の要求や Multi-Factor Authentication の強制などの修復を開始したりすることができます。

既存の条件付きアクセス ポリシーに加えて、追加の保護レイヤーとして Azure AD Identity Protection ポリシーを使用することができます。 このようなポリシーを必要とするような危険な動作をユーザーがトリガーすることはないかもしれませんが、管理者として、ユーザーの保護に確信を持つことができます。

リスク イベントをトリガーする可能性があるのは、次のような操作です。

* 資格情報が漏洩したユーザー
* 匿名の IP アドレスからのサインイン
* 特殊な場所へのあり得ない移動
* 感染しているデバイスからのサインイン
* 不審なアクティビティのある IP アドレスからのサインイン
* 未知の場所からのサインイン

Azure AD Identity Protection の詳細については、[Azure AD Identity Protection の概要](../active-directory-identityprotection.md)に関する記事を参照してください。

> [!div class="checklist"]
> * Azure MFA への登録を有効にする
> * リスクベースのパスワードの変更を有効にする
> * リスクベースの Multi-Factor Authentication を有効にする

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版 Azure AD Premium P2 ライセンスが割り当てられた、動作している Azure AD テナントへのアクセス。
* Azure AD テナントで全体管理者特権を持つアカウント。
* 前のセルフサービスによるパスワードのリセット (SSPR) と Multi-Factor Authentication (MFA) のチュートリアルを完了していること。

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>SSPR と MFA に対してリスクベースのポリシーを有効にする

リスクベースのポリシーの有効化は、簡単なプロセスです。 次の手順では、サンプルの構成について説明します。

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>ユーザーが Multi-Factor Authentication に登録できるようにする

Azure AD Identity Protection には、ユーザーを Multi-Factor Authentication に登録させ、現在の登録状況を簡単に識別できるようにする、既定のポリシーが含まれています。 このポリシーを有効にしても、ユーザーが Multi-Factor Authentication の実行を要求されるようにはなりませんが、事前登録を求められます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** をクリックし、 **[Azure AD Identity Protection]** に移動します。
1. **[MFA 登録]** をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
   1. このポリシーを設定すると、すべてのユーザーが方法を登録し、それを Multi-Factor Authentication で使用するための準備を行う必要があります。
1. **[Save]** をクリックします。

   ![サインイン時に MFA への登録をユーザーに求める](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>リスクベースのパスワードの変更を有効にする

Microsoft では、研究者、法執行機関、Microsoft のさまざまなセキュリティ チーム、その他の信頼できる情報源と協力して、ユーザー名とパスワードのペアを調査しています。 それらのペアのいずれかが環境内のアカウントに一致すると、次のポリシーを使用して、リスクベースのパスワード変更がトリガーされます。

1. [ユーザーのリスク ポリシー] をクリックします。
1. **[条件]** の下の **[ユーザーのリスク]** を選択し、 **[中以上]** を選択します。
1. [選択]、[完了] の順にクリックします。
1. **[アクセス]** の下の **[アクセスを許可]** を選択し、 **[パスワードの変更を必須とする]** を選択します。
1. [選択] をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
1. **[保存]**

### <a name="enable-risk-based-multi-factor-authentication"></a>リスクベースの Multi-Factor Authentication を有効にする

ほとんどのユーザーは、追跡できる正常な動作をしています。この規範から外れた場合は、そのユーザーにサインインを許可すると危険であることがあります。 そのユーザーをブロックしたり、Multi-Factor Authentication を実行してユーザーが本人であることを証明するように求めたりすることが必要な場合もあります。 危険なサインインが検出されたときに MFA を必要とするポリシーを有効にするには、次のポリシーを有効にします。

1. [サインインのリスク ポリシー] をクリックします
1. **[条件]** の下の **[ユーザーのリスク]** を選択し、 **[中以上]** を選択します。
1. [選択]、[完了] の順にクリックします。
1. **[アクセス]** の下の **[アクセスを許可]** を選択し、 **[多要素認証を要求する]** を選択します。
1. [選択] をクリックします。
1. [ポリシーの適用] を **[オン]** に設定します。
1. **[保存]**

## <a name="clean-up-resources"></a>リソースのクリーンアップ

テストを完了し、リスクベースのポリシーを有効にする必要がなくなった場合は、無効にする各ポリシーに戻り、 **[ポリシーの適用]** を **[オフ]** に設定します。
