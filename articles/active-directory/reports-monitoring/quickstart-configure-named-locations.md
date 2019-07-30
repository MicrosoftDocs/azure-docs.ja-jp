---
title: Azure Active Directory でネームド ロケーションを構成する | Microsoft Docs
description: ネームド ロケーションを構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12991813f68a42f9846c1c9c9c31c01d371d1d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107632"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>クイック スタート:Azure Active Directory でネームド ロケーションを構成する

ネームド ロケーションを使うと、組織内の信頼できる IP アドレス範囲にラベルを付けることができます。 Azure AD では、次のためにネームド ロケーションを使用します。
- [リスク イベント](concept-risk-events.md)で誤判定を検出する。 信頼できる場所からサインインすることで、ユーザーのサインイン リスクが低下します。   
- [場所ベースの条件付きアクセス](../conditional-access/location-condition.md)を構成する。

このクイック スタートでは、環境内でネームド ロケーションを構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

* Azure AD テナント。 [無料試用版](https://azure.microsoft.com/trial/get-started-active-directory/)にサインアップします。 
* テナントの全体管理者であるユーザー。
* 組織内で確立され、信頼できる IP の範囲。 IP の範囲は **Classless Inter-Domain Routing (CIDR)** 形式である必要があります。

## <a name="configure-named-locations"></a>ネームド ロケーションの構成

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のウィンドウで、 **[Azure Active Directory]** を選択し、 **[セキュリティ]** セクションから **[条件付きアクセス]** を選択します。

    ![[条件付きアクセス] タブ](./media/quickstart-configure-named-locations/entrypoint.png)

3. **[条件付きアクセス]** ページで **[ネームド ロケーション]** を選択し、 **[新しい場所]** を選択します。

    ![ネームド ロケーション](./media/quickstart-configure-named-locations/namedlocation.png)

6. 新しいページのフォームに入力します。 

   * **[名前]** ボックスに、ネームド ロケーションの名前を入力します。
   * **[IP 範囲]** ボックスに、IP 範囲を CIDR 形式で入力します。  
   * **Create** をクリックしてください。
    
     ![[新規] ブレード](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [Azure AD の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)。
- [Azure AD の条件付きアクセスの場所の条件](../conditional-access/location-condition.md)
- [リスクの高いサインイン レポート](concept-risky-sign-ins.md)。  
