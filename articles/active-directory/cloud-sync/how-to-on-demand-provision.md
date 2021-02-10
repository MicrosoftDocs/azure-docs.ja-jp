---
title: Azure AD Connect クラウド同期のオンデマンド プロビジョニング
description: この記事では、オンデマンド プロビジョニング機能について説明します。
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
ms.openlocfilehash: 6ac186d4b460165605ccf0fc53bdb0b691348bf3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622526"
---
# <a name="azure-ad-connect-cloud-sync-on-demand-provisioning"></a>Azure AD Connect クラウド同期のオンデマンド プロビジョニング

Azure AD Connect クラウド同期では、これらの変更を 1人のユーザーに適用することで、構成の変更をテストできる新機能が導入されました。  これを使用して、構成に対して行われた変更が正しく適用されたこと、および Azure AD に正しく同期されていることの妥当性を確認し、検証することができます。  

> [!IMPORTANT] 
> オンデマンド プロビジョニングを使用する場合は、選択したユーザーにスコープ フィルターが適用されません。  これは、指定した OU の外部にあるユーザーにオンデマンド プロビジョニングを使用できることを意味します。


## <a name="using-on-demand-provisioning"></a>オンデマンド プロビジョニングを使用する
新しい機能を使用するには、次の手順に従ってください。


1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[Azure AD Connect]** を選びます。
3.  **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。

    ![プロビジョニングを管理する](media/how-to-install/install-6.png)
4. **[構成]** の下で、自分の構成を選択します。
5. **[検証]** で、 **[ユーザーのプロビジョニング]** ボタンをクリックします。 

 ![ユーザーをプロビジョニングする](media/how-to-on-demand-provision/on-demand-2.png)

6. [オンデマンド プロビジョニング] 画面上で。  ユーザーの **識別名** を入力し、 **[プロビジョニング]** ボタンをクリックします。  
 
 ![オンデマンド プロビジョニング](media/how-to-on-demand-provision/on-demand-3.png)
7. 完了すると、成功の画面と、正常にプロビジョニングされたことを示す 4 つの緑色のチェック ボックスが表示されます。  エラーが発生すると、左側に表示されます。

  ![Success](media/how-to-on-demand-provision/on-demand-4.png)

これで、ユーザーを調べて、構成で行った変更が適用されているかどうかを確認できます。  このドキュメントの残りの部分では、正常に同期されたユーザーの詳細に表示される個々のセクションについて説明します。

## <a name="import-user-details"></a>ユーザーの詳細のインポート
このセクションでは、Active Directory からインポートされたユーザーに関する情報を提供します。  これは、Azure AD にプロビジョニングされる前にユーザーがどのように表示されるかを示しています。  この情報を表示するには、 **[View Details (表示の詳細)]** リンクをクリックします。

![ユーザーをインポートする](media/how-to-on-demand-provision/on-demand-5.png)

この情報を使用すると、インポートされたさまざまな属性とその値を確認できます。  カスタム属性マッピングを作成した場合は、ここで値を確認できます。
![ユーザーの詳細のインポート](media/how-to-on-demand-provision/on-demand-6.png)

## <a name="determine-if-user-is-in-scope-details"></a>ユーザーがスコープ内にあるかどうかを確認する
このセクションでは、Azure AD からインポートされたユーザーがスコープ内にあるかどうかに関する情報を提供します。  この情報を表示するには、 **[View Details (表示の詳細)]** リンクをクリックします。

![ユーザー スコープ](media/how-to-on-demand-provision/on-demand-7.png)

この情報を使用して、ユーザーのスコープに関する追加情報を確認できます。

![ユーザースコープの詳細](media/how-to-on-demand-provision/on-demand-10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>ソースとターゲットのシステム詳細の間でユーザーを照合する
このセクションでは、ユーザーが既に Azure AD に存在しているかどうか、および新しいユーザーをプロビジョニングする代わりに参加する必要があるかどうかについて説明します。  この情報を表示するには、 **[View Details (表示の詳細)]** リンクをクリックします。
![詳細の表示](media/how-to-on-demand-provision/on-demand-8.png)

この情報を使用すると、一致が見つかったか、新しいユーザーが作成されるかを確認できます。

![ユーザー情報](media/how-to-on-demand-provision/on-demand-11.png)

一致の詳細には、次の3つの操作のいずれかを含むメッセージが表示されます。  これらは次のとおりです。
- 作成-ユーザーは Azure AD で作成されます。
- 更新-ユーザーは、構成に加えられた変更に基づいて更新されます。
- 削除 - Azure AD からユーザーが削除されます。

実行した操作の種類によって、メッセージは異なります。

## <a name="perform-action-details"></a>アクションの詳細を実行
このセクションでは、構成の適用後に Azure AD にプロビジョニングまたはエクスポートされたユーザーに関する情報を提供します。  これは、Azure AD にプロビジョニングされるとユーザーがどのように表示されるかを示しています。  この情報を表示するには、 **[View Details (表示の詳細)]** リンクをクリックします。
![アクションの詳細を実行](media/how-to-on-demand-provision/on-demand-9.png)

この情報を使用して、構成が適用された後に属性の値を確認できます。  インポートされたものと同じように見えますか、または違うように見えますか。  構成は正常に適用されますか。  

これにより、クラウドと Azure AD テナントの間を移動するときに、属性変換をトレースできるようになります。

![トレース属性](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [Azure AD Connect クラウド同期をインストールする方法](how-to-install.md)
 