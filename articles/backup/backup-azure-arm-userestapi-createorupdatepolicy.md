---
title: 'Azure Backup: REST API を使用してバックアップ ポリシーを作成する'
description: REST API を使用してバックアップ ポリシー (スケジュールと保持期間) を管理します。
author: pvrk
manager: shivamg
keywords: REST API; Azure VM のバックアップ; Azure VM の復元;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: f0729a49c3dc72a28431d711e6783abda96d2ce3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466815"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>REST API を使用して Azure Recovery Services バックアップ ポリシーを作成する

Azure Recovery Services コンテナー用のバックアップ ポリシーを作成する手順の概要については、[ポリシー REST API に関するドキュメント](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)をご覧ください。 このドキュメントを Azure VM のバックアップに対するポリシーを作成するためのリファレンスとして使用しましょう。

## <a name="backup-policy-essentials"></a>バックアップ ポリシーの基礎

- バックアップ ポリシーはコンテナーごとに作成されます。
- バックアップ ポリシーは次のワークロードのバックアップ用に作成できます
  - Azure VM
  - Azure VM 内の SQL
  - Azure ファイル共有
- ポリシーは、多くのリソースに割り当てることができます。 Azure VM のバックアップ ポリシーを使用して、多くの Azure VM を保護できます。
- ポリシーは、2 つのコンポーネントで構成されています
  - スケジュール: いつバックアップを作成するか
  - 保持期間: 各バックアップをどれだけの期間保持する必要があるか。
- スケジュールでは、"毎日" または "毎週" の特定の時点として定義できます。
- 保持期間は、"毎日"、"毎週"、"毎月"、"毎年" のバックアップ ポイントに対して定義できます。
- "毎週" は週の特定の日、"毎月" は月の特定の日、"毎年" は年の特定の日に、バックアップを行うことを意味します。
- "毎月"、"毎年" のバックアップ ポイントに対する保持期間は、"長期的な保持" と呼ばれます。
- コンテナーを作成すると、"既定のポリシー" と呼ばれる Azure VM バックアップに対するポリシーも作成され、Azure VM のバックアップに使用できます。

Azure Backup ポリシーを作成または更新するには、次の *PUT* 操作を使用します。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

`{policyName}` と `{vaultName}` は URI で指定します。 その他の情報は要求本文で指定します。

## <a name="create-the-request-body"></a>要求本文を作成する

たとえば、Azure VM のバックアップに対するポリシーを作成する場合、要求本文のコンポーネントは次のとおりです。

|EnableAdfsAuthentication  |必須  |Type  |説明  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource のプロパティ        |
|tags     |         | Object        |  リソース タグ       |

要求本文での定義の完全な一覧については、[バックアップ ポリシー REST API に関するドキュメント](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)をご覧ください。

### <a name="example-request-body"></a>要求本文の例

次の要求本文では、Azure VM のバックアップに対するバックアップ ポリシーが定義されています。

次のようなポリシーです。

- 毎週のバックアップを、月曜日、水曜日、木曜日の午前 10 時 00 分 (太平洋標準時) に作成します。
- 毎週の月曜日、水曜日、木曜日に作成したバックアップを、1 週間保持します。
- 毎月の第 1 水曜日と第 3 木曜日に作成したバックアップを、2 か月間保持します (前の保持条件が存在する場合は上書きします)。
- 2 月と 11 月の第 4 月曜日と第 4 木曜日に作成したバックアップを、4 年間保持します (前の保持条件が存在する場合は上書きします)。

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> スケジュールと保持期間の日時形式では、DateTime のみがサポートされます。 Time 形式のみはサポートされません。

## <a name="responses"></a>Responses

バックアップ ポリシーの作成/更新は、[非同期操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|EnableAdfsAuthentication  |Type  |説明  |
|---------|---------|---------|
|200 OK     |    [ProtectionPolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Accepted     |         |     承認済み    |

### <a name="example-responses"></a>応答の例

ポリシーの作成または更新に対する *PUT* 要求を送信した後、最初の応答は場所ヘッダーまたは Azure-async-header を含む 202 (Accepted) です。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

その後は、場所ヘッダーまたは Azure-AsyncOperation ヘッダーを使用して簡単な *GET* コマンドで結果の操作を追跡します。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

操作が完了すると、応答本文にポリシーの内容が含まれる 200 (OK) が返されます。

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

ポリシーを使用して項目が既に保護されている場合、ポリシーでの更新はすべて、そのようなすべての関連する項目に対する[保護の変更](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection)になります。

## <a name="next-steps"></a>次の手順

[保護されていない Azure VM の保護を有効にします](backup-azure-arm-userestapi-backupazurevms.md)。

Azure Backup REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Recovery Services プロバイダー REST API](/rest/api/recoveryservices/)
- [Azure Rest API の開始](/rest/api/azure/)