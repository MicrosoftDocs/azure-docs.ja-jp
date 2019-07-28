---
title: Azure Multi-Factor Authentication パイロットの有効化
description: このチュートリアルでは、パイロット グループのユーザーに対して Azure AD Multi-Factor Authentication を有効にします
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: microsoftguyjflo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113240"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>チュートリアル:Azure Multi-Factor Authentication のパイロット ロール アウトを完了する

このチュートリアルでは、Azure portal にログインするときに Azure Multi-Factor Authentication (Azure MFA) を有効にする条件付きアクセス ポリシーの構成について説明します。 このポリシーは、パイロット ユーザーの特定のグループにデプロイされ、テストされます。 条件付きアクセスを使用する Azure MFA のデプロイは、組織および管理者にとって、従来の強制方法よりも大幅に高い柔軟性を持つ方法です。

> [!div class="checklist"]
> * Azure Multi-Factor Authentication の有効化
> * Azure Multi-Factor Authentication のテスト

## <a name="prerequisites"></a>前提条件

* 少なくとも試用版ライセンスが有効になっている、動作している Azure AD テナント。
* グローバル管理者特権を持つアカウント。
* 管理者がテスト目的で把握しているパスワードを持つ、管理者以外のテスト ユーザー。ユーザーを作成する必要がある場合は、[Azure Active Directory に新しいユーザーを追加する](../add-users-azure-active-directory.md)」を参照してください。
* 管理者以外のユーザーが所属している、テストするパイロット グループ。グループを作成する必要がある場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory-groups-create-azure-portal.md)」を参照してください。

## <a name="enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の有効化

1. 全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** 、 **[条件付きアクセス]** の順に移動します
1. **[新しいポリシー]** を選択します
1. ポリシーに「**MFA Pilot**」という名前を付けます
1. **[ユーザーとグループ]** の下で、 **[ユーザーとグループの選択]** を選択します
    * この記事の前提条件セクションで作成したパイロット グループを選択します
    * **[完了]** をクリックします
1. **[クラウド アプリ]** の下の **[アプリを選択]** を選択します
    * Azure portal のクラウド アプリは、**Microsoft Azure の管理**です
    * **[選択]** をクリックします。
    * **[完了]** をクリックします
1. **[条件]** セクションはスキップします
1. **[許可]** の下で、 **[アクセス権の付与]** が選択されていることを確認します
    * **[多要素認証を要求する]** ボックスをオンにします
    * **[選択]** をクリックします。
1. **[セッション]** セクションはスキップします
1. **[ポリシーを有効にする]** を **[オン]** にします
1. **[作成]**

## <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication をテストする

条件付きアクセス ポリシーが機能することを証明するには、MFA を必要としないリソースへのログインをテストし、次に MFA を必要とする Azure portal へのログインをテストします。

1. InPrivate または incognito モードで新しいブラウザー ウィンドウを開き、[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com) に移動します。
   * この記事の前提条件のセクションで作成したテスト ユーザーでログインし、MFA を完了するように求められないことに注意してください。
   * ブラウザー ウィンドウを閉じます。
2. InPrivate または incognito モードで新しいブラウザー ウィンドウを開き、[https://portal.azure.com](https://portal.azure.com) に移動します。
   * この記事の前提条件のセクションで作成したテスト ユーザーでログインし、今度は Azure Multi-Factor Authentication に登録してこれを使用するように要求されることに注意してください。
   * ブラウザー ウィンドウを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで構成した機能をもう使用しないと判断した場合は、次の変更を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** 、 **[条件付きアクセス]** の順に移動します
1. 作成した条件付きアクセス ポリシーを選択します。
1. **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Multi-Factor Authentication を有効にしました。 次のチュートリアルに進み、Azure Identity Protection をセルフサービスによるパスワードのリセットと Multi-Factor Authentication のエクスペリエンスに統合する方法を参照してください。

> [!div class="nextstepaction"]
> [サインイン時にリスクを評価する](tutorial-risk-based-sspr-mfa.md)
