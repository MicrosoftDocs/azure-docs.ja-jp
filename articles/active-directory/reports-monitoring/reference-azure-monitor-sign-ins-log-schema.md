---
title: Azure Monitor (プレビュー) の Azure Active Directory サインイン ログ スキーマ | Microsoft Docs
description: Azure Monitor (プレビュー) で使用する Azure AD サインイン ログ スキーマについて説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ce1de5b5b9ff46efcc9e7faed9aa147b53211a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439058"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor-preview"></a>Azure Monitor (プレビュー) での Azure AD サインイン ログ スキーマを解釈する

この記事では、Azure Monitor での Azure Active Directory (Azure AD) サインイン ログ スキーマについて説明します。 サインインに関連する情報のほとんどは、`records` オブジェクトの *Properties* 属性で提供されます。

```json
{ 
    "records": [ 
    { 
        "time": "2018-05-16T16:09:58.4634578Z", 
        "resourceId": null, 
        "operationName": "Sign-in activity", 
        "operationVersion": "1.0", 
        "category": "SignIn", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "50140", 
        "resultSignature": "None", 
        "resultDescription": "Other", 
        "durationMs": 0, 
        "callerIpAddress": "167.220.0.158", 
        "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
        "identity": "arvind harinder", 
        "Level": 4, 
        "location": "US", 
        "properties": { 
            "id": "0782c515-08b6-4029-a65c-29d9a3d20800", 
            "createdDateTime": "2018-05-16T16:09:58.4634578+00:00", 
            "userDisplayName": "Arvind Harinder", 
            "userPrincipalName": "ah@wingtiptoysonline.onmicrosoft.com", 
            "userId": "5b9f356d-9592-42fd-9ec4-d70963909534", 
            "appId": "c44b4083-3bb0-49c1-b47d-974e53cbdf3c", 
            "appDisplayName": "Azure Portal", 
            "ipAddress": "167.220.0.158", 
            "status": { 
                "errorCode": 50140, 
                "failureReason": "Other" 
            }, 
            "clientAppUsed": "Browser", 
            "deviceDetail": { 
                "operatingSystem": "Windows 10", 
                "browser": "Chrome 66.0.3359" 
            }, 
            "location": { 
                "city": "Sammamish", 
                "state": "Washington", 
                "countryOrRegion": "US", 
                "geoCoordinates": { 
                    "latitude": 47.66630935668945, 
                    "longitude": -122.09821319580078 
                } 
            }, 
            "correlationId": "13e19598-e040-487f-bd32-d38a2cd75d9a", 
            "conditionalAccessStatus": 2, 
            "conditionalAccessPolicies": [ 
            { 
                "id": "de7e60eb-ed89-4d73-8205-2227def6b7c9", 
                "displayName": "[billg] SharePoint limited access policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "7412a2d8-cbb1-4f1c-96cf-8410b4b8b37b", 
                "displayName": "[BillG] AIP MFA Policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "727ed8ea-059d-4d8f-aba5-c1dc500e8b06", 
                "displayName": "[billg] mfa for mail", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "6701123a-b4c6-48af-8565-565c8bf7cabc", 
                "displayName": "Medium signin risk block", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "fbafa2da-cf7f-4ec3-83cf-281188e53f76", 
                "displayName": "Require MFA for admins [Ignite talk] ", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "15339054-709d-4e06-a9ec-342bf043ea56", 
                "displayName": "Enhanced proofing for Azure portal [Ignite talk]", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "2ff9436f-bc72-4ce6-b17e-e7e51153146e", 
                "displayName": "[calebb] AIP policy", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "46ab586b-9447-4847-a889-e60705d96e56", 
                "displayName": "Test policy, OR", 
                "enforcedGrantControls": [], 
                "enforcedSessionControls": [], 
                "result": 3 
            }, 
            { 
                "id": "ceb6e17e-a5d0-4b3a-a150-6c2be2d5b0e9", 
                "displayName": "mm policy with Duo", 
                "enforcedGrantControls": [ 
                    "Require Duo Mfa" 
                ], 
            "enforcedSessionControls": [], 
            "result": 2 
            }, 
            ], 
            "isRisky": false 
            } 
        } 
    } 
```

## <a name="field-descriptions"></a>フィールドの説明

| フィールド名 | 説明 |
|------------|-------------|
| Time | 日付と時刻 (UTC)。 |
| ResourceId | この値はマップされていないため、このフィールドを無視しても問題ありません。  |
| OperationName | サインインの場合、この値は常に *Sign-in activity* です。 |
| OperationVersion | クライアントによって要求された REST API のバージョン。 |
| Category | サインインの場合、この値は常に *SignIn* です。 | 
| TenantId | ログに関連付けられたテナント GUID。 |
| ResultType | サインイン操作の結果は、*Success* または *Failure* です。 | 
| ResultSignature | サインイン操作にエラーがある場合は、そのエラー コードが含まれます。 |
| ResultDescription | サインイン操作のエラーの説明を指定します。 |
| DurationMs |  この値はマップされていないため、このフィールドを無視しても問題ありません。|
| CallerIpAddress | 要求を行ったクライアントの IP アドレス。 | 
| CorrelationId | クライアントから渡される省略可能な GUID。 この値は、クライアント側の操作をサーバー側の操作と関連付けるために役立ちます。また、複数のサービスにまたがるログをトレースする場合に便利です。 |
| ID | 要求を行ったときに提示されたトークンからの ID。 ユーザー アカウント、システム アカウント、またはサービス プリンシパルです。 |
| Level | メッセージの種類を指定します。 監査の場合は常に *Informational* です。 |
| Location | サインイン アクティビティの場所を指定します。 |
| Properties | サインインに関連付けられたすべてのプロパティを一覧表示します。詳しくは、[Microsoft Graph API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)をご覧ください。 読みやすくするため、このスキーマではサインイン リソースと同じ属性名を使用しています。

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure 診断ログの詳細を確認する](../../azure-monitor/platform/diagnostic-logs-overview.md)
