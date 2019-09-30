---
title: Azure Data Share プレビューのロールおよび要件
description: Azure Data Share プレビューのロールおよび要件
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7bf98f8774551292574d4f1951eba44657fa7de0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307352"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Azure Data Share プレビューのロールおよび要件

この記事では、Azure Data Share プレビューを使用してデータを共有する、または Azure Data Share プレビューを使用して承諾およびデータの受信を行う際に必要となるロールについて説明します。 

## <a name="roles-and-requirements"></a>ロールと要件

Azure Data Share では、データ プロバイダーによって共有されるデータの読み取りと、データ コンシューマーとして共有されるデータの受信を可能にするために、Azure サービス用マネージド ID (旧称 MSI) を使用して、基になるストレージ アカウントに対する認証が行われます。 結果として、データ プロバイダーとデータ コンシューマーの間で資格情報が交換されることはありません。 

マネージド サービス ID には、基になるストレージ アカウントへのアクセス権が付与されている必要があります。 Azure Data Share サービスでは、Azure Data Share リソースのマネージド サービス ID を使用して、データの読み取りと書き込みが行われます。 Azure Data Share のユーザーは、データの共有元または共有先であるストレージ アカウントに対して、マネージド サービス ID のロール割り当てを作成できる必要があります。 ロール割り当てを作成するアクセス許可を保持しているのは、**所有者**ロール、管理者ロール、または Microsoft.Authorization/ロール割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールです。 

対象のストレージ アカウントの所有者ではなく、Azure Data Share リソースのマネージド ID に対するロール割り当てを自分で作成できない場合は、自分の代わりにロール割り当てを作成するよう Azure 管理者に依頼できます。 

Data Share リソースのマネージド ID に割り当てられているロールの概要を次に示します。

| |  |  |
|---|---|---|
|**ストレージの種類**|**データ プロバイダーのソース ストレージ アカウント**|**データ コンシューマーのターゲット ストレージ アカウント**|
|Azure Blob Storage| ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|Azure Data Lake Gen1 | Owner | サポートされていません
|Azure Data Lake Gen2 | ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|
### <a name="data-providers"></a>データ プロバイダー 
データセットを Azure データ共有に追加するには、データ プロバイダー データ共有リソースのマネージド ID をストレージ BLOB データ閲覧者ロールに追加する必要があります。 これは Azure Data Share サービスによって自動的に実行されます (ただし、ユーザーが Azure を使用してデータセットを追加しようとしており、かつ、ストレージ アカウントの所有者であるか、Microsoft.Authorization/ロール割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールのメンバーである場合)。 

または、ユーザーは Azure 管理者に依頼して、データ共有リソースのマネージド ID をストレージ BLOB データ閲覧者ロールに手動で追加してもらうこともできます。 このロール割り当てを管理者が手動で作成すると、ストレージ アカウントの所有者であるか、カスタム ロールが割り当てられている必要はなくなります。 これは、Azure Storage または Azure Data Lake Gen2 から共有されているデータに適用されます。 

Azure Data Lake Gen1 からデータを共有する場合は、所有者ロールに対してロール割り当てを作成する必要があります。 

Data Share リソースのマネージド ID に対してロール割り当てを作成するには、次の手順に従います。

1. ストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. *[ロール]* で、 *[ストレージ BLOB データ閲覧者]* を選択します。
1. *[選択]* で、Azure Data Share アカウントの名前を入力します。
1. [ *Save*] をクリックします。

### <a name="data-consumers"></a>データ コンシューマー
データを受信するには、データ コンシューマー データ共有リソースのマネージド ID をストレージ BLOB データ共同作成者ロールに追加する必要があります。 このロールは、Azure Data Share サービスでストレージ アカウントに書き込むことができるようにするために必要です。 これは Azure Data Share サービスによって自動的に実行されます (ただし、ユーザーが Azure を使用してデータセットを追加しようとしており、かつ、ストレージ アカウントの所有者であるか、Microsoft.Authorization/ロール割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールのメンバーである場合)。 

または、ユーザーは Azure 管理者に依頼して、データ共有リソースのマネージド ID をストレージ BLOB データ共同作成者ロールに手動で追加してもらうこともできます。 このロール割り当てを管理者が手動で作成すると、ストレージ アカウントの所有者であるか、カスタム ロールが割り当てられている必要はなくなります。 これは、Azure Storage または Azure Data Lake Gen2 に共有されているデータに適用されることに注意してください。 Azure Data Lake Gen1 へのデータの受信はサポートされていません。 

Data Share リソースのマネージド ID に対してロール割り当てを手動で作成するには、次の手順に従います。

1. ストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. *[ロール]* で、 *[ストレージ BLOB データ共同作成者]* を選択します。 
1. *[選択]* で、Azure Data Share アカウントの名前を入力します。
1. [ *Save*] をクリックします。

REST API を使用してデータを共有する場合は、データ共有アカウントを適切なロールに追加することによって、これらのロール割り当てを手動で作成する必要があります。 

ロール割り当てを追加する方法の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)を参照してください。Azure リソースにロール割り当てを追加する方法を説明しています。 

## <a name="resource-provider-registration"></a>リソース プロバイダーの再登録 

Azure Data Share の招待を承諾するときは、サブスクリプションに Microsoft.DataShare リソース プロバイダーを手動で登録する必要があります。 Microsoft.DataShare リソース プロバイダーを Azure サブスクリプションに登録するには、次の手順に従います。 

1. Azure portal で、 **[サブスクリプション]** に移動します。
1. Azure Data Share に使っているサブスクリプションを選択します。
1. **[リソース プロバイダー]** をクリックします。
1. Microsoft.DataShare を検索します。
1. **[登録]** をクリックします。

## <a name="next-steps"></a>次の手順

- Azure でのロールの詳細 - [ロール定義について](../role-based-access-control/role-definitions.md)

