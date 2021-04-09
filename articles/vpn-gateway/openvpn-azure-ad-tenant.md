---
title: 'VPN Gateway: P2S VPN 接続用の Azure AD テナント:Azure AD 認証'
description: P2S Open VPN 認証用の Azure AD テナントを設定する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390166"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 この記事では、P2S Open VPN 認証用の Azure AD テナントを設定する方法について説明します。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1.Azure AD テナントを確認する

Azure AD テナントがあることを確認します。 Azure AD テナントがない場合、[新しいテナントの作成](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って作成できます。

* 組織名
* 初期ドメイン名

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="新しい Azure AD テナント" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.Azure AD テナント ユーザーを作成する

Azure AD テナントには、全体管理者アカウントとマスター ユーザー アカウントが必要です。 マスター ユーザー アカウントは、マスター埋め込みアカウント (サービス アカウント) として使用されます。 Azure AD テナント ユーザー アカウントを作成する際には、作成するユーザーの種類に応じてディレクトリ ロールを調整します。

[Azure Active Directory でのユーザーの追加と削除](../active-directory/fundamentals/add-users-azure-active-directory.md)に関する記事に記載されている手順を使用して、Azure AD テナントに対して 2 人以上のユーザーを作成します。 作成するアカウントの種類 (下記) に応じて、**ディレクトリ ロール** を変更してください。

* 全体管理者
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3.VPN ゲートウェイでの Azure AD 認証を有効にする

1. 認証に使用するディレクトリのディレクトリ ID を特定します。 これは、[Active Directory] ページの [プロパティ] セクションに表示されています。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="[ディレクトリのプロパティ] を示すスクリーンショット" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. ディレクトリ ID をコピーします。

1. **全体管理者** ロールを割り当てられたユーザーとして、Azure portal にサインインします。

1. 次に、管理者の同意を与えます。 デプロイの場所に関連する URL をコピーし、ブラウザーのアドレス バーに貼り付けます。

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
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Azure AD テナントにネイティブではないグローバル管理者アカウントを使用して同意を与える場合は、"common" を URL の Azure AD ディレクトリ ID に置き換えてください。 また、他の特定の場合でも、"common" をディレクトリ ID に置き換える必要があることがあります。
   >

1. メッセージが表示されたら、**全体管理者** アカウントを選択します。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="アカウントを選択する" border="false":::
1. メッセージが表示されたら、 **[同意する]** を選択します。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="スクリーンショットには、[Permissions requested Accept for your organization]\(アクセス許可は組織の同意を要求しています\) というメッセージが、詳細および同意を示すオプションと一緒に示されています。" border="false":::
1. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されます。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="[すべてのアプリケーション] ページのスクリーンショット。" lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. 機能しているポイント対サイト環境がまだない場合は、指示に従って作成します。 ポイント対サイト VPN ゲートウェイを作成して構成する方法については、[ポイント対サイト VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照してください。

    > [!IMPORTANT]
    > Basic SKU は OpenVPN ではサポートされていません。

1. VPN ゲートウェイで Azure AD 認証を有効にするには、 **[ポイント対サイトの構成]** に移動し、 **[トンネルの種類]** として **[OpenVPN (SSL)]** を選択します。 **[認証の種類]** として **[Azure Active Directory]** を選択し、 **[Azure Active Directory]** セクションに情報を入力します。

   * **Tenant:** Azure AD テナントの TenantID```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Audience:** "Azure VPN" Azure AD Enterprise アプリの ApplicationID ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **発行者**:セキュリティ トークン サービスの URL ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="Azure VPN" border="false":::

   > [!NOTE]
   > `AadIssuerUri` 値の末尾にスラッシュが含まれていることを確認してください。 それ以外の場合は、接続に失敗する可能性があります。
   >

1. **[VPN クライアントのダウンロード]** リンクをクリックして、プロファイルを作成してダウンロードします。

1. ダウンロードした zip ファイルを解凍します。

1. 解凍された "AzureVPN" フォルダーを参照します。

1. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="next-steps"></a>次のステップ

VPN クライアント プロファイルを作成して構成します。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
