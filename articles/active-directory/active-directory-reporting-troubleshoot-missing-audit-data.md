---
title: "トラブルシューティング: Azure Active Directory アクティビティ ログにデータが見つからない | Microsoft Docs"
description: "Azure Active Directory で使用可能なさまざまなレポートの一覧を示します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 18af51e95a283a5cd33688484a0d7477eb4b957d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/08/2017

---

# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>実行した操作の一部が Azure Active Directory アクティビティ ログに見つかりません


## <a name="symptoms"></a>現象

Azure Portal でいくつかの操作を実行したので、`Activity logs > Audit Logs` ブレードでこれらの操作の監査ログが表示されるはずですが、見つかりません。

 ![レポート](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>原因

操作はアクティビティの監査ログにすぐには表示されません。 ポータルの監査ログに表示されるまでには、操作を実行してから 15 分～ 1 時間かかります。

## <a name="resolution"></a>解決策

15 分～ 1 時間待ってから、操作のログが表示されるかどうかを確認します。 それでも表示されない場合は、サポート チケットを発行してください。Microsoft が調査します。


## <a name="next-steps"></a>次のステップ
「[Azure Active Directory レポートに関する FAQ](active-directory-reporting-faq.md)」を参照してください。


