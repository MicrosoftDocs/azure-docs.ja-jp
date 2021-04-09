---
title: Azure App Configuration REST API - 認可
description: Azure App Configuration REST API を使用した認可についての参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932644"
---
# <a name="authorization"></a>承認

認可とは、要求を行うときに呼び出し元が持つアクセス許可を決定するために使用される手順のことです。 複数の認可モデルがあります。 要求に使用される認可モデルは、使用される[認証](./rest-api-authentication-index.md)方法によって異なります。 以下に認可モデルの一覧を示します。

## <a name="hmac"></a>HMAC

HMAC 認証に関連付けられている[認可モデル](./rest-api-authorization-hmac.md) モデルでは、アクセス許可が、読み取り専用または読み取り/書き込みに分割されています。 詳細については、[HMAC 認可](./rest-api-authorization-hmac.md)に関するページを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 認証に関連付けられている[認可モデル](./rest-api-authorization-azure-ad.md)では、Azure RBAC を使用してアクセス許可を制御します。 詳細については、[Azure AD 認可](./rest-api-authorization-azure-ad.md)に関するページを参照してください。
