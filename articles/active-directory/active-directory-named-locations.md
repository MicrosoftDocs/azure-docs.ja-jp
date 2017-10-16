---
title: "Azure Active Directory における名前付きの場所 | Microsoft Docs"
description: "名前付きの場所を構成することによって、組織が所有している IP アドレスが、特殊な場所へのあり得ない移動というリスク イベントの種類について誤検出を行うことを回避できます。"
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
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory における名前付きの場所

Azure Active Directory の名前付きの場所機能を使用して、組織内の信頼された IP アドレス範囲にラベル付けできます。 環境内で、[リスク イベント](active-directory-reporting-risk-events.md)の検出のコンテキストで名前付きの場所を使用できます。 この機能によって、種類が "*特殊な場所へのあり得ない移動*" であるリスク イベントの誤検出の報告数が減少します。 

## <a name="configuration"></a>構成

名前付きの場所は、次のように構成します。

1. [Azure ポータル](https://portal.azure.com)にグローバル管理者としてサインインします。

2. 左側のウィンドウで、**[Azure Active Directory]** をクリックします。

    ![左側のウィンドウの [Azure Active Directory] リンク](./media/active-directory-named-locations/01.png)

3. **[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、**[条件付きアクセス]** をクリックします。

    ![[条件付きアクセス] コマンド](./media/active-directory-named-locations/05.png)


4. **[条件付きアクセス]** ブレードの **[管理]** セクションで、**[Named locations] \(名前付きの場所)** をクリックします。

    ![[名前付きの場所] コマンド](./media/active-directory-named-locations/06.png)


5. **[名前付きの場所]** ブレードで、**[新しい場所]** をクリックします。

    ![[新しい場所] コマンド](./media/active-directory-named-locations/07.png)


6. **[新規]** ブレードで、次の操作を行います。

    ![[新規] ブレード](./media/active-directory-named-locations/56.png)

    a. **[名前]** ボックスに、名前付きの場所の名前を入力します。

    b. **[IP 範囲]** ボックスに、IP 範囲を入力します。 IP 範囲は *Classless Inter-Domain Routing* (CIDR) 形式である必要があります。  

    c. **Create** をクリックしてください。



## <a name="what-you-should-know"></a>知っておくべきこと

**一括更新**: 一括更新で名前付きの場所を作成または更新する場合は、IP 範囲を含む CSV ファイルをアップロードまたはダウンロードできます。 アップロードすると、ファイル内の IP 範囲が一覧に追加されます。一覧の内容は上書きされません。

![[アップロード] リンクと [ダウンロード] リンク](./media/active-directory-named-locations/09.png)


**制限事項**: 最大 60 の名前付き場所を定義でき、それぞれに 1 つの IP 範囲を割り当てることができます。 構成する名前付き場所が 1 つだけの場合、そこには最大 500 の IP 範囲を定義できます。


## <a name="next-steps"></a>次のステップ

リスク イベントの詳細については、「[Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)」を参照してください。

