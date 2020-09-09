---
title: Apache Kafka REST プロキシ - Azure HDInsight
description: Azure HDInsight で Kafka REST プロキシを使用して Apache Kafka 操作を実行する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 508d054bc4eed88867bb6e3282edbafaae9a5247
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298047"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST プロキシを使用して Azure HDInsight で Apache Kafka クラスターを操作する

Kafka REST プロキシを使用すると、HTTP 経由の REST API を使用して Kafka クラスターを操作することができます。 この操作は、Kafka クライアントを仮想ネットワークの外部に配置できることを意味します。 クライアントは、Kafka ライブラリを利用するのではなく、Kafka クラスターに対して単純な HTTP 呼び出しを行うことができます。 この記事では、REST プロキシが有効な Kafka クラスターを作成する方法について説明します。 また、REST プロキシを呼び出す方法を示すサンプル コードも提供します。

## <a name="rest-api-reference"></a>REST API リファレンス

Kafka REST API によってサポートされる操作については、[HDInsight Kafka REST Proxy の API リファレンス](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)を参照してください。

## <a name="background"></a>バックグラウンド

![Kafka REST プロキシ設計](./media/rest-proxy/rest-proxy-architecture.png)

API でサポートされている操作の詳細については、[Apache Kafka REST プロキシ API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy) を参照してください。

### <a name="rest-proxy-endpoint"></a>REST プロキシ エンドポイント

REST プロキシを使用して HDInsight Kafka クラスターを作成すると、クラスターの新しいパブリック エンドポイントが作成されます。これは、Azure portal の HDInsight クラスターの **[プロパティ]** にあります。

### <a name="security"></a>Security

Kafka REST プロキシへのアクセスは Azure Active Directory セキュリティ グループで管理されます。 Kafka クラスターを作成するときに、Azure AD セキュリティ グループに REST エンドポイント アクセスを提供します。 REST プロキシにアクセスする必要がある Kafka クライアントは、グループの所有者によってこのグループに登録される必要があります。 グループの所有者は、ポータルまたは PowerShell を使用して登録することができます。

REST プロキシ エンドポイント要求の場合、クライアント アプリケーションは OAuth トークンを取得する必要があります。 このトークンは、セキュリティ グループのメンバーシップを確認するために使用されます。 OAuth トークンを取得する方法については、後述する「[クライアント アプリケーションのサンプル](#client-application-sample)」を参照してください。 クライアント アプリケーションは、HTTP 要求の OAuth トークンを REST プロキシに渡します。

> [!NOTE]
> AAD セキュリティ グループの詳細については、「[Azure Active Directory グループを使用したアプリとリソース アクセスの管理](../../active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。 OAuth トークンのしくみについては、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../../active-directory/develop/v1-protocols-oauth-code.md)」を参照してください。

## <a name="kafka-rest-proxy-with-network-security-groups"></a>ネットワーク セキュリティ グループを使用した Kafka REST プロキシ
独自の VNet を利用し、ネットワーク セキュリティ グループを使用してネットワーク トラフィックを制御する場合は、ポート 443 に加えてポート **9400** で**受信**トラフィックを許可します。 これにより、Kafka REST プロキシ サーバーにアクセスできるようになります。

## <a name="prerequisites"></a>前提条件

1. アプリケーションを Azure AD に登録する。 Kafka REST プロキシを操作するために記述するクライアント アプリケーションは、このアプリケーションの ID とシークレットを使用して Azure に対する認証を行います。

1. Azure AD セキュリティ グループを作成する。 Azure AD に登録したアプリケーションをセキュリティ グループにグループの**メンバー**として追加します。 このセキュリティ グループは、REST プロキシの操作を許可するアプリケーションを制御するために使用されます。 Azure AD グループの作成方法の詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

    グループの種類が **[セキュリティ]** であることを確認します。
    ![セキュリティ グループ](./media/rest-proxy/rest-proxy-group.png)

    アプリケーションがグループのメンバーであることを確認します。
    ![メンバーシップの確認](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST プロキシが有効な Kafka クラスターを作成する

以下の手順では、Azure portal を使用します。 Azure CLI の使用例については、[Azure CLI を使用した Apache Kafka REST プロキシ クラスターの作成](tutorial-cli-rest-proxy.md)に関するページを参照してください。

1. Kafka クラスター作成ワークフローの実行中に、 **[セキュリティとネットワーク]** タブで、 **[Enable Kafka REST proxy]\(Kafka REST プロキシを有効にする\)** オプションをオンにします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **[セキュリティ グループの選択]** をクリックします。 セキュリティ グループの一覧から、REST プロキシにアクセスするセキュリティ グループを選択します。 検索ボックスを使用して、適切なセキュリティ グループを見つけることができます。 下部にある **[選択]** ボタンをクリックします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 「[Azure portal を使用して Azure HDInsight 内に Apache Kafka クラスターを作成する](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)」の説明に従って、残りのステップを完了してクラスターを作成します。

1. クラスターが作成されたら、クラスターのプロパティにアクセスして Kafka REST プロキシ URL を記録します。

     ![REST プロキシ URL の表示](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>クライアント アプリケーションのサンプル

次の python コードを使用して、Kafka クラスターで REST プロキシを操作できます。 コード サンプルを使用するには、次のステップを実行します。

1. Python がインストールされているマシンにサンプルコードを保存します。
1. `pip3 install msal` を実行して、必要な Python 依存関係をインストールします。
1. 実際の環境に合わせて **Configure these properties** のコード セクションを変更し、次のプロパティを変更します。

    |プロパティ |説明 |
    |---|---|
    |テナント ID|該当するサブスクリプションがある Azure テナント。|
    |クライアント ID|セキュリティ グループに登録したアプリケーションの ID。|
    |クライアント シークレット|セキュリティ グループに登録したアプリケーションのシークレット。|
    |Kafkarest_endpoint|この値は、[デプロイ セクション](#create-a-kafka-cluster-with-rest-proxy-enabled)の説明に従って、クラスターの概要の **[プロパティ]** タブから取得します。 `https://<clustername>-kafkarest.azurehdinsight.net` という形式にする必要があります。|

1. コマンド ラインから、`sudo python3 <filename.py>` を実行して python ファイルを実行します

このコードは、次の操作を実行します。

1. Azure AD から OAuth トークンを取得します。
1. Kafka REST プロキシに対して要求を行う方法を示します。

Python での OAuth トークンの取得の詳細については、[Python の AuthenticationContext クラス](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)に関する記事を参照してください。 Kafka REST プロキシで作成または削除されていない `topics` がそこに反映される間、遅延が発生する可能性があります。 この遅延は、キャッシュの更新によるものです。

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_custom_value_json_object():

    custom_value_json_object = {
        "static_value": "welcome to HDI Kafka REST proxy",
        "random_value": get_random_string(),
    }

    return custom_value_json_object


def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"
        },
        {
            "value": "5"
        },
        {
            "partition": 0,
            "value": json.dumps(get_custom_value_json_object())  # need to be a serialized string. For example, "{\"static_value\": \"welcome to HDI Kafka REST proxy\", \"random_value\": \"pAPrgPk\"}"
        },
        {
            "value": json.dumps(get_custom_value_json_object())  # need to be a serialized string. For example, "{\"static_value\": \"welcome to HDI Kafka REST proxy\", \"random_value\": \"pAPrgPk\"}"
        }
    ]
}

print("Producing 4 messages in a request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
```

curl コマンドを使用して REST プロキシ用のトークンを Azure から取得する方法については、以下の別のサンプルを参照してください。 **トークンの取得中に指定された `scope=https://hib.azurehdinsight.net/.default` が必要であることに注意してください。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>次のステップ

* [Kafka REST プロキシ API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
