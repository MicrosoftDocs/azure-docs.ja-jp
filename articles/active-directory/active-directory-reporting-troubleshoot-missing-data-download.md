---
title: "トラブルシューティング: ダウンロードした Azure Active Directory アクティビティ ログ (プレビュー) の見つからないデータ | Microsoft Docs"
description: "ダウンロードした Azure Active Directory アクティビティ ログ (プレビュー) にデータが見つからない問題の解決策を提供します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/10/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>ダウンロードした Azure Active Directory アクティビティ ログにデータが見つかりません


## <a name="symptoms"></a>現象

アクティビティ ログ (監査またはサインイン) をダウンロードしましたが、選択した期間のレコードがまったく表示されません。 なぜですか? 

 ![レポート](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

Azure Portal でアクティビティ ログをダウンロードする場合は、新しい順に並べ替えられた最新の 12 万件のレコードに制限されます。 

## <a name="resolution"></a>解決策

[Azure AD Reporting API](active-directory-reporting-api-getting-started.md) を利用すると、任意の時点のレコードを最大&100; 万件取得できます。 Reporting API を呼び出して、増分方式で一定期間 (たとえば、毎日や毎週) のレコードを取得するスクリプトを定期的に実行することをお勧めします。

## <a name="next-steps"></a>次のステップ
「[Azure Active Directory レポートに関する FAQ](active-directory-reporting-faq.md)」を参照してください。


