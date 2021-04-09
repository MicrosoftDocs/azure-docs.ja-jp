---
title: クイック スタート:Azure Active Directory を ID プロバイダーとして使用するアプリにユーザーを割り当てる
description: このクイックスタートでは、ID プロバイダーとして Azure AD を使用するように設定したアプリをユーザーが使用できるようにする手順を説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258373"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>クイック スタート:Azure AD を ID プロバイダーとして使用しているアプリにユーザーを割り当てる

前のクイックスタートでは、アプリのプロパティを構成しました。 プロパティを設定するときに、割り当てられるユーザーと割り当てられないユーザーの両方に対するエクスペリエンスを構成しました。 このクイックスタートでは、ユーザーをアプリに割り当てる手順について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリにユーザーを割り当てるには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。

## <a name="assign-users-to-an-app"></a>アプリにユーザーを割り当てる
1. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 次に、構成するアプリケーションを探して選択します。
2. 左側のナビゲーション メニューで **[ユーザーとグループ]** を選択します。
   > [!NOTE]
   > 一部の Microsoft 365 アプリでは、PowerShell を使用する必要があります。 
3. **[ユーザーの追加]** ボタンを選択します。
4. **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。
5. アプリケーションに割り当てるユーザーまたはグループを選択します。 検索ボックスで、ユーザーまたはグループの名前の入力を開始することもできます。 複数のユーザーとグループを選択でき、選択した項目が **[選択されたアイテム]** に表示されます。
    > [!IMPORTANT]
    > グループをアプリケーションに割り当てると、そのグループ内のユーザーのみがアクセスできるようになります。 割り当ては、入れ子になったグループにはカスケードされません。

    > [!NOTE]
    > グループベースの割り当てには、Azure Active Directory Premium P1 または P2 エディションが必要です。 グループ ベースの割り当てがサポートされるのはセキュリティ グループのみです。 入れ子になったグループ メンバーシップと Microsoft 365 グループは、現在サポートされていません。 この記事で説明されている機能に必要なライセンスの詳細については、[Azure Active Directory の価格のページ](https://azure.microsoft.com/pricing/details/active-directory)を参照してください。 
6. 完了したら、 **[選択]** を選択します。
   ![アプリケーションにユーザーまたはグループを割り当てる](./media/assign-user-or-group-access-portal/assign-users.png)
7. **[ユーザーとグループ]** ウィンドウで、リストから 1 つまたは複数のユーザーまたはグループを選択し、ウィンドウの下部にある **[選択]** ボタンを選びます。
8. アプリケーションでサポートされている場合は、ユーザーまたはグループにロールを割り当てることができます。 **[割り当ての追加]** ウィンドウで、 **[ロールの選択]** を選択します。 次に、 **[ロールの選択]** ウィンドウで、選択したユーザーまたはグループに適用するロールを選択して、ウィンドウの下部にある **[OK]** を選びます。 
    > [!NOTE]
    > アプリケーションでロールの選択がサポートされていない場合は、既定のアクセス ロールが割り当てられます。 この場合、アプリケーションでユーザーのアクセス レベルを管理します。
9. **[割り当ての追加]** ウィンドウで、ウィンドウの下部にある **[割り当て]** ボタンを選択します。

同じ手順を使用して、ユーザーまたはグループの割り当てを解除できます。 割り当てを解除するユーザーまたはグループを選択してから、 **[削除]** を選択します。 一部の Microsoft 365 アプリと Office 365 アプリでは、PowerShell を使用する必要があります。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタートを完了したら、アプリの削除を検討してください。 そうすることで、テスト テナントをクリーンな状態に保つことができます。 アプリの削除については、このシリーズの最後のクイックスタートである[アプリの削除](delete-application-portal.md)に関する記事で説明されています。

## <a name="next-steps"></a>次のステップ

次の記事に進み、アプリのシングル サインオンをセットアップする方法を学習してください。
> [!div class="nextstepaction"]
> [SAML ベースのシングル サインオンの設定](add-application-portal-setup-sso.md)

OR

> [!div class="nextstepaction"]
> [OIDC ベースのシングル サインオンの設定](add-application-portal-setup-oidc-sso.md)
