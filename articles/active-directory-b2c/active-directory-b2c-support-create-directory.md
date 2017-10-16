---
title: "Azure Active Directory B2C: テナント作成のトラブルシューティング | Microsoft Docs"
description: "Azure Active Directory または Azure Active Directory B2C テナント作成の問題と解決策"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Azure Active Directory または Azure Active Directory B2C テナントの作成に関するトラブルシューティング 

## <a name="create-an-azure-ad-tenant"></a>Azure AD テナントを作成する
最初の試行で Azure Active Directory (Azure AD) テナントを作成できない場合は、もう一度やり直してください。 問題が解決しない場合は、Azure サポートにお問い合わせください。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する
[Azure Active Directory B2C (Azure AD B2C) テナントの作成](active-directory-b2c-get-started.md)で問題が発生した場合は、次のオプションを試してください。

* Azure AD B2C テナントがテナントの一覧に表示されない場合は、もう一度テナントを作成し直してください。
* Azure AD B2C テナントがテナントの一覧に表示され、次のエラー メッセージが表示されたときは、そのテナントを削除して、もう一度作成してください。

    "B2C テナント 'contosob2c' の作成を完了できませんでした。 ガイダンスについては、この[リンク](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)を参照してください。"
* 既存の Azure AD B2C テナントを削除し、同じドメイン名を使用して再作成したときに発生する既知の問題があります。 新しい Azure AD B2C テナントを作成する場合は、別のドメイン名を使用する必要があります。
* これらの解決策で問題が解決しない場合は、Azure サポートに問い合わせてください。 詳細については、[Azure AD B2C のサポート要求の提出](active-directory-b2c-support.md)に関する記事を参照してください。

