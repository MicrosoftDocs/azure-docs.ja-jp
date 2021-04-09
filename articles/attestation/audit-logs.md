---
title: Azure Attestation 監査ログ
description: Azure Attestation 用に収集される完全な監査ログについて説明します。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95758688"
---
# <a name="audit-logs-for-azure-attestation"></a>Azure Attestation 用の監査ログ

監査ログとは、時間の経過と共に発生した個別のイベントの、セキュリティで保護された、変更できないタイムスタンプ付きのレコードです。 これらのログによって、ご利用の構成証明インスタンスの機能に影響する可能性がある重要なイベントが取り込まれます。

Azure Attestation を使用すると、構成証明インスタンスと、これに関連付けられているポリシーを管理することができます。 インスタンス管理およびポリシーの変更に関連するアクションが監査およびログされます。

この記事には、ログされるイベント、収集される情報、およびこれらのログの場所に関する情報が含まれています。

## <a name="about-audit-logs"></a>監査ログについて

Azure Attestation では、構成証明の実行方法に影響を与えるイベントの監査ログを、コードを使用して生成します。 これは、通常、構成証明インスタンスおよび一部の管理操作に対するポリシー変更がどのように、いつ行われたかを要約したものとなります。

### <a name="auditable-events"></a>監査可能なイベント
収集する監査ログの一部を次に示します。

|     イベント/API                              |     イベントの説明                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Create Instance]\(基本モード: インスタンスの作成\)                        |     構成証明サービスの新しいインスタンスを作成します。 |
|     インスタンスの破棄                       |     構成証明サービスのインスタンスを破棄します。 |
|     ポリシー証明書の追加                 |     現在のポリシー管理証明書のセットに証明書を追加します。 |
|     ポリシー証明書の削除              |     現在のポリシー管理証明書のセットから証明書を削除します。 |
|     現在のポリシーの設定                     |     指定された TEE の種類に対する構成証明ポリシーを設定します。 |
|     構成証明ポリシーのリセット               |     指定された TEE の種類に対する構成証明ポリシーをリセットします。 |
|     ポリシーを更新するための準備               |     指定された TEE の種類について、構成証明ポリシーを更新するための準備をします。 |
|     障害発生後のテナントのリハイドレート       |     構成証明サービスのこのインスタンスのすべての構成証明テナントを再シールします。 これを実行できるのは、構成証明サービス管理者に限られています。 |

### <a name="collected--information"></a>収集される情報
このようなイベントがそれぞれ発生した場合、Azure Attestation によって以下の情報が収集されます。

- 操作の名前
- 操作の成功
- 操作の呼び出し元。次のいずれかになると考えられます。
    - Azure AD UPN
    - Object ID
    - Certificate
    - Azure AD Tenant ID (Azure AD テナント ID)
- 操作のターゲット。次のいずれかになると考えられます。
    - 環境
    - サービス リージョン
    - サービス ロール
    - サービス ロール インスタンス
    - Resource ID
    - リソース リージョン

### <a name="sample-audit-log"></a>サンプルの監査ログ

監査ログは JSON 形式で提供されます。 監査ログがどのように表示されるかを以下に示します。

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>監査ログへのアクセス

これらのログは Azure に格納されており、直接アクセスすることはできません。 これらのログにアクセスする必要がある場合は、サポート チケットを提出してください。 詳細については、「 [Microsoft サポートに問い合わせる](https://azure.microsoft.com/support/options/)」を参照してください。 

サポート チケットが提出されると、Microsoft でこれらのログをダウンロードしてアクセスできるようにします。
