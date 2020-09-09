---
title: Microsoft Graph API を使用してプロビジョニングを構成する - Azure Active Directory | Microsoft Docs
description: アプリケーションの複数のインスタンスを対象にプロビジョニングを設定する必要がありますか? Microsoft Graph API を使用して自動プロビジョニングの構成を自動化し、時間を節約する方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 17660df34c8039ae96440c417aef051d51a5c91c
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234755"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Microsoft Graph API を使用してプロビジョニングを構成する

Azure portal は、個々のアプリのプロビジョニングを一度に 1 つずつ構成する便利な方法です。 しかし、アプリケーションのインスタンスを複数作成する場合、または作成するインスタンスの数が数百にも及ぶ場合は、Microsoft Graph API を使用してアプリの作成と構成を自動化すると、より簡単です。 この記事では、API を使用してプロビジョニングの構成を自動化する方法について説明します。 この方法は、[アマゾン ウェブ サービス](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)などのアプリケーションで一般的に使用されています。

**Microsoft Graph API を使用してプロビジョニングの構成を自動化する手順の概要**


|手順  |詳細  |
|---------|---------|
|[手順 1.ギャラリー アプリケーションを作成する](#step-1-create-the-gallery-application)     |API クライアントにサインインする <br> ギャラリー アプリケーション テンプレートを取得する <br> ギャラリー アプリケーションを作成する         |
|[手順 2.テンプレートに基づいてプロビジョニング ジョブを作成する](#step-2-create-the-provisioning-job-based-on-the-template)     |プロビジョニング コネクタのテンプレートを取得する <br> プロビジョニング ジョブを作成する         |
|[手順 3.アクセスを承認する](#step-3-authorize-access)     |アプリケーションへの接続をテストする <br> 資格情報を保存する         |
|[手順 4. プロビジョニング ジョブを開始する](#step-4-start-the-provisioning-job)     |ジョブの開始         |
|[手順 5. プロビジョニングを監視する](#step-5-monitor-provisioning)     |プロビジョニング ジョブの状態を確認する <br> プロビジョニング ログを取得する         |

> [!NOTE]
> この記事に示されている応答オブジェクトは、読みやすくするために短縮されている可能性があります。 実際の呼び出しからは、すべてのプロパティが返されます。

## <a name="step-1-create-the-gallery-application"></a>手順 1:ギャラリー アプリケーションを作成する

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph エクスプローラー (推奨)、Postman、または使用しているその他の API クライアントにサインインする

1. [Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を開始します。
1. [Sign-In with Microsoft]\(Microsoft アカウントでサインイン\) ボタンを選択し、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。

    ![Graph のサインイン](./media/application-provisioning-configure-api/wd_export_02.png)

1. サインインに成功すると、左側のペインにユーザー アカウントの詳細が表示されます。

### <a name="retrieve-the-gallery-application-template-identifier"></a>ギャラリー アプリケーション テンプレート識別子を取得する
Azure AD アプリケーション ギャラリーのアプリケーションにはそれぞれ、そのアプリケーションのメタデータを記述する[アプリケーション テンプレート](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta)があります。 このテンプレートを使用して、管理用のテナントにアプリケーションとサービス プリンシパルのインスタンスを作成できます。

#### <a name="request"></a>*Request*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Response*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>ギャラリー アプリケーションを作成する

前のステップで取得したアプリケーションのテンプレート ID を使用して、ご自身のテナント内にアプリケーションとサービス プリンシパルの[インスタンスを作成](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta)します。

#### <a name="request"></a>*Request*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Response*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>手順 2:テンプレートに基づいてプロビジョニング ジョブを作成する

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>プロビジョニング コネクタのテンプレートを取得する

ギャラリー内の、プロビジョニングが有効になっているアプリケーションには、構成を効率化するためのテンプレートがあります。 次の要求を使用して、[プロビジョニング構成のテンプレートを取得](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta)します。 ID を指定する必要があることに注意してください。 ID は、前のリソース (この場合は ServicePrincipal) を示します。 

#### <a name="request"></a>*Request*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>プロビジョニング ジョブを作成する
プロビジョニングを有効にするには、まず[ジョブを作成する](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)必要があります。 プロビジョニング ジョブを作成するには、以下の要求を使用します。 ジョブに使用するテンプレートを指定するときは、前の手順の templateId を使用します。

#### <a name="request"></a>*Request*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>手順 3:アクセスを承認する

### <a name="test-the-connection-to-the-application"></a>アプリケーションへの接続をテストする

サードパーティ アプリケーションとの接続をテストします。 次の例は、clientSecret と secretToken を必要とするアプリケーションの場合です。 アプリケーションにはそれぞれ独自の要件があります。 アプリケーションでは、多くの場合、ClientSecret の代わりに BaseAddress を使用します。 アプリで必要な資格情報を確認するには、アプリケーションのプロビジョニング構成ページに移動し、開発者モードでテスト接続をクリックします。 ネットワーク トラフィックに、資格情報に使用されたパラメーターが表示されます。 すべての資格情報の一覧は、[こちら](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta)で確認できます。 

#### <a name="request"></a>*Request*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>資格情報を保存する

プロビジョニングを構成するには、Azure AD とアプリケーションの間に信頼を確立する必要があります。 サードパーティ アプリケーションへのアクセスを承認します。 次の例は、clientSecret と secretToken を必要とするアプリケーションの場合です。 アプリケーションにはそれぞれ独自の要件があります。 [API ドキュメント](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta)を参照して、使用可能なオプションを確認してください。 

#### <a name="request"></a>*Request*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>手順 4:プロビジョニング ジョブを開始する
プロビジョニング ジョブが構成されたので、次のコマンドを使用して[ジョブを開始](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)します。 


#### <a name="request"></a>*Request*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>手順 5:プロビジョニングを監視する

### <a name="monitor-the-provisioning-job-status"></a>プロビジョニング ジョブの状態を監視する

プロビジョニング ジョブが実行中になっているので、次のコマンドを使用して、現在のプロビジョニング サイクルの進行状況と、ターゲット システムで作成されたユーザーとグループの数などの現在までの統計情報を追跡します。 

#### <a name="request"></a>*Request*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>プロビジョニング ログを使用してプロビジョニング イベントを監視する
プロビジョニング ジョブの状態を監視するだけでなく、[プロビジョニング ログ](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta)を使用して、発生しているすべてのイベントのクエリを実行できます (例: 特定のユーザーを照会して、それらのユーザーが正常にプロビジョニングされたかどうかを判断する)。

#### <a name="request"></a>*Request*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Response*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>関連記事

- [同期に関する Microsoft Graph のドキュメントを確認する](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [カスタム SCIM アプリと Azure AD の統合](use-scim-to-provision-users-and-groups.md)