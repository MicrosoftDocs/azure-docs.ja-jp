---
title: "Azure クラシック ポータルの既知のネットワーク | Microsoft Docs"
description: "既知のネットワークを構成すると、&quot;複数の地域からのサインイン&quot; レポートおよび &quot;疑わしいアクティビティを示す IP アドレスからのサインイン&quot; レポートに、組織が所有する IP アドレスが含まれないようにすることができます。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4
ms.lasthandoff: 01/19/2017


---
# <a name="known-networks"></a>既知のネットワーク

> [!div class="op_single_selector"]
> * [Azure クラシック ポータル](active-directory-known-networks.md)
> * [Azure ポータル](active-directory-known-networks-azure-portal.md)
> 
> 


Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

"*複数の地域からのサインイン*" レポートと "*疑わしいアクティビティを示す IP アドレスからのサインイン*" レポートで、組織で実際に所有している IP アドレスに対してフラグが不適切に設定される場合が考えられます。 

これは、たとえば次の場合に起こります。 

* ボストン支社のユーザーがサンフランシスコのデータ センターにリモートでサインインすると、"複数の地域からのサインイン" レポートがトリガーされます。 
* 組織のユーザーが間違ったパスワードで複数回サインインしようとすると、"疑わしいアクティビティを示す IP アドレスからのサインイン" レポートがトリガーされます。 

こうした場合に誤解を招くセキュリティ レポートが生成されないようにするには、組織の パブリック IP アドレスの一覧に既知の IP アドレス範囲を追加する必要があります。    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>組織のパブリック IP アドレス範囲を追加するには、次の手順に従います。

1. [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。

2. 左側のウィンドウで、 **[Active Directory]**をクリックします。 

    ![既知のネットワーク](./media/active-directory-known-networks/known-netwoks-01.png)

3. **[ディレクトリ]** タブでディレクトリを選択します。

4. 上部のメニューで **[構成]**をクリックします。 

    ![既知のネットワーク](./media/active-directory-known-networks/known-netwoks-02.png)

5. [構成] タブで、**[組織のパブリック IP アドレス範囲]** に移動します。 

    ![既知のネットワーク](./media/active-directory-known-networks/known-netwoks-03.png)

6. **[既知の IP アドレス範囲の追加]**をクリックします。

7. 表示されるダイアログ ボックスでアドレス範囲を追加し、完了したらチェック ボタンをクリックします。 

    ![既知のネットワーク](./media/active-directory-known-networks/known-netwoks-04.png)

**その他のリソース:**

* [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)
* [不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md)


