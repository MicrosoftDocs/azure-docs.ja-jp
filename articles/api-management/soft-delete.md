---
title: Azure API Management の論理的な削除 (プレビュー) | Microsoft Docs
description: 論理的な削除を使用すると、削除された API Management インスタンスを復旧できます。
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652387"
---
# <a name="api-management-soft-delete-preview"></a>API Management の論理的な削除 (プレビュー)

API Management の論理的な削除 (プレビュー) を使用すると、最近削除された API Management (APIM) インスタンスを復旧および復元できます。

> [!IMPORTANT]
> この記事で説明する手順を使用して論理的な削除を行い、回復できるのは、`2020-06-01-preview` 以降の API バージョンを使用して削除された API Management インスタンスのみです。 以前の API バージョンを使用して削除された APIM インスタンスは、引き続き物理的に削除されます。 Azure PowerShell と Azure CLI では現在、`2020-06-01-preview` バージョンが使用されないため、やはり物理的に削除する動作が実行されます。

## <a name="supporting-interfaces"></a>インターフェイスのサポート

論理的な削除機能は [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore) を通じて利用できます。

> [!TIP]
> Azure REST API を呼び出すためのヒントとツールについては、[Azure REST API リファレンス](/rest/api/azure/)を参照してください。

| 操作 | 説明 | API Management の名前空間 | 最小 API バージョン |
|--|--|--|--|
| [Create or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | API Management サービスを作成または更新します。  | API Management サービス | Any |
| [Create or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) (`restore` プロパティを **true** に設定) | API Management サービスが以前に論理的に削除されていた場合、削除を取り消します。 `restore` が指定されていて `true` に設定されている場合、その他のプロパティはすべて無視されます。  | API Management サービス |  2020-06-01-preview |
| [削除](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | 既存の API Management サービスを削除します。 | API Management サービス | 2020-06-01-preview|
| [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | 論理的に削除された API Management サービスを、名前を指定して取得します。 | 削除されたサービス | 2020-06-01-preview |
| [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | 指定されたサブスクリプションについて削除の取り消しが可能な、論理的に削除されたサービスを一覧表示します。 | 削除されたサービス | 2020-06-01-preview
| [消去](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | API Management サービスを消去します (削除を取り消すオプションを使用せずに削除します)。 | 削除されたサービス | 2020-06-01-preview

## <a name="soft-delete-behavior"></a>論理的な削除の動作

任意の API バージョンを使用して、API Management インスタンスを作成する (およびこれを回復するオプションを使用する) ことができますが、APIM インスタンスを論理的に削除するには `2020-06-01-preview` 以降のバージョンを使用する必要があります。

API Management インスタンスを削除すると、サービスは削除された状態になり、APIM 操作からアクセスできなくなります。 この状態の APIM インスタンスに対しては、一覧表示、回復、消去 (完全削除) だけができます。

また、Azure では、APIM インスタンスに対応する基盤となるデータの削除が、事前に定義された (48 時間の) データ保有期間の後に実行されるようにスケジュールされます。 また、インスタンスに対応する DNS レコードも、データ保有期間の間だけ保持されます。 データ保有期間が経過するまで、論理的に削除された API Management インスタンスの名前を再利用することはできません。

APIM インスタンスが 48 時間以内に回復されない場合、そのインスタンスは物理的に削除されます (回復不能)。 また、論理的な削除の保有期間を取り下げて、APIM インスタンスを[消去](#purge-a-soft-deleted-apim-instance) (完全に削除) することもできます。

## <a name="list-deleted-apim-instances"></a>削除された APIM インスタンスを一覧表示する

削除されたサービスの [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) または [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) を使用して、論理的に削除された APIM インスタンスを復元 (削除を取り消し) できることを確認できます。

### <a name="get-a-soft-deleted-instance-by-name"></a>論理的に削除されたインスタンスを名前を指定して取得する

API Management の [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) 操作を使用して、`{subscriptionId}`、`{location}`、`{serviceName}` を Azure サブスクリプション、リソースの場所、および API Management インスタンス名に置き換えます。

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

削除の取り消しが可能な場合、Azure では APIM インスタンスの `deletionDate` と `scheduledPurgeDate` を示すレコードを返します。次に例を示します。

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>特定のサブスクリプションについて論理的に削除されたすべてのインスタンスを一覧表示する

API Management の [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) 操作を使用して、`{subscriptionId}` をサブスクリプション ID に置き換えます。

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

これにより、指定されたサブスクリプションで削除の取り消しが可能な、論理的に削除されたすべてのサービスの一覧が返され、それぞれ `deletionDate` と `scheduledPurgeDate` が表示されます。

## <a name="recover-a-deleted-apim-instance"></a>削除された APIM インスタンスを復旧する

API Management の [Create Or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) 操作を使用して、`{subscriptionId}`、`{resourceGroup}`、`{apimServiceName}` を Azure サブスクリプション、リソース グループ名、および API Management 名に置き換えます。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . 要求本文で `restore` プロパティを `true` に設定します。 (このフラグが指定されていて *true* に設定されている場合、その他のプロパティはすべて無視されます。)次に例を示します。

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>論理的に削除された APIM インスタンスを消去する

API Management の [Purge](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) 操作を使用して、`{subscriptionId}`、`{location}`、`{serviceName}` を Azure サブスクリプション、リソースの場所、および API Management 名に置き換えます。

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

これにより、API Management インスタンスが Azure から完全に削除されます。

## <a name="next-steps"></a>次の手順

長期的な API Management のバックアップと回復のオプションについて説明します。

- [Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法](api-management-howto-disaster-recovery-backup-restore.md)