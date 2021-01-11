---
title: Azure Data Share のロールと要件
description: Azure Data Share を使用してデータを共有および受信するために必要なアクセス許可について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 07/30/2020
ms.openlocfilehash: 84d1ba6ff343b5f3d1f88d7ae5c618601f416e2c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513767"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share のロールと要件 

この記事では、Azure Data Share サービスを使用してデータを共有および受信するために必要なロールとアクセス許可について説明します。 

## <a name="roles-and-requirements"></a>ロールと要件

Azure Data Share サービスを使用すると、データ プロバイダーとコンシューマーの間で資格情報を交換することなくデータを共有できます。 Azure Data Share サービスでは、Azure データ ストアに対する認証を行うためにマネージド ID (旧称 MSI) が使用されます。 

Azure Data Share リソースのマネージド ID には、Azure データ ストアへのアクセス権が付与されている必要があります。 次に、Azure Data Share サービスではこのマネージド ID を使用して、スナップショット ベースの共有の場合はデータの読み取りと書き込みが、インプレース共有の場合はシンボリック リンクの確立が行われます。 

Azure データ ストアからデータを共有または受信するには、ユーザーに少なくとも以下のアクセス許可が必要です。 SQL ベースの共有には追加のアクセス許可が必要です。

* その Azure データ ストアに対する書き込みアクセス許可。 通常、このアクセス許可は**共同作成者**ロールにあります。
* その Azure データ ストアでロールの割り当てを作成するためのアクセス許可。 通常、ロールの割り当てを作成するためのアクセス許可は、**所有者**ロール、ユーザー アクセス管理者ロール、または Microsoft.Authorization/ロールの割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールにあります。 データ共有リソースのマネージド ID に Azure データ ストアへのアクセス権が既に付与されている場合は、このアクセス許可は必要ありません。 必要なロールについては、以下の表を参照してください。

Data Share リソースのマネージド ID に割り当てられるロールの概要を次に示します。

|**データ ストアの種類**|**データ プロバイダーのソース データ ストア**|**データ コンシューマーのターゲット データ ストア**|
|---|---|---|
|Azure Blob Storage| ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|Azure Data Lake Gen1 | 所有者 | サポートされていません
|Azure Data Lake Gen2 | ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|Azure SQL Server | SQL DB Contributor | SQL DB Contributor
|Azure Data Explorer クラスター | Contributor | Contributor
|

SQL ベースの共有を行う場合、Azure SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 その SQL ユーザーに必要なアクセス許可の概要を次に示します。

|**SQL データベースの種類**|**データ プロバイダーの SQL ユーザーのアクセス許可**|**データ コンシューマーの SQL ユーザーのアクセス許可**|
|---|---|---|
|Azure SQL データベース | db_datareader | db_datareader、db_datawriter、db_ddladmin
|Azure Synapse Analytics (旧称 SQL DW) | db_datareader | db_datareader、db_datawriter、db_ddladmin
|

### <a name="data-provider"></a>データ プロバイダー

Azure Data Share にデータセットを追加するには、プロバイダーのデータ共有リソースのマネージド ID にソース Azure データ ストアへのアクセス権が付与されている必要があります。 たとえば、ストレージ アカウントを使用する場合は、データ共有リソースのマネージド ID にストレージ BLOB データ閲覧者ロールが付与されます。 

これは、ユーザーが Azure portal を使用してデータセットを追加する際に、そのユーザーが適切なアクセス許可を持っていれば、Azure Data Share サービスによって自動的に行われます。 たとえば、ユーザーが Azure データ ストアの所有者である場合や、Microsoft.Authorization/ロールの割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールのメンバーである場合などです。 

または、ユーザーが、Azure データ ストアの所有者にデータ共有リソースのマネージド ID を手動で Azure データ ストアに追加させることもできます。 この操作は、データ共有リソースごとに 1 回だけ実行する必要があります。

データ共有リソースのマネージド ID に対してロールの割り当てを作成するには、次の手順に従います。

1. Azure データ ストアに移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. *[ロール]* の下で、上記のロールの割り当て表に記載されているロールを選択します (たとえば、ストレージ アカウントの場合は、 *[ストレージ BLOB データ閲覧者]* を選択します)。
1. *[選択]* の下に、使用する Azure Data Share リソースの名前を入力します。
1. [*保存*] をクリックします。

SQL ベースのソースの場合は、上記の手順に加えて、SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 このユーザーには *db_datareader* アクセス許可を付与する必要があります。 サンプル スクリプトと、SQL ベースの共有に必要なその他の前提条件については、「[データの共有](share-your-data.md)」のチュートリアルを参照してください。 

### <a name="data-consumer"></a>データ コンシューマー
データを受信するには、コンシューマーのデータ共有リソースのマネージド ID に、ターゲット Azure データ ストアへのアクセス権が付与されている必要があります。 たとえば、ストレージ アカウントを使用する場合は、データ共有リソースのマネージド ID にストレージ BLOB データ共同作成者ロールが付与されます。 

これは、ユーザーが Azure portal を使用してターゲット データ ストアを指定する場合に、そのユーザーが適切なアクセス許可を持っていれば、Azure Data Share サービスによって自動的に行われます。 たとえば、ユーザーが Azure データ ストアの所有者である場合や、Microsoft.Authorization/ロールの割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールのメンバーである場合などです。 

または、ユーザーが、Azure データ ストアの所有者にデータ共有リソースのマネージド ID を手動で Azure データ ストアに追加させることもできます。 この操作は、データ共有リソースごとに 1 回だけ実行する必要があります。

データ共有リソースのマネージド ID に対してロールの割り当てを手動で作成するには、次の手順に従います。

1. Azure データ ストアに移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. *[ロール]* の下で、上記のロールの割り当て表に記載されているロールを選択します (たとえば、ストレージ アカウントの場合は、 *[ストレージ BLOB データ閲覧者]* を選択します)。
1. *[選択]* の下に、使用する Azure Data Share リソースの名前を入力します。
1. [*保存*] をクリックします。

SQL ベースのターゲットの場合は、上記の手順に加えて、SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 このユーザーには、*db_datareader、db_datawriter、db_ddladmin* アクセス許可を付与する必要があります。 サンプル スクリプトと、SQL ベースの共有に必要なその他の前提条件については、[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルを参照してください。 

REST API を使用してデータを共有する場合は、これらのロールの割り当てを手動で作成する必要があります。 

ロールの割り当てを追加する方法の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)を参照してください。 

## <a name="resource-provider-registration"></a>リソース プロバイダーの登録 

次のシナリオでは、Microsoft.DataShare リソース プロバイダーを Azure サブスクリプションに手動で登録することが必要になる場合があります。 

* 自分の Azure テナントで Azure Data Share の招待を初めて表示する
* Azure Data Share リソースとは異なる Azure サブスクリプションで、Azure データ ストアからのデータを共有する
* Azure Data Share リソースとは異なる Azure サブスクリプションで、Azure データ ストアへのデータを受信する

Microsoft.DataShare リソース プロバイダーを Azure サブスクリプションに登録するには、次の手順に従います。 リソース プロバイダーを登録するには、Azure サブスクリプションへの "*共同作成者*" アクセス権が必要です。

1. Azure portal で、 **[サブスクリプション]** に移動します。
1. Azure Data Share に使っているサブスクリプションを選択します。
1. **[リソース プロバイダー]** をクリックします。
1. Microsoft.DataShare を検索します。
1. **[登録]** をクリックします。

## <a name="next-steps"></a>次のステップ

- Azure でのロールの詳細 - [ロール定義について](../role-based-access-control/role-definitions.md)
