---
title: Azure App Configuration REST API - 認証
description: Azure App Configuration REST API を使用した認証についての参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932695"
---
# <a name="authentication"></a>認証

すべての HTTP 要求が認証される必要があります。 以下の認証スキームがサポートされています。

## <a name="hmac"></a>HMAC

[HMAC 認証](./rest-api-authentication-hmac.md)では、ランダムに生成されたシークレットを使用して要求ペイロードに署名します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[HMAC 認証](./rest-api-authorization-hmac.md)に関するセクションを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 認証](../active-directory/authentication/overview-authentication.md)では、Azure Active Directory から取得されるベアラー トークンを利用して要求を認証します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[Azure AD 認証](./rest-api-authorization-azure-ad.md)に関するセクションを参照してください。