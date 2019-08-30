---
title: Azure Data Explorer の Python ライブラリを使用してデータのクエリを実行する
description: この記事では、Python を使用して Azure Data Explorer からデータをクエリする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 2c32e67bb2b47a24036a341ea4e1b83037abbaee
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827534"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Azure Data Explorer の Python ライブラリを使用してデータのクエリを実行する

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure データ エクスプ ローラーでは、[Python 用のデータ クライアント ライブラリ](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)が提供されています。 このライブラリを使用すると、コードからデータをクエリできます。 この記事では、学習を支援するために設定した*ヘルプ クラスター*上のテーブルに接続します。 次に、そのクラスター上のテーブルをクエリし、結果を返します。

この記事は [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb) でも利用できます。

## <a name="prerequisites"></a>前提条件

* Azure Active Directory (AAD) のメンバーである、組織の電子メール アカウント

* 開発用コンピューターに [Python](https://www.python.org/downloads/) がインストール済み

## <a name="install-the-data-library"></a>データ ライブラリをインストールする

*azure-kusto-data* をインストールします。

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>import ステートメントおよび定数を追加する

ライブラリのほか、データ分析ライブラリである *pandas* からクラスをインポートします。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Azure データ エクスプ ローラーでは、アプリケーションを認証するために AAD テナント ID が使用されます。 テナント ID を検索するには、次の URL を使用し、ドメインを *YourDomain* に置き換えます。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

たとえば、ドメインが *contoso.com* の場合、URL は [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/) になります。 結果を表示するには、この URL をクリックします。最初の行は次のとおりです。

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

この場合のテナント ID は `6babcaad-604b-40ac-a9d7-9fd97c0b779f` です。 このコードを実行する前に、AAD_TENANT_ID の値を設定します。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

では、接続文字列を作成します。 この例では、デバイス認証を使用してクラスターにアクセスします。 [AAD アプリケーション証明書](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)、[AAD アプリケーション キー](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)、および [AAD ユーザーとパスワード](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)を使用することもできます。

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Azure データ エクスプローラーに接続し、クエリを実行する

クラスターに対してクエリを実行し、データ フレームに出力を格納します。 このコードを実行すると、次のようなメッセージが返されます。"*サインインするには、Web ブラウザーを使用して https://microsoft.com/devicelogin ページを開き、認証するためのコード F3W4VWZDM を入力します*。" この手順に従ってサインインし、元のページに戻って次のコード ブロックを実行します。

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>データフレームでデータを確認する

サインインを入力すると、クエリによって結果が返され、データ フレームに格納されます。 他のすべてのデータ フレームと同様に、結果を操作できます。

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

StormEvents テーブルの上位 10 件の結果が表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)
