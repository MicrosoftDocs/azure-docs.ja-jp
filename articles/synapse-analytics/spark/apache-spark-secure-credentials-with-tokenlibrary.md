---
title: Apache Spark for Azure Synapse Analytics でリンクされたサービスを使用してアクセス資格情報をセキュリティで保護する
description: この記事では、リンクされたサービスとトークン ライブラリを使用して、Apache Spark for Azure Synapse Analytics を他のサービスと安全に統合する方法についての概念を説明します
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693579"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>リンクされたサービスと TokenLibrary を使用して資格情報をセキュリティで保護する

外部ソースからデータにアクセスすることは、よくあるパターンです。 外部データソースで匿名アクセスが許可されていない限り、資格情報、シークレット、または接続文字列を使用して接続をセキュリティで保護することがおそらく必要になります。  

Synapse では、リソース間の認証に既定で Azure Active Directory (AAD) パススルーを使用します。  他の資格情報を使用してリソースに接続する必要がある場合は、TokenLibrary を直接使用します。  TokenLibrary により、リンクされたサービスまたは Azure Key Vault に格納されている SAS トークン、AAD トークン、接続文字列、シークレットを取得するプロセスが簡略化されます。

Azure Key Vault からシークレットを取得する場合は、Azure Key Vault にリンクされたサービスを作成することをお勧めします。  Synapse ワークスペースの管理サービス ID (MSI) に、Azure Key Vault に対するシークレット取得権限があることを確認してください。  Synapse は、Synapse ワークスペースの管理サービス ID を使用して Azure Key Vault に対して認証を行います。 リンクされたサービスを使用せずに Azure Key Vault に直接接続する場合は、ユーザーの Azure Active Directory 資格情報を使用して認証を行います。

詳細については、[リンクされたサービス](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

## <a name="usage"></a>使用法

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
この関数は、TokenLibrary のヘルプ ドキュメントを表示します。

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 の TokenLibrary

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 プライマリ ストレージ

プライマリ Azure Data Lake Storage のファイルへのアクセスでは、認証に既定で Azure Active Directory パススルーが使用されるため、TokenLibrary を明示的に使用する必要はありません。

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2 ストレージ (リンクされたサービスを使用する場合)

Azure Data Lake Storage Gen2 に接続する際に、Synapse によって、リンクされたサービスが統合されたエクスペリエンスが提供されます。  リンクされたサービスは、**アカウント キー**、**サービス プリンシパル**、**マネージド ID**、または **資格情報** を使用して認証するように構成できます。

リンクされたサービスの認証方法が **アカウント キー** に設定されている場合、リンクされたサービスは指定されたストレージ アカウント キーを使用して認証を行い、SAS キーを要求し、**LinkedServiceBasedSASProvider** を使用してそれを自動的にストレージ要求に適用します。

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

リンクされたサービスの認証方法が **マネージド ID** または **サービス プリンシパル** に設定されている場合、リンクされたサービスは **LinkedServiceBasedTokenProvider** プロバイダーと共にマネージド ID またはサービス プリンシパルのトークンを使用します。  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 ストレージ (リンクされたサービスを使用しない場合)

SAS キーを使用して ADLS Gen2 ストレージに直接接続する場合は、**ConfBasedSASProvider** を使用し、SAS キーを **spark.storage.synapse.sas** 構成設定に指定します。

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2 ストレージ (Azure Key Vault を使用する場合)

Azure Key Vault シークレットに格納されている SAS トークンを使用して ADLS Gen2 ストレージに接続します。  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>その他のリンクされたサービスの TokenLibrary

他のリンクされたサービスに接続するには、TokenLibrary を直接呼び出すことができます。

#### <a name="getconnectionstring"></a>getConnectionString()

 接続文字列を取得するには、**getConnectionString** 関数を使用し、**リンクされたサービスの名前** を渡します。

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

getConnectionStringAsMap は、Scala および Python で使用可能なヘルパー関数で、次のような接続文字列内にある "_キーと値_" のペアから特定の値を解析します。

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

**getConnectionStringAsMap** 関数を使用してキーを渡し、値を返します。  上記の接続文字列の例では、 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

は次を返します

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Azure Key Vault に格納されているシークレットを取得する場合は、Synapse ワークスペース内で Azure Key Vault へのリンクされたサービスを作成することをお勧めします。 Azure Key Vault に対するシークレット **取得** アクセス許可を Synapse ワークスペースの管理サービス ID に付与する必要があります。  リンクされたサービスは、管理サービス ID を使用して Azure Key Vault サービスに接続して、シークレットを取得します。  それ以外の場合は、Azure Key Vault に直接接続すると、ユーザーの Azure Active Directory (AAD) 資格情報が使用されます。  この場合、ユーザーに Azure Key Vault に対するシークレット取得アクセス許可を付与する必要があります。

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Azure Key Vault からシークレットを取得するには、**TokenLibrary.getSecret()** 関数を使用します。

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>次のステップ

- [専用 SQL プールへの書き込み](./synapse-spark-sql-pool-import-export.md)
