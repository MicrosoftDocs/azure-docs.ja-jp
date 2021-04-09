---
title: Azure AD Connect クラウド同期のオンデマンド プロビジョニング
description: この記事では、Azure AD Connect のクラウド同期機能を使用して、構成の変更をテストする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554277"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期のオンデマンド プロビジョニング

Azure Active Directory (Azure AD) Connect のクラウド同期機能を使用すると、これらの変更を 1 人のユーザーに適用して、構成の変更をテストできます。 このオンデマンド プロビジョニングでは、構成に対する変更が正しく適用されたこと、および Azure AD と正しく同期されていることを確認および検証できます。  

> [!IMPORTANT] 
> オンデマンド プロビジョニングを使用した場合、選択したユーザーにはスコープ フィルターは適用されません。 オンデマンド プロビジョニングは、指定した組織単位外のユーザーに使用できます。

## <a name="validate-a-user"></a>ユーザーを検証する
オンデマンド プロビジョニングを使用するには、次の手順を実行します。

1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[Azure AD Connect]** を選びます。
3.  **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。

    ![クラウドへの同期を管理するリンクを示すスクリーンショット。](media/how-to-install/install-6.png)
4. **[構成]** の下で、自分の構成を選択します。
5. **[検証]** で、 **[ユーザーのプロビジョニング]** ボタンを選択します。 

   ![ユーザーをプロビジョニングするボタンを示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-2.png)

6. **[Provision on demand]** \(オンデマンド プロビジョニング\) 画面で、ユーザーの識別名を入力し、 **[プロビジョニング]** ボタンを選択します。  
 
   ![ユーザー名と [プロビジョニング] ボタンを示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-3.png)
7. プロビジョニングが完了すると、4 つの緑色のチェックマークが表示された成功画面が表示されます。 エラーがある場合は、左側に表示されます。

   ![プロビジョニングの成功を示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>プロビジョニングの詳細を取得する
これで、ユーザー情報を調べて、構成で行った変更が適用されているかどうかを確認できます。 この記事の残りでは、ユーザー同期の成功の詳細に表示される個々のセクションについて説明します。

### <a name="import-user"></a>ユーザーをインポートする
**[ユーザーのインポート]** セクションには、Active Directory からインポートされたユーザーの情報が表示されます。 これは、Azure AD にプロビジョニングされる前のユーザーについてです。 この情報の表示には、 **[詳細の表示]** リンクを選択します。

![インポートされたユーザーの詳細を表示するボタンのスクリーンショット。](media/how-to-on-demand-provision/on-demand-5.png)

この情報を使用すると、インポートされたさまざまな属性 (とその値) を確認できます。 カスタム属性のマッピングを作成した場合は、ここでその値を確認できます。

![ユーザーの詳細を示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>ユーザーがスコープ内にあるかどうかを確認する
**[ユーザーがスコープ内にあるかどうかを確認]** セクションには、Azure AD にインポートしたユーザーがスコープ内にあるかどうかを示す情報が表示されます。 この情報の表示には、 **[詳細の表示]** リンクを選択します。

![ユーザーのスコープの詳細を表示するボタンのスクリーンショット。](media/how-to-on-demand-provision/on-demand-7.png)

この情報を使用すると、ユーザーがスコープ内にあるかどうかを確認できます。

![ユーザーのスコープの詳細を示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>ソースおよびターゲット システム間でユーザーを照合
**[ソース システムとターゲット システム間でユーザーを照合]** セクションには、ユーザーが既に Azure AD に存在するか、および新しいユーザーをプロビジョニングするのではなく結合するかどうかに関する情報があります。 この情報の表示には、 **[詳細の表示]** リンクを選択します。

![一致するユーザーの詳細を表示するボタンのスクリーンショット。](media/how-to-on-demand-provision/on-demand-8.png)

この情報を使用すると、一致が見つかったか、新しいユーザーが作成されるかを確認できます。

![ユーザー情報を示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-11.png)

照合の詳細メッセージには、次の 3 つの操作のいずれかが表示されます。
- **作成**: ユーザーが Azure AD に作成されます。
- **更新**: ユーザーが構成に加えられた変更に基づいて更新されます。
- **削除**: ユーザーが Azure AD から削除されます。

このメッセージは、実行した操作の種類によって異なります。

### <a name="perform-action"></a>実行するアクション
**[アクションの実行]** セクションには、構成の適用後に Azure AD にプロビジョニングまたはエクスポートされたユーザーに関する情報が表示されます。 これは、Azure AD にプロビジョニングされた後のユーザーについてです。 この情報の表示には、 **[詳細の表示]** リンクを選択します。

![実行したアクションの詳細を表示するボタンのスクリーンショット。](media/how-to-on-demand-provision/on-demand-9.png)

この情報を使用すると、構成の適用後の属性値を確認できます。 インポートしたものと似ていますか、または違いますか。 正常に構成を適用できましたか。  

この手順では、クラウドからお使いの Azure AD テナントに移行される属性の変換をトレースできます。

![トレースしている属性の詳細を示すスクリーンショット。](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [Azure AD Connect クラウド同期のインストール](how-to-install.md)
 