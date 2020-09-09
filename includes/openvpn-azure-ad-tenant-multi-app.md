---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2eb2dbc43c59f4f6301c7f5073a73462639d35b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797121"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.Azure AD テナントを作成する

[新しいテナントの作成](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って、Azure AD テナントを作成します。

* 組織名
* 初期ドメイン名

  例:

   ![新しい Azure AD テナント](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2.テナント ユーザーを作成する

この手順では、2 つの Azure AD テナント ユーザーを作成します。全体管理者アカウントを 1 つと、マスター ユーザー アカウントを 1 つです。 マスター ユーザー アカウントは、マスター埋め込みアカウント (サービス アカウント) として使用されます。 Azure AD テナント ユーザー アカウントを作成する際には、作成するユーザーの種類に応じてディレクトリ ロールを調整します。 [この記事](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)の手順を使用して、Azure AD テナントに対して少なくとも 2 人のユーザーを作成します。 作成するアカウントの種類 (下記) に応じて、**ディレクトリ ロール**を変更してください。

* 全体管理者
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3.VPN クライアントを登録する

Azure AD テナントで VPN クライアントを登録します。

1. 認証に使用するディレクトリのディレクトリ ID を特定します。 これは、[Active Directory] ページの [プロパティ] セクションに表示されます。

    ![ディレクトリ ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. ディレクトリ ID をコピーします。

3. **全体管理者**ロールを割り当てられたユーザーとして、Azure portal にサインインします。

4. 次に、管理者の同意を与えます。 デプロイの場所に関連する URL をコピーし、ブラウザーのアドレス バーに貼り付けます。

    パブリック

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. メッセージが表示されたら、**全体管理者**アカウントを選択します。

    ![ディレクトリ ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. メッセージが表示されたら、 **[同意する]** を選択します。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されます。

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4.その他のアプリケーションを登録する

この手順では、さまざまなユーザーやグループ用にその他のアプリケーションを登録します。

1. Azure Active Directory で **[アプリの登録]** 、 **[+ 新しい登録]** の順にクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **[アプリケーションの登録]** ページで、 **[名前]** を入力します。 目的の **[サポートされているアカウントの種類]** を選択し、 **[登録]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 新しいアプリが登録されたら、アプリ ブレードにある **[API の公開]** をクリックします。

4. **[+ スコープの追加]** をクリックします。

5. 既定の **[アプリケーション ID URI]** のままにします。 **保存して続行** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 必須フィールドに入力し、**[状態]** を **[有効]** にします。 **[スコープの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. **[API の公開]** をクリックし、 **[+ クライアント アプリケーションの追加]** をクリックします。  **[クライアント ID]** には、クラウドに基づいて次の値を入力します。

    - Azure **Public** の場合、「**41b23e61-6c1e-4545-b367-cd054e0ed4b4**」と入力します
    - Azure **Government** の場合、「**51bb15d4-3a4f-4ebf-9dca-40096fe32426**」と入力します
    - Azure **Germany** の場合、「**538ee9e6-310a-468d-afef-ea97365856a9**」と入力します
    - Azure **China 21Vianet** の場合、「**49f817b6-84ae-4cc0-928c-73f27289b3aa**」と入力します

8. **[アプリケーションの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. **[概要]** ページから **[アプリケーション (クライアント) ID]** をコピーします。 この情報は、VPN ゲートウェイを構成するために必要です。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. この「[その他のアプリケーションを追加する](#register-apps)」セクションの手順を繰り返して、セキュリティ要件に必要な数だけアプリケーションを作成します。 各アプリケーションが VPN ゲートウェイに関連付けられ、さまざまなユーザーのグループを指定できます。 1 つのゲートウェイに関連付けられるアプリケーションは 1 つだけです。

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5.アプリケーションへのユーザーの割り当て

ユーザーをアプリケーションに割り当てます。

1. **Azure AD -> [エンタープライズ アプリケーション]** で、新しく登録したアプリケーションを選択し、 **[プロパティ]** をクリックします。 **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定します。 **[保存]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. アプリ ページで **[ユーザーとグループ]** をクリックし、 **[+ ユーザーの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. **[割り当ての追加]** で **[ユーザーとグループ]** をクリックします。 この VPN アプリケーションにアクセスできるようにするユーザーを選択します。 **[選択]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
