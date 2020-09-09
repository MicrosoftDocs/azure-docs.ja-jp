---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "77608727"
---
## <a name="prerequisites"></a>前提条件
CDN 管理コードを記述する前に、コードが Azure Resource Manager と対話できるように準備する必要があります。 この準備を行うには、次のことを実行する必要があります。

* このチュートリアルで作成する CDN プロファイルを格納するリソース グループを作成する
* Azure Active Directory を構成して、アプリケーションの認証を提供する
* リソース グループにアクセス許可を適用して、Azure AD テナントの承認されたユーザーのみが CDN プロファイルを操作できるようにする

### <a name="creating-the-resource-group"></a>リソース グループの作成
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[リソースの作成]** をクリックします。
3. 「**リソース グループ**」を検索し、[リソース グループ] ウィンドウで **[作成]** をクリックします。

    ![Creating a new resource group](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. リソース グループに *CdnConsoleTutorial* という名前を付けます。  サブスクリプションを選択し、近くの場所を選択します。  必要に応じて **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、リソース グループをポータルのダッシュボードにピン留めできます。  ピン留めすると、後で見つけるのが容易になります。  必要な項目を選択したら、 **[作成]** をクリックします。

    ![Naming the resource group](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. ダッシュボードにリソース グループをピン留めしていない場合は、 **[参照]** 、 **[リソース グループ]** の順にクリックして、作成したリソース グループを見つけることができます。  開くには、そのリソース グループをクリックします。  **サブスクリプション ID**をメモしておきます。 この情報は後で必要になります。

    ![Naming the resource group](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Azure AD アプリケーションの作成とアクセス許可の適用
Azure Active Directory でのアプリの認証には、2 つの手法があります。つまり個別ユーザー、またはサービス プリンシパルです。 サービス プリンシパルは、Windows のサービス アカウントと似ています。  CDN プロファイルとやり取りするアクセス許可を特定のユーザーに付与する代わりに、サービス プリンシパルにアクセス許可を付与します。  通常、サービス プリンシパルは、自動化された非対話型プロセスに使用されます。  このチュートリアルでは対話型コンソール アプリを作成していますが、サービス プリンシパルのアプローチに注目します。

サービス プリンシパルの作成は、Azure Active Directory アプリケーションの作成を含むいくつかの手順で構成されます。  これを作成するには、[こちらのチュートリアルに従います](../articles/active-directory/develop/howto-create-service-principal-portal.md)。

> [!IMPORTANT]
> [リンク先のチュートリアル](../articles/active-directory/develop/howto-create-service-principal-portal.md)のすべての手順を実行してください。  説明に従って正確に手順を完了することが "*重要*" です。  **テナント ID**、**テナントのドメイン名** (ドメイン カスタム ドメインが指定されている場合を除き、通常は *.onmicrosoft.com* ドメイン)、**クライアント ID**、**クライアントの認証キー**をメモしておきます。この情報は後で必要になります。  **クライアント ID** と**クライアントの認証キー**の取り扱いには注意してください。これらの資格情報を使用すると、だれでもサービス プリンシパルとして操作を実行できます。
>
> マルチテナント アプリケーションを構成する手順で、 **[いいえ]** を選択します。
>
> [アプリケーションをロールに割り当てる](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)手順で、前に作成したリソース グループ *CdnConsoleTutorial* を使用します。ただし、**閲覧者**ロールではなく、**CDN プロファイルの投稿者**ロールを割り当てます。  アプリケーションにリソース グループの **CDN プロファイルの投稿者**ロールを割り当てたら、このチュートリアルに戻ります。 
>
>

サービス プリンシパルを作成し、**CDN プロファイルの投稿者**ロールを割り当てた後、リソース グループの **[ユーザー]** ブレードの表示は次の図のようになります。

![Users blade](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>対話型ユーザー認証
サービス プリンシパルの代わりに対話型の個別ユーザー認証を行う場合でも、そのプロセスはサービス プリンシパルを使用する場合と似ています。  実際に行う手順は同じですが、いくつか細かい点が異なります。

> [!IMPORTANT]
> 次の手順は、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。
>
>

1. アプリケーションを作成するときに、 **[Web アプリケーション]** ではなく **[ネイティブ アプリケーション]** を選択します。

    ![ネイティブ アプリケーション](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. 次のページで、**リダイレクト URI** の入力を求められます。  URI は検証されません。入力した内容を覚えておいてください。 この情報は後で必要になります。
3. **クライアント認証キー**を作成する必要はありません。
4. サービス プリンシパルを **CDN プロファイルの投稿者** ロールに割り当てる代わりに、個別ユーザーまたはグループに割り当てます。  この例では、*CDN Demo User* を **CDN プロファイルの投稿者**ロールに割り当てています。  

    ![Individual user access](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
