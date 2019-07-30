---
title: Python を使用して Azure Data Lake Analytics を管理する
description: この記事では、Python を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、ジョブを管理する方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355984"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Python を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

この記事では、Python を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、ジョブを管理する方法について説明します。

## <a name="supported-python-versions"></a>サポートされている Python のバージョン

* Python の 64 ビット バージョンを使用します。
* **[Python.org ダウンロード](https://www.python.org/downloads/)** にある標準の Python ディストリビューションを使用できます。 
* 多くの開発者は、 **[Anaconda Python ディストリビューション](https://www.anaconda.com/download/)** を使用すると便利なことがわかります。  
* この資料は、標準の Python ディストリビューションからの Python バージョン 3.6 を使用して作成されました。

## <a name="install-azure-python-sdk"></a>Azure Python SDK をインストールする

次のモジュールをインストールします。

* **azure-mgmt-resource** モジュールには、Active Directory 用のその他の Azure モジュールなどが含まれています。
* **azure-datalake-store** モジュールには、Azure Data Lake Store ファイル システム操作が含まれています。 
* **azure-mgmt-datalake-store** モジュールには、Azure Data Lake Store アカウント管理操作が含まれています。
* **azure-mgmt-datalake-analytics** モジュールには、Azure Data Lake Analytics 操作が含まれています。 

最初に、次のコマンドを実行して最新の `pip` があることを確認します。

```
python -m pip install --upgrade pip
```

このドキュメントは、`pip version 9.0.1` を使用して記述されています。

コマンドラインからモジュールをインストールするには、次の `pip` コマンドを使用します。

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>新しい Python スクリプトを作成する

以下のコードをスクリプトに貼り付けます。

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

このスクリプトを実行して、モジュールをインポートできることを確認します。

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>ポップアップを使用した対話型ユーザー認証

この方法はサポートされていません。

### <a name="interactive-user-authentication-with-a-device-code"></a>デバイス コードを使用した対話型ユーザー認証

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>SPI とシークレットを使用した非対話型認証

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>API と証明書を使用した非対話型認証

この方法はサポートされていません。

## <a name="common-script-variables"></a>共通スクリプト変数

これらの変数は、サンプルで使用します。

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>クライアントを作成する

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

最初にストア アカウントを作成します。

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
次にそのストアを使用する ADLA アカウントを作成します。

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>ジョブの送信

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>ジョブが終了するまで待機する

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>パイプラインと反復を一覧表示する
ジョブに関連付けられているパイプラインまたは反復メタデータがあるかどうかにより、パイプラインと反復を一覧表示できます。

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>コンピューティング ポリシーを管理する

DataLakeAnalyticsAccountManagementClient オブジェクトでは、Data Lake Analytics アカウントのコンピューティング ポリシーを管理するためのメソッドが提供されています。

### <a name="list-compute-policies"></a>コンピューティング ポリシーを一覧表示する

次のコードは、Data Lake Analytics アカウントのコンピューティング ポリシーの一覧を取得します。

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>新しいコンピューティング ポリシーを作成する

次のコードは、Data Lake Analytics アカウントの新しいコンピューティング ポリシーを作成し、指定したユーザーが使用できる最大 AU を 50 に、最小ジョブ優先順位を 250 に設定します。

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>次の手順

- 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
- U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
- 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。

