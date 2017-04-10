---
title: "トラブルシューティング: Azure Active Directory アクティビティ ログ (プレビュー) の見つからないデータ | Microsoft Docs"
description: "解決策を提供します。Azure Active Directory プレビューで使用可能なさまざまなレポートの一覧を示します。"
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
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: ebc92bc23201dbcd01f90e0cc3d5055fb85820be
ms.lasthandoff: 03/10/2017


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


