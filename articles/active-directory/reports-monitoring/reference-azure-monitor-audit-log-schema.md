---
title: Azure Monitor の Azure Active Directory 監査ログ スキーマを解釈する (プレビュー) | Microsoft Docs
description: Azure Monitor (プレビュー) で使用する Azure AD 監査ログ スキーマについて説明します
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
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4832776dee7c478996bcfc2a903a6d8e2a3c4426
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436967"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Azure Monitor の Azure AD 監査ログ スキーマを解釈する (プレビュー)

この記事では、Azure Monitor での Azure Active Directory (Azure AD) 監査ログ スキーマについて説明します。 次の 2 つの例のように、個々のログ エントリはテキストとして保存され、形式は JSON BLOB となります。 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>フィールドとプロパティの説明

| フィールド名 | 説明 |
|------------|-------------|
| time       | 日付と時刻 (UTC)。 |
| operationName | 操作の名前。 |
| operationVersion | クライアントによって要求された REST API のバージョン。 |
| category | 現在、サポートされている値は *Audit* のみです。 |
| tenantId | ログに関連付けられたテナント GUID。 |
| resultType | 操作の結果。 結果は *Success* または *Failure* になります。 |
| resultSignature |  このフィールドはマップされていないため、無視しても問題ありません。 | 
| resultDescription | 結果に関する追加の説明です (使用可能な場合)。 | 
| durationMs |  このフィールドはマップされていないため、無視しても問題ありません。 |
| callerIpAddress | 要求を行ったクライアントの IP アドレス。 | 
| correlationId | クライアントから渡される省略可能な GUID。 クライアント側の操作をサーバー側の操作と関連付けるために役立ちます。また、複数のサービスにまたがるログをトレースする場合に便利です。 |
| identity | 要求を行ったときに提示されたトークンからの ID。 この ID は、ユーザー アカウント、システム アカウント、またはサービス プリンシパルになります。 |
| level | メッセージの種類。 監査ログの場合、レベルは常に *Informational* です。 |
| location | データセンターの場所。 |
| properties | サポートされているプロパティのうち、監査ログに関連するプロパティを一覧表示します。 詳細については、次の表を参照してください。 | 

<br>

| プロパティ名 | 説明 |
|---------------|-------------|
| AuditEventCategory | 監査イベントの種類。 *User Management* や *Application Management* などを指定できます。|
| Identity Type | *Application* か *User* になります。 |
| 操作の種類 | *Add*、*Update*、*Delete*、 または *Other* になります。 |
| Target Resource Type | 操作が実行されたターゲット リソースの種類を指定します。 *Application*、*User*、*Role*、*Policy* を指定できます。 | 
| Target Resource Name | ターゲット リソースの名前。 アプリケーション名、ロール名、ユーザー プリンシパル名、またはサービス プリンシパル名になります。 |
| additionalTargets | 特定の操作の追加のプロパティをすべて一覧表示します。 たとえば、更新操作の場合、以前の値と新しい値が *targetUpdatedProperties* 以下に一覧表示されます。 | 

## <a name="next-steps"></a>次の手順

* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
