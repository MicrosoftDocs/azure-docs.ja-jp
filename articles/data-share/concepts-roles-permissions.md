---
title: Azure Data Share のロールと要件
description: Azure Data Share を使用してデータを共有および受信するために必要なアクセス許可について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f5c5d6da239d302b57bdb37e9d49116a29c1ccb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558126"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share のロールと要件 

この記事では、Azure Data Share サービスを使用してデータを共有および受信するために必要なロールとアクセス許可について説明します。 

## <a name="roles-and-requirements"></a>ロールと要件

Azure Data Share サービスを使用すると、データ プロバイダーとコンシューマーの間で資格情報を交換することなくデータを共有できます。 Azure Data Share サービスでは、Azure データ ストアに対する認証を行うためにマネージド ID (旧称 MSI) が使用されます。 

Azure Data Share リソースのマネージド ID には、Azure データ ストアへのアクセス権が付与されている必要があります。 次に、Azure Data Share サービスではこのマネージド ID を使用して、スナップショット ベースの共有の場合はデータの読み取りと書き込みが、インプレース共有の場合はシンボリック リンクの確立が行われます。 

Azure データ ストアからデータを共有または受信するには、ユーザーに少なくとも以下のアクセス許可が必要です。 SQL ベースの共有には追加のアクセス許可が必要です。

* その Azure データ ストアに対する書き込みアクセス許可。 通常、このアクセス許可は **共同作成者** ロールにあります。
* その Azure データ ストアでロールの割り当てを作成するためのアクセス許可。 通常、ロールの割り当てを作成するためのアクセス許可は、**所有者** ロール、ユーザー アクセス管理者ロール、または Microsoft.Authorization/ロールの割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールにあります。 データ共有リソースのマネージド ID に Azure データ ストアへのアクセス権が既に付与されている場合は、このアクセス許可は必要ありません。 必要なロールについては、以下の表を参照してください。

Data Share リソースのマネージド ID に割り当てられるロールの概要を次に示します。

|**データ ストアの種類**|**データ プロバイダーのソース データ ストア**|**データ コンシューマーのターゲット データ ストア**|
|---|---|---|
|Azure Blob Storage| ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|Azure Data Lake Gen1 | 所有者 | サポートされていません
|Azure Data Lake Gen2 | ストレージ BLOB データ閲覧者 | ストレージ BLOB データ共同作成者
|Azure Data Explorer クラスター | Contributor | Contributor
|

SQL ベースの共有を行う場合、Azure SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 このユーザーを作成するには、Azure Active Directory の管理者権限が必要です。 その SQL ユーザーに必要なアクセス許可の概要を次に示します。

|**SQL データベースの種類**|**データ プロバイダーの SQL ユーザーのアクセス許可**|**データ コンシューマーの SQL ユーザーのアクセス許可**|
|---|---|---|
|Azure SQL データベース | db_datareader | db_datareader、db_datawriter、db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader、db_datawriter、db_ddladmin
|

### <a name="data-provider"></a>データ プロバイダー

Azure Data Share にデータセットを追加するには、プロバイダーのデータ共有リソースのマネージド ID にソース Azure データ ストアへのアクセス権が付与されている必要があります。 たとえば、ストレージ アカウントを使用する場合は、データ共有リソースのマネージド ID にストレージ BLOB データ閲覧者ロールが付与されます。 

これは、ユーザーが Azure portal を使用してデータセットを追加する際に、そのユーザーが適切なアクセス許可を持っていれば、Azure Data Share サービスによって自動的に行われます。 たとえば、ユーザーが Azure データ ストアの所有者である場合や、Microsoft.Authorization/ロールの割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールのメンバーである場合などです。 

または、ユーザーが、Azure データ ストアの所有者にデータ共有リソースのマネージド ID を手動で Azure データ ストアに追加させることもできます。 この操作は、データ共有リソースごとに 1 回だけ実行する必要があります。

データ共有リソースのマネージド ID に対してロールの割り当てを手動で作成するには、次の手順に従います。  

1. Azure データ ストアに移動します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. *[ロール]* の下で、上記のロールの割り当て表に記載されているロールを選択します (たとえば、ストレージ アカウントの場合は、 *[ストレージ BLOB データ閲覧者]* を選択します)。
1. *[選択]* の下に、使用する Azure Data Share リソースの名前を入力します。
1. [*保存*] をクリックします。

ロールの割り当ての詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。 REST API を使用してデータを共有している場合は、「[REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)」を参照することによって、API を使用してロールの割り当てを作成できます。 

SQL ベースのソースの場合は、Azure Active Directory 認証を使用して SQL データベースに接続する一方で、SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 このユーザーには *db_datareader* アクセス許可を付与する必要があります。 サンプル スクリプトと、SQL ベースの共有に必要なその他の前提条件については、「[Azure SQL Database または Azure Synapse Analytics からの共有](how-to-share-from-sql.md)」のチュートリアルを参照してください。 

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

ロールの割り当ての詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。 REST API を使用してデータを受信している場合は、「[REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)」を参照することによって、API を使用してロールの割り当てを作成できます。 

SQL ベースのターゲットの場合は、Azure Active Directory 認証を使用して SQL データベースに接続する一方で、SQL Database に、Azure Data Share リソースと同じ名前を使用して、外部プロバイダーから SQL ユーザーを作成する必要があります。 このユーザーには、*db_datareader、db_datawriter、db_ddladmin* アクセス許可を付与する必要があります。 サンプル スクリプトと、SQL ベースの共有に必要なその他の前提条件については、「[Azure SQL Database または Azure Synapse Analytics からの共有](how-to-share-from-sql.md)」のチュートリアルを参照してください。 

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
 
リソース プロバイダーの詳細については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください.

## <a name="next-steps"></a>次のステップ

- Azure でのロールの詳細 - [Azure ロールの定義について](../role-based-access-control/role-definitions.md)