---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944348"
---
## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) バージョン 2.7.1846 以降の 2.x バージョン。
::: zone-end  
::: zone pivot="programming-language-python"
+ インストール済みの Python バージョンに対応する Azure Functions Core Tools バージョン。

   | Python バージョン | Core Tools バージョン |
   | -------------- | ------------------ |
   | Python 3.8     | [バージョン 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [バージョン 2.7.1846 以降](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.4 以降。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 ビット)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 ビット)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 ビット)](https://www.python.org/downloads/release/python-368/)。これらは Azure Functions でサポートされています。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks)、バージョン 8。 

    > [!IMPORTANT]
    > + Java 11 に対する Functions のサポートは現在プレビューの段階にあり、Maven アーキタイプでは既定で Java 8 デプロイが作成されます。 代わりに、Java 11 で関数アプリを実行する場合は、Java 11 の値を使用して pom.xml ファイルを手動で更新する必要があります。 詳細については、「[Java のバージョン](../articles/azure-functions/functions-reference-java.md#java-versions)」を参照してください。 
    > + このクイックスタートを行うには、`JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。

+ [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

::: zone-end
