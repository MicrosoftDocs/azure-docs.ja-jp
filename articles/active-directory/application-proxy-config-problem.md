---
title: "アプリケーション プロキシ アプリケーションの作成時に発生する問題 | Microsoft Docs"
description: "Azure AD 管理ポータルでアプリケーション プロキシ アプリケーションを作成する際に発生する問題のトラブルシューティング方法"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 7551c290858251b6dbbd131049dbee14e5353785
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="problem-creating-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションの作成時に発生する問題 

ここでは、新しいアプリケーション プロキシ アプリケーションを作成する際によく発生する問題をいくつか取り上げます。

## <a name="recommended-documents"></a>推奨されるドキュメント 

管理ポータルでのアプリケーション プロキシ アプリケーションの作成の詳細については、「[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)」を参照してください。

同ドキュメントの手順に従ってアプリケーションを作成しているときにエラーが発生した場合、アプリケーションの修復方法に関する情報や推奨事項については、そのエラーの詳細を見て確認してください。 ほとんどのエラー メッセージには、推奨される解決方法が記載されています。 

## <a name="specific-things-to-check"></a>具体的なチェック項目

一般的なエラーを避けるために、以下の点を確認してください。

-   アプリケーション プロキシ アプリケーションする権限を持った管理者であること。

-   内部 URL が一意であること。

-   外部 URL が一意であること。

-   URL が http または https から始まり、"/" で終わっていること。

-   URL がドメイン名であって IP アドレスではないこと。

アプリケーションを作成する際、エラー メッセージは右上隅に表示されます。 通知アイコンを選択してエラー メッセージを表示することもできます。

   ![通知アイコン](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>次のステップ
[Azure Portal でアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)

