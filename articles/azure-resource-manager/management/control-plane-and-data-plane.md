---
title: コントロール プレーンとデータ プレーンの操作
description: コントロール プレーンとデータ プレーンの操作の違いについて説明します。 コントロール プレーンの操作は、Azure Resource Manager によって処理されます。 データ プレーンの操作は、サービスによって処理されます。
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371271"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure コントロール プレーンとデータ プレーン

Azure の操作は、コントロール プレーンとデータ プレーンの 2 つのカテゴリに分けることができます。 この記事では、これら 2 種類の操作の違いについて説明します。

コントロール プレーンは、サブスクリプション内のリソースを管理するために使用します。 データ プレーンは、リソースの種類のインスタンスによって公開される機能を使用するために使用します。

次に例を示します。

* コントロール プレーンを使用して仮想マシンを作成します。 仮想マシンを作成した後は、リモート デスクトップ プロトコル (RDP) などのデータ プレーン操作を通じて、仮想マシンと対話します。

* コントロール プレーンを使用してストレージ アカウントを作成します。 ストレージ アカウント内のデータの読み取りと書き込みを行うには、データ プレーンを使用します。

* コントロール プレーンを使用して Cosmos データベースを作成します。 Cosmos データベースのデータに対してクエリを実行するには、データ プレーンを使用します。

## <a name="control-plane"></a>コントロール プレーン

コントロール プレーン操作に対するすべての要求は、Azure Resource Manager の URL に送信されます。 この URL は、Azure 環境によって異なります。

* Azure グローバルの場合、URL は `https://management.azure.com` です。
* Azure Government の場合、URL は `https://management.usgovcloudapi.net/` です。
* Azure Germany の場合、URL は `https://management.microsoftazure.de/` です。
* Microsoft Azure China 21Vianet の場合、URL は `https://management.chinacloudapi.cn` です。

Azure Resource Manager URL を使用する操作を確認するには、[Azure REST API](/rest/api/azure/) を参照してください。 たとえば、MySql の[作成または更新操作](/rest/api/mysql/databases/createorupdate)は、要求 URL が次のようになっているため、コントロール プレーン操作です。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

すべてのコントロール プレーン要求は Azure Resource Manager によって処理されます。 リソースを管理するために実装した次のような Azure 機能が自動的に適用されます。

* [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [管理ロック](lock-resources.md)
* [アクティビティ ログ](view-activity-logs.md)

要求が認証されると、Azure Resource Manager によって要求がリソース プロバイダーに送信され、そこで操作が完了します。

コントロール プレーンには、要求を処理するために、"グリーン フィールド" と "ブラウン フィールド" という 2 つのシナリオが含まれています。 グリーン フィールドは新しいリソースを指します。 ブラウン フィールドは既存のリソースを指します。 リソースをデプロイするときに、Azure Resource Manager によって新しいリソースを作成するタイミングと、既存のリソースをいつ更新するかが把握されます。 同じリソースが作成されることを心配する必要はありません。

## <a name="data-plane"></a>データ プレーン

データ プレーン操作の要求は、インスタンスに固有のエンドポイントに送信されます。 たとえば、Cognitive Services の[言語の検出操作](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language)は、要求 URL が次のようになっているため、データ プレーン操作です。

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

データ プレーン操作は REST API に限定されません。 仮想マシンやデータベース サーバーへのログインなど、追加の資格情報が必要になる場合があります。

管理とガバナンスを適用する機能は、データ プレーンの操作には適用されない可能性があります。 ユーザーがソリューションとやりとりするさまざまな方法を検討する必要があります。 たとえば、ユーザーがデータベースを削除できないようにするロックは、ユーザーがクエリを使用してデータを削除するのを防ぐことはできません。

一部のポリシーを使用して、データ プレーンの操作を制御できます。 詳細については、Azure Policy の「[リソース プロバイダーのモード (プレビュー)](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)」を参照してください。

## <a name="next-steps"></a>次の手順

* Azure Resource Manager の概要については、「[Azure Resource Manager とは](overview.md)」を参照してください。

* ポリシー定義が新しいリソースと既存のリソースに与える影響の詳細については、[「新しい Azure Policy 定義の影響を評価する](../../governance/policy/concepts/evaluate-impact.md)」を参照してください。
