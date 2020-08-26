---
title: Azure AD のエンタイトルメント管理で接続されている組織を追加する - Azure Active Directory
description: 自分の組織外のユーザーがアクセス パッケージを要求して、プロジェクトの共同作業を行うことができるようにする方法について説明します。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4869859e11cb6c0cc868ec9deacb3e5cb972c6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586573"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理で接続されている組織を追加する

Azure Active Directory (Azure AD) のエンタイトルメント管理を利用すると、自分の組織外のユーザーと共同作業を行うことができます。 外部 Azure AD ディレクトリまたはドメイン内のユーザーと頻繁に共同作業を行う場合は、それらのユーザーを接続された組織として追加できます。 この記事では、接続された組織を追加して、自分の組織外のユーザーが自分のディレクトリ内のリソースを要求できるようにする方法について説明します。

## <a name="what-is-a-connected-organization"></a>接続されている組織とは

接続されている組織とは、ご自身と関係のある外部 Azure AD ディレクトリまたはドメインです。

たとえば、あなたは Woodgrove Bank で働いていて、2 つの外部組織と共同作業を行う必要があるとします。 この2つの組織の構成は異なります。

- Graphic Design Institute では、Azure AD を使用しており、ユーザーは、末尾が *graphicdesigninstitute.com* のユーザー プリンシパル名を持っています。
- Contoso ではまだ Azure AD を使用していません。 Contoso のユーザーは、末尾が *contoso.com* のユーザー プリンシパル名を持っています。

この場合、2 つの接続された組織を構成できます。 Graphic Design Institute 用に 1 つの接続された組織、そして Contoso 用にもう 1 つを作成します。 その後、これらの 2 つの接続された組織をポリシーに追加すると、ポリシーに一致するユーザー プリンシパル名を持つ各組織のユーザーがアクセス パッケージを要求できるようになります。 *graphicdesigninstitute.com* のドメインを持つユーザー プリンシパル名を使用するユーザーは、Graphic Design Institute の接続された組織に一致し、要求を送信することを許可されます。 *contoso.com* のドメインを持つユーザー プリンシパル名を使用するユーザーは、Contoso の接続された組織に一致し、同様にパッケージの要求を許可されます。 そして、Graphic Design Institute は Azure AD を使用しているため、[検証済みドメイン](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)に一致するプリンシパル名を持つユーザーはテナントに追加されます。たとえば、*graphicdesigninstitute.example* も同じポリシーを使用してアクセス パッケージを要求することができます。

![接続されている組織の例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD ディレクトリまたはドメインのユーザーの認証方法は、認証の種類によって異なります。 接続された組織の認証の種類は次のとおりです。

- Azure AD
- [直接フェデレーション](../b2b/direct-federation.md)
- [ワンタイム パスコード](../b2b/one-time-passcode.md) (ドメイン)

接続された組織を追加する方法のデモについては、次のビデオをご覧ください。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>接続されている組織の追加

外部 Azure AD ディレクトリまたはドメインを接続された組織として追加するには、このセクションの手順のようにします。

**事前に必要なロール**:"*グローバル管理者*" または "*ユーザー管理者*"

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。

1. 左側のペインで、 **[接続されている組織]** を選択した後、 **[接続されている組織の追加]** を選択します。

    ![[接続されている組織の追加] ボタン](./media/entitlement-management-organization/connected-organization.png)

1. **[基本]** タブを選択し、組織の表示名と説明を入力します。

    ![[接続されている組織の追加] の [基本] ペイン](./media/entitlement-management-organization/organization-basics.png)

1. **[ディレクトリとドメイン]** タブを選択し、 **[ディレクトリとドメインの追加]** を選択します。

    **[ディレクトリとドメインの選択]** ペインが開きます。

1. 検索ボックスにドメイン名を入力して、Azure AD ディレクトリまたはドメインを検索します。 必ずドメイン名全体を入力してください。

1. 組織名と認証の種類が正しいことを確認します。 ユーザーのサインイン方法は、認証の種類によって異なります。

    ![[ディレクトリとドメインの選択] ペイン](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. **[追加]** を選択して、Azure AD ディレクトリまたはドメインを追加します。 現時点では、接続されている組織ごとに Azure AD ディレクトリまたはドメインを 1 つのみ追加できます。

    > [!NOTE]
    > Azure AD ディレクトリまたはドメインのすべてのユーザーはこのアクセス パッケージを要求できます。 これには、ディレクトリに関連付けられている Azure AD 内のすべてのサブドメインのユーザーが含まれます。ただし、それらのドメインが Azure AD 企業間 (B2B) の許可リストまたは拒否リストによってブロックされている場合を除きます。 詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](../b2b/allow-deny-list.md)」を参照してください。

1. Azure AD ディレクトリまたはドメインを追加した後、 **[選択]** を選択します。

    組織が一覧に表示されます。

    ![[ディレクトリとドメイン] ペイン](./media/entitlement-management-organization/organization-directory-domain.png)

1. **[スポンサー]** タブを選択し、この接続された組織のオプションのスポンサーを追加します。

    スポンサーとは、この接続された組織との関係の連絡先となる、自分のディレクトリ内に既に存在する内部または外部のユーザーです。 内部スポンサーは、自分のディレクトリ内のメンバー ユーザーです。 外部スポンサーは、以前に招待され、自分のディレクトリ内に既に存在する、接続されている組織のゲスト ユーザーです。 スポンサーは、この接続されている組織内のユーザーがこのアクセス パッケージへのアクセスを要求したときに、承認者として利用できます。 ゲスト ユーザーを自分のディレクトリに招待する方法については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../b2b/add-users-administrator.md)」を参照してください。

    **[追加/削除]** を選択すると開くペインで、組織内または組織外のスポンサーを選択できます。 このウィンドウには、自分のディレクトリ内のユーザーとグループのフィルター処理されていない一覧が表示されます。

    ![[スポンサー] ペイン](./media/entitlement-management-organization/organization-sponsors.png)

1. **[確認および作成]** タブを選択し、自分の組織の設定を確認して、 **[作成]** を選択します。

    ![[確認および作成] ペイン](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>接続されている組織の更新 

接続されている組織を別のドメインに変更する場合、組織の名前を変更する場合、またはスポンサーを変更する場合は、このセクションの手順のようにして、接続されている組織を更新できます。

**事前に必要なロール**:"*グローバル管理者*" または "*ユーザー管理者*"

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。

1. 左側のペインで、 **[接続されている組織]** を選択した後、接続されている組織を選択して開きます。

1. 接続されている組織の概要ペインで、 **[編集]** を選択して、組織の名前または説明を変更します。  

1. **[ディレクトリとドメイン]** ページで、 **[ディレクトリとドメインを更新する]** を選択して、別のディレクトリまたはドメインに変更します。

1. **[スポンサー]** ページで、 **[内部スポンサー追加]** または **[外部スポンサーの追加]** を選択して、ユーザーをスポンサーとして追加します。 スポンサーを削除するには、スポンサーを選択し、右側のペインで **[削除]** を選択します。


## <a name="delete-a-connected-organization"></a>接続されている組織の削除

外部の Azure AD ディレクトリまたはドメインとの関係がなくなった場合は、接続されている組織を削除できます。

**事前に必要なロール**:"*グローバル管理者*" または "*ユーザー管理者*"

1. Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。

1. 左側のペインで、 **[接続されている組織]** を選択した後、接続されている組織を選択して開きます。

1. 接続されている組織の概要ウィンドウで、 **[削除]** を選択して削除します。

    現時点では、接続されているユーザーがいない場合にのみ、接続されている組織を削除できます。

    ![接続されている組織の [削除] ボタン](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>プログラムによる接続された組織の管理

Microsoft Graph を使用して、接続されている組織を作成、一覧表示、更新、および削除することもできます。 委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを持つ適切なロールのユーザーは、API を呼び出して、[connectedOrganization](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta) オブジェクトを管理し、それに対してスポンサーを設定できます。

## <a name="next-steps"></a>次のステップ

- [外部ユーザーのアクセス権の管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [自分のディレクトリ外のユーザーのアクセス権の管理](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
