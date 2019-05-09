---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795899"
---
`objectIdType` (または**オブジェクト識別子の型**) は、ロールに与えられる ID の型を示します。 `DeviceId` および `UserDefinedFunctionId` 型を除き、オブジェクト識別子の型は Azure Active Directory オブジェクトのプロパティに対応します。

次の表に、Azure Digital Twins でサポートされているオブジェクト識別子の型を示します。

| type | 説明 |
| --- | --- |
| UserId | ユーザーにロールを割り当てます。 |
| deviceId | デバイスにロールを割り当てます。 |
| DomainName | ドメイン名にロールを割り当てます。 指定されたドメイン名を持つ各ユーザーには、対応するロールのアクセス権が与えられます。 |
| TenantId | テナントにロールを割り当てます。 指定された Azure AD テナント ID に属する各ユーザーには、対応するロールのアクセス権が与えられます。 |
| ServicePrincipalId | サービス プリンシパルのオブジェクト ID にロールを割り当てます。 |
| UserDefinedFunctionId | ユーザー定義関数 (UDF) にロールを割り当てます。 |