---
title: Azure Cosmos DB Emulator 証明書をエクスポートする
description: Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書をエクスポートする方法について説明します。 証明書をエクスポートし、Windows 証明書ストアを使用しない言語およびランタイム環境で使用する必要があります。
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554005"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Emulator では、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 Azure Cosmos DB エミュレーターでは、TLS 接続経由のセキュリティで保護された通信のみがサポートされます。

Azure Cosmos DB ローカル エミュレーターの証明書は、エミュレーターを初めて実行したときに生成されます。 証明書は 2 つあります。 その 1 つはローカル エミュレーターへの接続に使用され、もう 1 つはエミュレーター内のエミュレーター データの既定の暗号化を管理するために使用されます。 エクスポートする証明書は、"DocumentDBEmulatorCertificate" というフレンドリ名の接続証明書です。

エミュレーターを使用して Java、Python、Node.js などのさまざまな言語でアプリを開発する場合は、エミュレーター証明書をエクスポートして、必要な証明書ストアにインポートする必要があります。

.NET 言語とランタイムは、アプリケーションが Windows OS ホスト上で実行されている場合、Windows 証明書ストアを使用して Azure Cosmos DB ローカル エミュレーターに安全に接続されます。 その他の言語では、証明書の管理と使用について独自の方法があります。 たとえば、Java には独自の[証明書ストア](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)が使用され、Python には[ソケット ラッパー](https://docs.python.org/2/library/ssl.html)が使用され、Node.js には [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) が使用されます。

この記事では、Windows 証明書ストアと統合されていないさまざまな言語およびランタイム環境で使用できるように TLS/SSL 証明書をエクスポートする方法について説明します。 このエミュレーターの詳細については、「[開発とテストでの Azure Cosmos DB Emulator の使用](./local-emulator.md)」をご覧ください。

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Azure Cosmos DB TLS/SSL 証明書をエクスポートする

Windows 証明書ストアと統合されていない言語およびランタイム環境からエミュレーター エンドポイントを正常に使用するには、エミュレーター証明書をエクスポートする必要があります。 Windows 証明書マネージャーを使用して証明書をエクスポートできます。 次の手順に従って、"DocumentDBEmulatorCertificate" 証明書を BASE-64 でエンコードされた X.509 (.cer) ファイルとしてエクスポートします。

1. certlm.msc を実行して Windows 証明書マネージャーを起動します。次に、Personal フォルダー、Certificates フォルダーの順に移動して、**DocumentDbEmulatorCertificate** というフレンドリ名の証明書を開きます。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 1":::

1. **[詳細]** 、 **[OK]** の順にクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 2":::

1. **[ファイルへコピー...]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 3":::

1. **[次へ]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 4":::

1. **[No, do not export private key (いいえ、秘密キーをエクスポートしません)]** をクリックしてから、 **[次へ]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 5":::

1. **[Base 64 encoded X.509 (.CER)]** をクリックしてから、 **[次へ]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 6":::

1. 証明書に名前を付けます。 ここでは「**documentdbemulatorcert**」と入力し、 **[次へ]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 7":::

1. **[完了]** をクリックします。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB ローカル エミュレーターのエクスポート手順 8":::

## <a name="use-the-certificate-with-java-apps"></a>Java アプリで証明書を使用する

Java ベースのクライアントが使用される Java アプリケーションまたは MongoDB アプリケーションを実行している場合は、`-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` フラグを渡すよりも、既定の Java 証明書ストアに証明書をインストールする方が簡単です。 たとえば、付属の Java デモ アプリケーション (`https://localhost:8081/_explorer/index.html`) では、既定の証明書ストアを使用します。

X.509 証明書を既定の Java 証明書ストアにインポートする場合は、[証明書を Java 証明書ストアに追加する方法](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm)に関するページの手順に従ってください。 keytool を実行する際には *%JAVA_HOME%* ディレクトリで作業することに注意してください。 証明書を証明書ストアにインポートすると、SQL API および Azure Cosmos DB の MongoDB 用 API のクライアントは、Azure Cosmos DB Emulator に接続できるようになります。

また、次の bash スクリプトを実行して証明書をインポートすることもできます。

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

"CosmosDBエミュレーターCertificate" TLS/SSL 証明書がインストールされると、アプリケーションはローカルの Azure Cosmos DB エミュレーターに接続し、それを使用できるようになります。 問題が発生した場合は、[SSL/TLS 接続のデバッグ](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)に関する記事を参照してください。 ほとんどの場合、証明書は *%JAVA_HOME%/jre/lib/security/cacerts* ストアにインストールされないことがあります。 たとえば、複数のバージョンの Java をインストールしてある場合、更新したストアとは異なる cacerts ストアがアプリケーションによって使用されている可能性があります。

## <a name="use-the-certificate-with-python-apps"></a>Python アプリで証明書を使用する

Python アプリからエミュレーターに接続すると、TLS 検証が無効になります。 既定では、SQL API 用の [Python SDK (バージョン 2.0.0 以降)](sql-api-sdk-python.md) は、ローカル エミュレーターに接続するときに TLS/SSL 証明書を使用しようとしません。 ただし、TLS 検証を使用したい場合は、[Python ソケット ラッパー](https://docs.python.org/2/library/ssl.html) のドキュメントの例に従うことができます。

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js で証明書を使用する方法

Node.js SDK からエミュレーターに接続すると、TLS 検証が無効になります。 既定では、SQL API 用の [Node.js SDK (バージョン 1.10.1 以降)](sql-api-sdk-node.md) は、ローカル エミュレーターに接続するときに TLS/SSL 証明書を使用しようとしません。 ただし、TLS 検証を使用したい場合は、[Node.js のドキュメント](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)の例に従うことができます。

## <a name="rotate-emulator-certificates"></a>エミュレーターの証明書のローテーション

Windows トレイで実行されている Azure Cosmos DB エミュレーターから **[データのリセット]** を選択すると、エミュレーター証明書を強制的に再生成することができます。 この操作を実行すると、エミュレーターによってローカルに保存されたすべてのデータも消去されることに注意してください。

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB ローカル エミュレーターの [データのリセット]":::

証明書を Java 証明書ストアにインストールした場合、または他の場所で使用した場合は、現在の証明書を使用して証明書を再インポートする必要があります。 証明書を更新するまで、アプリケーションはローカル エミュレーターに接続できません。

## <a name="next-steps"></a>次のステップ

* [コマンド ライン パラメーターと PowerShell コマンドを使用してエミュレーターを制御する](emulator-command-line-parameters.md)
* [エミュレーターに関する問題をデバッグする](troubleshoot-local-emulator.md)
