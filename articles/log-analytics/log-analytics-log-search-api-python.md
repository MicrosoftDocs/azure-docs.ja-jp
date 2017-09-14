---
title: "Azure Log Analytics からデータを取得するPython スクリプト | Microsoft Docs"
description: "Log Analytics のログ検索 API を使用して、任意の REST API クライアントで Log Analytics ワークスペースからデータを取得することができます。  この記事では、ログ検索 API を使用するPython スクリプトの例を示します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 56d7c6dc648a01e7b0efc167cb65c94bac5468ec
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---

# <a name="retrieve-data-from-log-analytics-with-a-python-script"></a>Python スクリプトを使用して Log Analytics からデータを取得する
[Log Analytics のログ検索 API](log-analytics-log-search-api.md) を使用して、任意の REST API クライアントで Log Analytics ワークスペースからデータを取得することができます。  この記事では、Log Analytics のログ検索 API を使用する Python スクリプトの例を示します。  

## <a name="authentication"></a>認証
このスクリプトでは、Azure Active Directory のサービス プリンシパルを使用してワークスペースを認証します。  サービス プリンシパルは、クライアント アプリケーションがアカウント名を持っていない場合でも、サービスがアカウントを認証することを要求できるようにします。 このスクリプトを実行する前に、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)」の手順に従ってサービス プリンシパルを作成する必要があります。  アプリケーション ID、テナント ID、および認証キーをスクリプトに指定する必要があります。 

> [!NOTE]
> [Azure Automation アカウントを作成](../automation/automation-create-standalone-account.md)すると、このスクリプトでの使用に適したサービス プリンシパルが作成されます。  Azure Automation によって作成されたサービス プリンシパルが既にある場合は、新しいものを作成する代わりにそれを使用することができますが、認証キーがない場合は、[認証キーの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)が必要になることがあります。

## <a name="script"></a>スクリプト
``` python
import adal
import requests
import json
import datetime
from pprint import pprint

# Details of workspace.  Fill in details for your workspace.
resource_group = 'xxxxxxxx'
workspace = 'xxxxxxxx'

# Details of query.  Modify these to your requirements.
query = "Type=Event"
end_time = datetime.datetime.utcnow()
start_time = end_time - datetime.timedelta(hours=24)
num_results = 100  # If not provided, a default of 10 results will be used.

# IDs for authentication.  Fill in values for your service principal.
subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
tenant_id = 'xxxxxxxx-xxxx-xxxx-xxx-xxxxxxxxxxxx'
application_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx'
application_key = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'

# URLs for authentication
authentication_endpoint = 'https://login.microsoftonline.com/'
resource  = 'https://management.core.windows.net/'

# Get access token
context = adal.AuthenticationContext('https://login.microsoftonline.com/' + tenant_id)
token_response = context.acquire_token_with_client_credentials('https://management.core.windows.net/', application_id, application_key)
access_token = token_response.get('accessToken')

# Add token to header
headers = {
    "Authorization": 'Bearer ' + access_token,
    "Content-Type":'application/json'
}

# URLs for retrieving data
uri_base = 'https://management.azure.com'
uri_api = 'api-version=2015-11-01-preview'
uri_subscription = 'https://management.azure.com/subscriptions/' + subscription_id
uri_resourcegroup = uri_subscription + '/resourcegroups/'+ resource_group
uri_workspace = uri_resourcegroup + '/providers/Microsoft.OperationalInsights/workspaces/' + workspace
uri_search = uri_workspace + '/search'

# Build search parameters from query details
search_params = {
        "query": query,
        "top": num_results,
        "start": start_time.strftime('%Y-%m-%dT%H:%M:%S'),
        "end": end_time.strftime('%Y-%m-%dT%H:%M:%S')
        }

# Build URL and send post request
uri = uri_search + '?' + uri_api
response = requests.post(uri,json=search_params,headers=headers)

# Response of 200 if successful
if response.status_code == 200:

    # Parse the response to get the ID and status
    data = response.json()
    search_id = data["id"].split("/")
    id = search_id[len(search_id)-1]
    status = data["__metadata"]["Status"]

    # If status is pending, then keep checking until complete
    while status == "Pending":

        # Build URL to get search from ID and send request
        uri_search = uri_search + '/' + id
        uri = uri_search + '?' + uri_api
        response = requests.get(uri,headers=headers)

        # Parse the response to get the status
        data = response.json()
        status = data["__metadata"]["Status"]

else:

    # Request failed
    print (response.status_code)
    response.raise_for_status()

print ("Total records:" + str(data["__metadata"]["total"]))
print ("Returned top:" + str(data["__metadata"]["top"]))
pprint (data["value"])
```
## <a name="next-steps"></a>次のステップ
- [Log Analytics のログ検索 API](log-analytics-log-search-api.md) の詳細を確認します。
