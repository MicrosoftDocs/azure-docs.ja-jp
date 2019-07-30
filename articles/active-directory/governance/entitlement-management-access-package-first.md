---
title: チュートリアル - Azure AD エンタイトルメント管理 (プレビュー) で最初のアクセス パッケージを作成する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) で最初のアクセス パッケージを作成する方法に関するステップ バイ ステップ チュートリアル。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489168"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>チュートリアル:Azure AD エンタイトルメント管理 (プレビュー) で最初のアクセス パッケージを作成する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) のエンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

グループ、アプリケーション、サイトなど、従業員が必要とするすべてのリソースに対するアクセスを管理することは、組織にとって重要な機能の 1 つです。 生産性を維持するために必要とされる適切なレベルのアクセスを従業員に付与し、不要になったアクセスは削除する必要があります。

このチュートリアルでは、あなたは Woodgrove Bank の IT 管理者として勤務しています。 内部ユーザーがセルフサービスの要求を行える、Web プロジェクト用のリソース パッケージを作成するよう依頼されています。 要求には承認が必要であり、ユーザーのアクセスは 30 日後に有効期限が切れます。 このチュートリアルでは、Web プロジェクトのリソースは 1 つのグループ内のメンバーシップにすぎませんが、グループ、アプリケーション、または SharePoint Online サイトのコレクションである場合もあります。

![シナリオの概要](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * グループをリソースとしてアクセス パッケージを作成する
> * 承認者を指定する
> * 内部ユーザーがどのようにしてアクセス パッケージを要求できるかを示す
> * アクセス要求を承認する

Azure AD Premium P2 または Enterprise Mobility + Security E5 ライセンスをお持ちでない場合は、無料の [Enterprise Mobility + Security E5 Trial](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1) を作成してください。

## <a name="prerequisites"></a>前提条件

Azure AD のエンタイトルメント管理 (プレビュー) を使用するには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="step-1-set-up-users-and-group"></a>手順 1:ユーザーとグループを設定する

リソース ディレクトリには、共有する 1 つ以上のリソースがあります。 この手順では、Woodgrove Bank ディレクトリに **Engineering Group** という名前のグループを作成します。これは、エンタイトルメント管理のターゲット リソースです。 また、内部要求者も設定します。

**必須のロール:** グローバル管理者またはユーザー管理者

![ユーザーとグループを作成する](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. [Azure portal](https://portal.azure.com) にグローバル管理者またはユーザー管理者としてサインインします。  

1. 左側のナビゲーションで、 **[Azure Active Directory]** をクリックします。

1. 次の 2 人のユーザーを作成または構成します。 以下の名前を使用することも、別の名前を使用することもできます。 **Admin1** は、現在サインイン済みのユーザーで構いません。

    | EnableAdfsAuthentication | ディレクトリ ロール | 説明 |
    | --- | --- | --- |
    | **Admin1** | 全体管理者<br/>または<br/>制限付き管理者 (ユーザー管理者) | 管理者かつ承認者 |
    | **Requestor1** | User | 内部要求者 |

    このチュートリアルでは管理者と承認者が同一人物ですが、通常は 1 人以上の人を承認者に指定します。

1. メンバーシップの種類が **[割り当て済み]** で名前が **Engineering Group** という Azure AD セキュリティ グループを作成します。

    このグループは、エンタイトルメント管理のターゲット リソースになります。 開始するグループのメンバーは空である必要があります。

## <a name="step-2-create-an-access-package"></a>手順 2:アクセス パッケージを作成する

*アクセス パッケージ*は、ユーザーがプロジェクトでの作業や業務の遂行に必要とするすべてのリソースのバンドルです。 アクセス パッケージは、"*カタログ*" と呼ばれるコンテナーに定義されます。 この手順では、 **Web project access package** を **[標準]** カタログ内に作成します。

**必須のロール:** グローバル管理者またはユーザー管理者

![アクセス パッケージを作成する](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Azure portal の左側のナビゲーションで、 **[Azure Active Directory]** をクリックします。

1. 左側のメニューで、 **[Identity Governance]** をクリックします

1. 左側のメニューで **[アクセス パッケージ]** をクリックします。  **[アクセスが拒否されました]** と表示される場合は、Azure AD Premium P2 ライセンスがそのディレクトリに存在することを確認してください。

1. **[New access package]\(新しいアクセス パッケージ\)** をクリックします。

    ![Azure portal でのエンタイトルメント管理](./media/entitlement-management-access-package-first/access-packages-list.png)

1. **[基本]** タブで、名前「**Web project access package**」と説明「**エンジニア リング Web プロジェクト用のアクセス パッケージ**」を入力します。

1. **[カタログ]** ドロップダウン リストは **[標準]** に設定したままにします。

    ![[New access package]\(新しいアクセス パッケージ\) - [基本] タブ](./media/entitlement-management-access-package-first/basics.png)

1. **[次へ]** をクリックして、 **[Resource roles]\(リソース ロール\)** タブを開きます。

    このタブでは、アクセス パッケージに含めるアクセス許可を選択します。

1. **[グループ]** をクリックします。

1. [グループの選択] ウィンドウで、先ほど作成した **Engineering Group** グループを見つけて選択します。

    既定では、 **[標準]** カタログ内外のグループが表示されます。 **[標準]** カタログ外のグループを選択すると、 **[標準]** カタログに追加されます。

    ![[New access package]\(新しいアクセス パッケージ\) - [Resource roles]\(リソース ロール\) タブ](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. **[選択]** をクリックして、そのグループを一覧に追加します。

1. **[ロール]** ドロップダウン リストで **[メンバー]** を選択します。

    ![[New access package]\(新しいアクセス パッケージ\) - [Resource roles]\(リソース ロール\) タブ](./media/entitlement-management-access-package-first/resource-roles.png)

1. **[次へ]** をクリックして、 **[ポリシー]** タブを開きます。

1. **[Create first policy]\(最初のポリシーを作成する\)** トグルを **[Later]\(後で\)** に設定します。

    ポリシーは次のセクションで作成します。

    ![[New access package]\(新しいアクセス パッケージ\) - [ポリシー] タブ](./media/entitlement-management-access-package-first/policy.png)

1. **[次へ]** をクリックして、 **[Review + Create]\(確認と作成\)** タブを開きます。

    ![[New access package]\(新しいアクセス パッケージ\) - [Review + Create]\(確認と作成\) タブ](./media/entitlement-management-access-package-first/review-create.png)

1. アクセス パッケージの設定を確認し、 **[作成]** をクリックします。

    カタログがまだ有効になっていないためにアクセス パッケージがユーザーに表示されないというメッセージが表示される場合があります。

    ![[New access package]\(新しいアクセス パッケージ\) - 表示されないというメッセージ](./media/entitlement-management-access-package-first/not-visible.png)

1. Click **OK**.

    しばらくすると、アクセス パッケージが正常に作成されたという通知が表示されます。

## <a name="step-3-create-a-policy"></a>手順 3:ポリシーの作成

*ポリシー*により、アクセス パッケージにアクセスするための規則またはガードレールを定義します。 この手順では、リソース ディレクトリ内の特定のユーザーがアクセス パッケージを要求することを許可するポリシーを作成します。 また、要求の承認が必須であることと承認者を指定します。

![アクセス パッケージのポリシーの作成](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**必須のロール:** グローバル管理者またはユーザー管理者

1. **[Web project access package]** の左側のメニューで **[ポリシー]** をクリックします。

    ![アクセス パッケージのポリシーの一覧](./media/entitlement-management-access-package-first/policies-list.png)

1. **[ポリシーの追加]** をクリックして、[ポリシーの作成] を開きます。

1. 名前「**Internal requestor policy**」と説明「**このディレクトリ内のユーザーが Web プロジェクトのリソースへのアクセスを要求すること許可**」 を入力します。

1. **[Users who can request access]\(アクセスを要求できるユーザー\)** セクションで、 **[For users in your directory]\(ディレクトリ内のユーザー\)** をクリックします。

    ![ポリシーの作成](./media/entitlement-management-access-package-first/policy-create.png)

1. **[ユーザーとグループの選択]** セクションまでスクロールして、 **[ユーザーとグループの追加]** をクリックします。

1. [ユーザーとグループの選択] ウィンドウで、以前に作成した **Requestor1** ユーザーを選択し、 **[選択]** をクリックします。

1. **[要求]** セクションで、 **[承認を要求する]** を **[はい]** に設定します。

1. **[承認者の選択]** セクションで、 **[Add approvers]\(承認者の追加\)** をクリックします。

1. [承認者の選択] ウィンドウで、以前に作成した **Admin1** を選択し、 **[選択]** をクリックします。

    このチュートリアルでは管理者と承認者が同一人物ですが、別の人を承認者として指定できます。

1. **[有効期限]** セクションで、 **[Access package expires]\(アクセス パッケージの有効期限\)** を **[日数]** に設定します。

1. **[Access expires after]\(次を過ぎるとアクセスは期限切れ\)** を **[30]** 日に設定します。

1. **[ポリシーを有効にする]** で、 **[はい]** をクリックします。

    ![ポリシーの作成の設定](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. **[作成]** をクリックして、**Internal requestor policy** を作成します。

1. Web project access package の左側のメニューで **[概要]** をクリックします。

1. **[My Access portal link]\(マイ アクセス ポータルのリンク\)** をコピーします。

    このリンクは次の手順で使用します。

    ![アクセス パッケージの [概要] - [My Access portal link]\(マイ アクセス ポータルのリンク\)](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>手順 4:アクセスの要求

この手順では、**内部要求者**として手順を実行し、アクセス パッケージへのアクセスを要求します。 要求者は、マイ アクセス ポータルと呼ばれるサイトを使用して要求を送信します。 マイ アクセス ポータルを使用すると、要求者は、アクセス パッケージの要求の送信、既にアクセス権を持つアクセス パッケージの参照、要求の履歴の表示ができます。

**必須のロール:** 内部要求者

1. Azure portal からサインアウトします。

1. 新しいブラウザー ウィンドウで、前の手順でコピーしたマイ アクセス ポータルのリンクに移動します。

1. マイ アクセス ポータルに **Requestor1** としてサインインします。

    **Web project access package** が表示されます。

1. 必要に応じて **[説明]** 列で矢印をクリックして、アクセス パッケージに関する詳細を表示します。

    ![マイ アクセス ポータル - [アクセス パッケージ]](./media/entitlement-management-shared/my-access-access-packages.png)

1. チェックマークをクリックして、このパッケージを選択します。

1. **[アクセスの要求]** をクリックして、[アクセスの要求] ウィンドウを開きます。

1. **[業務上の正当な理由]** ボックスに、正当な理由「**Web プロジェクトで作業**」を入力します。

1. **[Request for specific period]\(特定期間の要求\)** トグルを **[はい]** に設定します。

1. **[開始日]** を今日に設定し、 **[終了日]** を明日に設定します。

    ![マイ アクセス ポータル - [アクセスの要求]](./media/entitlement-management-shared/my-access-request-access.png)

1. **[送信]** をクリックします。

1. 左側のメニューで **[Request history]\(要求の履歴\)** をクリックして、要求が送信されたことを確認します。

## <a name="step-5-approve-access-request"></a>手順 5:アクセス要求を承認する

この手順では、**承認者**ユーザーとしてサインインし、内部要求者のアクセス要求を承認します。 承認者は、要求者が要求を送信するときに使用するのと同じマイ アクセス ポータルを使用します。 承認者はマイ アクセス ポータルを使用して、保留中の承認を表示し、要求を承認または拒否することができます。

**必須のロール:** 承認者

1. マイ アクセス ポータルからサインアウトします。

1. [マイ アクセス ポータル](https://myaccess.microsoft.com)に **Admin1** としてサインインします。

1. 左側のメニューで **[承認]** をクリックします。

1. **[保留中]** タブで、**Requestor1** を見つけます。

    Requestor1 の要求が表示されない場合は、数分待ってからもう一度お試しください。

1. **[表示]** リンクをクリックして、[Access request]\(アクセス要求\) ウィンドウを開きます。

1. **[Approve]\(承認\)** をクリックします。

1. **[Reason]\(理由\)** ボックスに、理由「**Web プロジェクトへのアクセスを承認済み**」を入力します。

    ![マイ アクセス ポータル - [Access request]\(アクセス要求\)](./media/entitlement-management-shared/my-access-approve-request.png)

1. **[送信]** をクリックして、決定を送信します。

    正常に承認されたというメッセージが表示されます。

## <a name="step-6-validate-that-access-has-been-assigned"></a>手順 6:アクセス権が割り当てられていることを検証する

アクセス要求を承認したので、この手順では、**内部要求者**がアクセス パッケージを割り当てられ、**Engineering Group** グループのメンバーになっていることを確認します。

**必須のロール:** グローバル管理者またはユーザー管理者

1. マイ アクセス ポータルからサインアウトします。

1. [Azure portal](https://portal.azure.com) に **Admin1** としてサインインします。

1. **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックします。

1. **Web project access package** を見つけてクリックします。

1. 左側のメニューで **[要求]** をクリックします。

    Requestor1 と Internal requestor policy が、 **[Delivered]\(配信済み\)** の状態で表示されます。

1. この要求をクリックして、要求の詳細を表示します。

    ![アクセス パッケージ - 要求の詳細](./media/entitlement-management-access-package-first/request-details.png)

1. 左側のナビゲーションで、 **[Azure Active Directory]** をクリックします。

1. **[グループ]** をクリックして、**Engineering Group** グループを開きます。

1. **[メンバー]** をクリックします。

    **Requestor1** がメンバーとして一覧表示されます。

    ![Engineering Group のメンバー](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>手順 7:リソースのクリーンアップ

この手順では、行った変更を削除し、 **Web project access package** アクセス パッケージを削除します。

**必須のロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. **Web project access package** を開きます。

1. **[割り当て]** をクリックします。

1. **Requestor1** について、省略記号 ( **...** ) をクリックし、 **[Remove access]\(アクセスの削除\)** をクリックします。

    状態は、[Delivered]\(配信済み\) から [Expired]\(期限切れ\) に変更されます。

1. **[ポリシー]** をクリックします。

1. **Internal requestor policy** について、省略記号 ( **...** ) をクリックし、 **[削除]** をクリックします。

1. **[Resource roles]\(リソース ロール\)** をクリックします。

1. **Engineering Group** について、省略記号 ( **...** ) をクリックし、 **[Remove resource role]\(リソース ロールの削除\)** をクリックします。

1. アクセス パッケージの一覧を開きます。

1. **Web project access project** について、省略記号 ( **...** ) をクリックし、 **[削除]** をクリックします。

1. Azure Active Directory で、**Requestor1**、**Admin1** など、作成したユーザーをすべて削除します。

1. **Engineering Group** グループを削除します。

## <a name="next-steps"></a>次の手順

次の記事に進み、エンタイトルメント管理の一般的なシナリオの手順を確認してください。
> [!div class="nextstepaction"]
> [一般的なシナリオ](entitlement-management-scenarios.md)
