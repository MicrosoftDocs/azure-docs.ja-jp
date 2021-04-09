---
title: Azure API Management のバージョン | Microsoft Docs
description: Azure API Management のバージョンの概念について説明します。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040381"
---
# <a name="versions-in-azure-api-management"></a>Azure API Management のバージョン

バージョンを使用すると、関連する API のグループを開発者に示すことができます。 バージョンを使用すると API の重大な変更を安全に処理できます。 クライアントは、準備ができたら新しい API バージョンを使用することを選択できます。その一方で、既存のクライアントは以前のバージョンを引き続き使用します。 バージョンは (ユーザーが選択する任意の文字列値である) バージョン識別子で区別されるため、バージョン管理スキームにより、クライアントは使用する API のバージョンを識別できます。

ほとんどの場合、各 API バージョンは独自の独立した API と見なすことができます。 2 つの異なる API バージョンが、異なる一連の操作と異なるポリシーを持っていることがあります。

バージョンを使用すると、次のことが可能になります。

- API の複数のバージョンを同時に公開する。
- パス、クエリ文字列、またはヘッダーを使用してバージョンを区別する。
- 任意の文字列値を使用してバージョンを識別する。これには、番号、日付、または名前を使用できます。
- 開発者ポータルにグループ化された API バージョンを表示する。
- 既存の (バージョン管理されていない) API を取得し、既存のクライアントを中断することなく、その新しいバージョンを作成する。

[このチュートリアルに従って、バージョンの使用を開始してください。](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>バージョン管理スキーム

バージョン管理の要件は API 開発者ごとに異なります。 Azure API Management では、バージョン管理への 1 つのアプローチが規定されているわけではなく、代わりに複数のオプションが提供されます。

### <a name="path-based-versioning"></a>パス ベースのバージョン管理

パス バージョン管理スキームが使用されている場合は、すべての API 要求の URL パスにバージョン識別子を含める必要があります。

たとえば、`https://apis.contoso.com/products/v1` と `https://apis.contoso.com/products/v2` は同じ `products` API ではあっても、それぞれバージョン `v1` と `v2` を参照できます。

パスベースのバージョン管理を使用している場合の API 要求 URL の形式は `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` です。

### <a name="header-based-versioning"></a>ヘッダー ベースのバージョン管理

ヘッダー バージョン管理スキームが使用されている場合は、すべての API 要求の HTTP 要求ヘッダーにバージョン識別子を含める必要があります。 HTTP 要求ヘッダーの名前を指定できます。

たとえば、`Api-Version` という名前のカスタム ヘッダーを作成すると、クライアントはこのヘッダーの値に `v1` または `v2` を指定できます。

### <a name="query-string-based-versioning"></a>クエリ文字列ベースのバージョン管理

クエリ文字列バージョン管理スキームが使用されている場合は、すべての API 要求のクエリ文字列パラメーターにバージョン識別子を含める必要があります。 クエリ文字列パラメーターの名前を指定できます。

クエリ文字列ベースのバージョン管理を使用している場合の API 要求 URL の形式は `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` です。

たとえば、`https://apis.contoso.com/products?api-version=v1` と `https://apis.contoso.com/products/api-version=v2` は同じ `products` API ではあっても、それぞれバージョン `v1` と `v2` を参照できます。

## <a name="original-versions"></a>元のバージョン

バージョン管理されていない API にバージョンを追加すると、`Original` バージョンが自動的に作成され、バージョン識別子が指定されていない既定の URL で応答します。 `Original` バージョンにより、バージョンを追加するプロセスによって、既存のどの呼び出し元も中断されなくなります。 最初にバージョンを有効にして新しい API を作成した場合、`Original` バージョンは作成されません。

## <a name="how-versions-are-represented"></a>バージョンを表す方法

Azure API Management には、1 つの論理的な API の一連のバージョンを表す、*バージョン セット* と呼ばれるリソースが保持されています。 バージョン セットには、バージョン管理された API の表示名と、指定されたバージョンに要求を送信するために[使用されるバージョン管理スキーム](#versioning-schemes)が含まれています。

API の各バージョンは独自の API リソースとして保持された後、バージョン セットに関連付けられます。 バージョン セットには、操作またはポリシーが異なる API が含まれている場合があります。 セット内のバージョン間で大幅な変更を行う場合があります。

Azure portal によって、バージョン セットが作成されます。 Azure portal でバージョン セットの名前と説明を変更できます。

[Azure CLI](/cli/azure/apim/api/versionset)、[Azure PowerShell](/powershell/module/az.apimanagement/#api-management)、[Resource Manager テンプレート](/azure/templates/microsoft.apimanagement/service/apiversionsets)、または [Azure Resource Manager API](/rest/api/apimanagement/2020-06-01-preview/apiversionset) を使用して、バージョン セットを直接表示および管理できます。

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>バージョン管理されていない API からバージョン管理された API への移行

Azure portal を使用して既存の API でバージョン管理を有効にすると、API Management リソースに次の変更が加えられます。

 * 新しいバージョン セットが作成されます。
 * 既存のバージョンは保持され、[`Original` API バージョンとして構成されます](#original-versions)。 この API はバージョン セットにリンクされますが、バージョン識別子を指定する必要はありません。
 * 新しいバージョンが新しい API として作成され、バージョン セットにリンクされます。 この新しい API には、バージョン管理スキームと識別子を使用してアクセスする必要があります。

## <a name="versions-and-revisions"></a>バージョンとリビジョン

バージョンとリビジョンは別個の機能です。 各バージョンは、バージョン管理されていない API と同じように、複数のリビジョンを持つことができます。 リビジョンは、バージョンを使用せずに使用することも、その他の方法で使用することもできます。 通常、バージョンは、破壊的変更のある API のバージョンを区別するために使用される一方、リビジョンは、API に軽微な変更および非破壊的変更を加えるために使用されます。

リビジョンに破壊的変更があることが判明した場合、またはリビジョンを正式にベータ バージョンまたはテスト バージョンに変更する場合は、リビジョンからバージョンを作成することができます。 Azure portal を使用して、[リビジョン] タブのリビジョン コンテキスト メニューで [Create Version from Revision]\(リビジョンからバージョンを作成\) をクリックします。

## <a name="developer-portal"></a>[開発者ポータル]

[開発者ポータル](./api-management-howto-developer-portal.md)には、API の各バージョンが個別に一覧表示されます。

![バージョン管理された API の一覧を表示している API Management 開発者ポータル](media/api-management-versions/portal-list.png)

API の詳細には、その API のすべてのバージョンの一覧も表示されます。 `Original` バージョンは、バージョン識別子なしで表示されます。

![API の詳細とその API のバージョンの一覧を表示している API Management 開発者ポータル](media/api-management-versions/portal-details.png)

> [!TIP]
> API バージョンが開発者ポータルに表示されるには、そのバージョンを製品に追加しておく必要があります。
