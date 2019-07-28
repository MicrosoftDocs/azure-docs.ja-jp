---
title: トラブルシューティング:ダウンロードした Azure Active Directory アクティビティ ログにデータが見つからない | Microsoft Docs
description: ダウンロードした Azure Active Directory アクティビティ ログにデータが見つからない問題の解決策を提供します。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437102"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>ダウンロードした Azure Active Directory アクティビティ ログにすべてのデータが表示されません。

## <a name="symptoms"></a>現象

アクティビティ ログ (監査またはサインイン) をダウンロードしましたが、選択した期間のレコードがまったく表示されません。 なぜですか? 

 ![レポート](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

Azure portal でアクティビティ ログをダウンロードする場合は、新しい順に並べ替えられた最新の 250,000 件のレコードに制限されます。 

## <a name="resolution"></a>解決策

[Azure AD Reporting API](concept-reporting-api.md) を利用すると、任意の時点のレコードを最大 100 万件取得できます。

## <a name="next-steps"></a>次の手順

* [Azure Active Directory レポートの FAQ](reports-faq.md)

