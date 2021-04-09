---
title: MS Graph API を使用してクラウドの同期をプログラムで構成する方法
description: このトピックでは、Graph API のみを使用して受信同期を有効にする方法について説明します
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593162"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>MS Graph API を使用してクラウドの同期をプログラムで構成する方法

次のドキュメントでは、MSGraph API のみを使用して 0 から同期プロファイルをレプリケートする方法について説明します。  
これを行う方法の構造は、次の手順で構成されています。  これらは次のとおりです。

- [基本的なセットアップ](#basic-setup)
- [サービス プリンシパルを作成する](#create-service-principals)
- [同期ジョブを作成する](#create-sync-job)
- [ターゲット ドメインを更新する](#update-targeted-domain)
- [パスワード ハッシュの同期を有効にする](#enable-sync-password-hashes-on-configuration-blade)
- [不注意による削除](#accidental-deletes)
- [同期ジョブを開始する](#start-sync-job)
- [状態をレビューする](#review-status)

これらの [Windows PowerShell 用 Microsoft Azure Active Directory モジュール](/powershell/module/msonline/)のコマンドを使用して、運用テナントの同期を有効にします。これは、そのテナントの管理 Web サービスを呼び出すための前提条件となっています。

## <a name="basic-setup"></a>基本的なセットアップ

### <a name="enable-tenant-flags"></a>テナント フラグを有効にする

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
これら 2 つのコマンドのうちの 1 つには、Azure Active Directory 資格情報が必要です。 これらのコマンドレットを使用すると、テナントを暗黙的に識別し、同期のために有効にすることができます。

## <a name="create-service-principals"></a>サービス プリンシパルの作成
次に、[AD2AAD アプリケーションまたはサービス プリンシパル](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)を作成する必要があります

このアプリケーション ID 1a4721b3-e57f-4451-ae87-ef078703ec94 を使用する必要があります。 displayName は、ポータルで使用されている場合は AD ドメインの URL (例えば contoso.com) ですが、他の名前を付けても構いません。

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>同期ジョブを作成する
上記のコマンドの出力では、作成されたサービス プリンシパルの objectId が返されます。 この例では、objectId は 614ac0e9-a59b-481f-bd8f-79a73d167e1c です。  このサービス プリンシパルに同期ジョブを追加するには、Microsoft Graph を使用します。  

同期ジョブの作成に関するドキュメントについては、[こちら](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)を参照してください。

上記の ID を記録していない場合は、次の MS Graph 呼び出しを実行してサービス プリンシパルを見つけることができます。 この呼び出しを行うには、Directory.Read.All 権限が必要です。
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

次に、出力からアプリ名を探します。

次の 2 つのコマンドを実行して、2 つのジョブを作成します。1 つはユーザーまたはグループのプロビジョニング用、もう 1 つはパスワード ハッシュ同期用です。 これは 2 回繰り返されている同じ要求ですが、テンプレート ID が異なります。


次の 2 つの要求を呼び出します。

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

両方を作成する場合は、2 回呼び出す必要があります。

戻り値の例 (プロビジョニングの場合):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>ターゲット ドメインを更新する
このテナントでは、サービス プリンシパルのオブジェクト識別子とアプリケーション識別子は次のようになります。

ObjectId:8895955e-2e6c-4d79-8943-4d72ca36878f AppId:00000014-0000-0000-c000-000000000000 DisplayName: testApp

この構成の対象となるドメインを更新する必要があります。そのため、このドメインのシークレットを更新してください。

使用するドメイン名が、オンプレミスのドメイン コントローラーに設定した URL と同じであることを確認します

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 次のキーと値のペアを、実行しようとしている内容に基づいて、以下の値配列に追加します。
 - PHS と同期テナント フラグの両方を有効にする { key:"AppKey", value: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - 同期テナント フラグのみを有効にする (PHS を有効にしない) { key:"AppKey", value: "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

予期される応答は次の通りです: HTTP 204/No content

ここで、強調表示されている "Domain" 値は、エントリが Azure Active Directory にプロビジョニングされるオンプレミスの Active Directory ドメインの名前です。

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>構成ブレードでパスワード ハッシュの同期を有効にする

 このセクションでは、特定の構成のパスワード ハッシュの同期を有効にする方法について説明します。 これは、テナントレベルの機能フラグを有効にする AppKey シークレットとは異なります。これは、1 つのドメインまたは構成に対してのみ実行されます。これがエンド ツー エンドで動作するようにするには、アプリケーション キーを PHS に設定する必要があります。

1. スキーマを取得する (非常に大容量) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. この CredentialData 属性のマッピングを取得する:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. スキーマで次の名前を持つ次のオブジェクトマッピングを検索する
 - Active Directory Domain Services Users のプロビジョニング
 - Active Directory Domain Services inetOrgPersons のプロビジョニング

 オブジェクト マッピングは、schema.synchronizationRules[0].objectMappings 内にあります (現時点では、1 つの同期規則のみがあると想定)

4. 手順 (2) から CredentialData マッピングを取得し、手順 (3) でオブジェクト マッピングに挿入します

 オブジェクト マッピングは次のようになります。
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 上記の **AD2AADProvisioning ジョブおよび AD2AADPasswordHash ジョブの作成** 手順からのマッピングをコピーして attributeMappings 配列に貼り付けます。 

 この配列内の要素の順序は重要ではありません (バックエンドによって並べ替えられます)。 この属性マッピングの追加については、既に名前が配列に存在する場合 (たとえば、targetAttributeName CredentialData を含む attributeMappings の項目が既に存在する場合)、競合エラーが発生するか、または既存のマッピングと新しいマッピングが一緒に結合される可能性があります (通常は望ましい結果ではありません)。 バックエンドでの重複除去は行われません。 

 この操作は、Users と inetOrgpersons の両方に対して実行してください

5. 作成したスキーマを保存します 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 要求本文にスキーマを追加します。 

## <a name="accidental-deletes"></a>不注意による削除
このセクションでは、プログラムによる有効化または無効化、およびプログラムによる[誤削除](how-to-accidental-deletes.md)の使用方法について説明します。


### <a name="enabling-and-setting-the-threshold"></a>しきい値の有効化と設定
ジョブの設定ごとに使用できるものには次の 2 つがあります。

 - DeleteThresholdEnabled - 'true' に設定されている場合は、ジョブの誤削除防止を有効にします。 既定では 'true' に設定されます。
 - DeleteThresholdValue - 誤削除防止が有効になっている場合は、ジョブの各実行で許可される削除の最大数を定義します。 既定では、値は 500 に設定されています。  したがって、値が 500 に設定されている場合、各実行で許可される削除の最大数は 499 になります。

削除のしきい値の設定は `SyncNotificationSettings` の一部であり、グラフを使用して変更できます。 

この構成の対象となる SyncNotificationSettings を更新する必要があるため、シークレットを更新します。

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 次のキーと値のペアを、実行しようとしている内容に基づいて、以下の値配列に追加します。

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

上記の例にある "Enabled" の設定は、ジョブが検疫されたときに通知メールを有効または無効にするためのものです。


現在、シークレットの PATCH 要求はサポートされていないため、他の値を保持するためには、(上記の例のように) PUT 要求の本文ですべての値を追加する必要があります。

すべてのシークレットの既存の値は、以下を使用して取得できます 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>削除を許可する
ジョブが検疫された後に削除が通過できるようにするには、スコープとして "ForceDeletes" のみを使用して再起動を発行する必要があります。 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>同期ジョブを開始する
ジョブは、次のコマンドを使用して再度取得できます。

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

ジョブを取得するためのドキュメントについては、[こちら](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta)を参照してください。 
 
ジョブを開始するには、最初の手順で作成したサービス プリンシパルの objectId と、ジョブを作成した要求から返されたジョブ識別子を使用して、この要求を発行します。

ジョブを開始する方法のドキュメントについては、[こちら](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)を参照してください。 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

予期される応答は次の通りです: HTTP 204/No content。

ジョブを制御するその他のコマンドについては、[こちら](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)に記載されています。
 
ジョブを再起動するには、以下を使用します。

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>状態をレビューする
次を使用してジョブの状態を取得します。

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

関連する詳細については、返されたオブジェクトの 'status' セクションを参照してください。

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)
- [変換](how-to-transformation.md)
- [Azure AD の同期 API](/graph/api/resources/synchronization-overview?view=graph-rest-beta)