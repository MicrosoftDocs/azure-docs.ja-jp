---
title: "Azure Active Directory における名前付きネットワーク | Microsoft Docs"
description: "名前付きネットワークを構成すると、組織が所有する IP アドレスによって、&quot;複数の地域からのサインイン&quot; リスク イベントおよび &quot;疑わしいアクティビティを示す IP アドレスからのサインイン&quot; リスク イベントに対して誤検出が生成されないようにできます。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Azure Active Directory における名前付きネットワーク

> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-known-networks-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-known-networks.md)
>
>


Azure Active Directory では、検出された[リスク イベント](active-directory-identity-protection-risk-events.md)ごとにレコードが作成されます。 Azure Active Directory セキュリティ レポートのリスク イベント情報により、環境内でユーザー アカウントが侵害されている確率を調べることができます。   

Azure Active Directory が、組織が実際に所有する IP アドレスに対して、[リスク イベントの種類](active-directory-reporting-risk-events.md#risk-event-types)として "*特殊な場所へのあり得ない移動*" と "*不審なアクティビティを示す IP アドレスからのサインイン*" を誤検出することがあります。 

これは、たとえば次の場合に起こります。

- ボストン支社のユーザーがサンフランシスコのデータ センターにリモートでサインインしたときに、"*複数の地域からのサインイン*" リスク イベントが生成される

- 組織のユーザーが間違ったパスワードで複数回サインインしようとして、"*疑わしいアクティビティを示す IP アドレスからのサインイン*" リスク イベントが生成される

こうした場合に誤解を招くリスク イベントが生成されないようにするには、指定した IP アドレス範囲を組織のパブリック IP アドレスの一覧に追加する必要があります。    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>組織のパブリック IP アドレス範囲を追加するには、次の手順に従います。

1. Microsoft Azure 管理ポータルにサインインします。

2. 左側のウィンドウで、 **[Active Directory]**をクリックします。

    ![既知のネットワーク](./media/active-directory-known-networks-azure-portal/01.png)

3. ディレクトリ ブレードの **[管理]** セクションで、**[Named networks (名前付きネットワーク)]** をクリックします。

    ![既知のネットワーク](./media/active-directory-known-networks-azure-portal/02.png)


4. **[場所の追加]** をクリックします

    ![既知のネットワーク](./media/active-directory-known-networks-azure-portal/03.png)

5. **[追加]** ブレードで、次の操作を行います。

    ![既知のネットワーク](./media/active-directory-known-networks-azure-portal/04.png)

    a. **[名前]** ボックスに、名前を入力します。

    b. **[IP 範囲]** ボックスに、IP 範囲を入力します。 IP 範囲は CIDR 形式で指定します。 一括更新の場合は、IP 範囲を含む CSV ファイルをアップロードできます。 アップロードすると、ファイル内の IP 範囲が一覧に追加されます。一覧の内容は上書きされません。

    c. **[作成]**をクリックします。


**その他のリソース:**

* [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)
* [不審なアクティビティのある IP アドレスからのサインイン](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [複数の地域からのサインイン](active-directory-reporting-sign-ins-from-multiple-geographies.md)

