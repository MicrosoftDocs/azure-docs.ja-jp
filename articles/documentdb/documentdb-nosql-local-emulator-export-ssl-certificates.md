---
title: "DocumentDB Emulator 証明書のエクスポート | Microsoft Docs"
description: "Windows 証明書ストアを使用しない言語とランタイムで開発を行う場合、SSL 証明書をエクスポートして管理する必要があります。 この記事では詳しい手順について説明します。"
services: documentdb
documentationcenter: 
keywords: DocumentDB Emulator
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 5f2fd8810041d47be2ef0b21e1487426a643d541
ms.lasthandoff: 03/07/2017


---

# <a name="export-the-documentdb-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java、Python、および Node.js で使用する DocumentDB Emulator 証明書のエクスポート

[**Emulator をダウンロードする**](https://aka.ms/documentdb-emulator)

Azure DocumentDB Emulator では、SSL 接続の使用など、Azure DocumentDB サービスを開発用にエミュレートするローカル環境を使用できます。 この記事では、独自の[証明書ストア](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)を使用する Java、[ソケット ラッパー](https://docs.python.org/2/library/ssl.html)を使用する Python、[tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) を使用する Node.js など、Windows 証明書ストアと統合されていない言語とランタイムで使用するために、SSL 証明書をエクスポートする方法について説明します。 このエミュレーターの詳細については、「[開発とテストでの Azure DocumentDB Emulator の使用](./documentdb-nosql-local-emulator.md)」をご覧ください。

## <a name="certification-rotation"></a>証明書のローテーション

DocumentDB ローカル エミュレーターの証明書は、エミュレーターを初めて実行したときに生成されます。 証明書は&2; つあります。 1 つはローカル エミュレーターに接続するために使用し、もう&1; つはエミュレーター内のシークレットを管理するために使用します。 エクスポートする証明書は、"DocumentDBEmulatorCertificate" というフレンドリ名の接続証明書です。

どちらの証明書も、Windows トレイで実行されている DocumentDB エミュレーターから、次に示す **[データのリセット]** をクリックすることで生成し直すことができます。 証明書を Java 証明書ストアにインストールしたり、その他の場所で使用したりしていた場合に、証明書を生成し直したときは、元の証明書を更新する必要があります。更新を行わないと、アプリケーションはローカル エミュレーターに接続しなくなります。

![DocumentDB ローカル エミュレーターのデータのリセット](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>DocumentDB SSL 証明書をエクスポートする方法

1. certlm.msc を実行して Windows 証明書マネージャーを起動します。次に、Personal フォルダー、Certificates フォルダーの順に移動して、**DocumentDBEmulatorCertificate** というフレンドリ名の証明書を開きます。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 1.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. **[詳細]**、**[OK]** の順にクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 2.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. **[ファイルへコピー...]** をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 3.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. **[次へ]**をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 4.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. **[No, do not export private key (いいえ、秘密キーをエクスポートしません)]** をクリックしてから、**[次へ]** をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 5.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. **[Base&64; encoded X.509 (.CER)]** をクリックしてから、**[次へ]** をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 6.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. 証明書に名前を付けます。 ここでは「**documentdbemulatorcert**」と入力し、**[次へ]** をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 7.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. **[完了]**をクリックします。

    ![DocumentDB ローカル エミュレーターのエクスポート手順 8.](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Java で証明書を使用する方法

Java クライアントが使用される Java アプリケーションまたは MongoDB アプリケーションを実行している場合は、"-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>" フラグを渡すよりも、既定の Java 証明書ストアに証明書をインストールする方が簡単です。 たとえば、付属の [Java デモ アプリケーション](https://localhost:8081/_explorer/index.html)では、既定の証明書ストアを使用します。

X.509 証明書を既定の Java 証明書ストアにインポートする場合は、「[証明書を Java CA 証明書ストアに追加する方法](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)」の手順に従ってください。 keytool を実行する際には %JAVA_HOME% ディレクトリで作業することに注意してください。

"DocumentDBEmulatorCertificate" SSL 証明書をインストールしたら、アプリケーションは、DocumentDB ローカル エミュレーターに接続してそれを使用できるようになります。 依然として問題がある場合は、記事「[Debugging SSL/TLS Connections (SSL/TLS 接続のデバッグ)](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)」に従ってください。 多くの場合、証明書が %JAVA_HOME%/jre/lib/security/cacerts ストアにインストールされていません。 たとえば、複数のバージョンの Java をインストールしてある場合、更新したストアとは異なる cacerts ストアがアプリケーションによって使用されている可能性があります。

## <a name="how-to-use-the-certificate-in-python"></a>Python で証明書を使用する方法

[Python SDK (バージョン 2.0.0 以降)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) for DocumentDB の場合、既定ではローカル エミュレーターに接続する際に SSL 証明書を使用しません。 SSL 検証を使用したい場合は、[Python ソケット ラッパー](https://docs.python.org/2/library/ssl.html)に関するドキュメントの例に従ってください。

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js で証明書を使用する方法

[Node.js SDK (バージョン 1.10.1 以降)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) for DocumentDB の場合、既定ではローカル エミュレーターに接続する際に SSL 証明書を使用しません。 SSL 検証を使用する場合は、[Node.js のドキュメント](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)の例に従ってください。

## <a name="next-steps"></a>次のステップ
* DocumentDB の詳細については、[Azure DocumentDB の概要](documentdb-introduction.md)に関する記事を参照してください。
* DocumentDB Emulator に対する開発を開始するには、[サポートされている DocumentDB SDK](documentdb-sdk-dotnet.md) のいずれかをダウンロードしてください。

