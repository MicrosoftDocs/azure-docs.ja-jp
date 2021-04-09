---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750230"
---
| プロパティ | 説明 |
|:--- |:---|
|**identity / type** | 要求の作成に使用された認証の種類。 例: `OAuth`、`Kerberos`、`SAS Key`、`Account Key`、`Anonymous` |
|**identity / tokenHash**|このフィールドは、内部使用専用として予約されています。 |
|**authorization / action** | 要求に割り当てられているアクション。 |
|**authorization / roleAssignmentId** | ロールの割り当て ID (例: `4e2521b7-13be-4363-aeda-111111111111`)。|
|**authorization / roleDefinitionId** | ロール定義 ID (例: `ba92f5b4-2d11-453d-a403-111111111111"`)。|
|**principals / id** | セキュリティ プリンシパルの ID (例: `a4711f3a-254f-4cfb-8a2d-111111111111`)。|
|**principals / type** | セキュリティ プリンシパルの種類 (例: `ServicePrincipal`)。 |
|**requester / appID** | 要求元として使用される Open Authorization (OAuth) アプリケーション ID <br> (例: `d3f7d5fe-e64a-4e4e-871d-333333333333`)。|
|**requester / audience** | 要求の OAuth 対象ユーザー (例: `https://storage.azure.com`)。 |
|**requester / objectId** | 要求元の OAuth オブジェクト ID。 Kerberos 認証の場合は、Kerberos で認証されたユーザーのオブジェクト識別子を表します (例: `0e0bf547-55e5-465c-91b7-2873712b249c`)。 |
|**requester / tenantId** | 識別子の OAuth テナント ID (例: `72f988bf-86f1-41af-91ab-222222222222`)。|
|**requester / tokenIssuer** | OAuth トークン発行者 (例: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`)。|
|**requester / upn** | 要求元のユーザー プリンシパル名 (UPN) (例: `someone@contoso.com`)。 |
|**requester / userName** | このフィールドは、内部使用専用として予約されています。|
