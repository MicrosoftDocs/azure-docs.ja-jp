---
title: Azure Monitor Application Insights for Java のトラブルシューティング
description: Azure Monitor Application Insights の Java エージェントのトラブルシューティング方法について説明します
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: f971466f25c2b7a4bd28e5b7eec6268f1b2e8b3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225576"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>トラブルシューティング ガイド:Azure Monitor Application Insights for Java

この記事では、Application Insights の Java エージェントを使用して Java アプリケーションをインストルメントする際に直面する可能性のある一般的な問題のいくつかを取り上げます。 また、これらの問題を解決する手順についても説明します。 Application Insights は、Azure Monitor プラットフォーム サービスの機能です。

## <a name="check-the-self-diagnostic-log-file"></a>自己診断ログ ファイルを確認する

既定では、Application Insights の Java 3.0 エージェントにより、`applicationinsights-agent-3.0.2.jar` ファイルが保持されているディレクトリに `applicationinsights.log` という名前のログ ファイルが生成されます。

このログ ファイルは、発生している問題に関するヒントを得るために最初に確認する場所です。

## <a name="jvm-fails-to-start"></a>JVM を起動できない

"zip ファイルを開くときのエラーまたは JAR マニフェストの欠落" が原因で JVM を起動できない場合は、エージェントの jar ファイルがファイル転送中に破損した可能性があるため、これを再ダウンロードしてみてください。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK からアップグレードする

アプリケーションで Application Insights Java 2.x SDK を既に使用している場合は、使用し続けてかまいません。 これは Java 3.0 エージェントによって検出されます。 詳細については、「[Application Insights Java 2.x SDK からのアップグレード](./java-standalone-upgrade-from-2x.md)」をご覧ください。

## <a name="upgrade-from-application-insights-java-30-preview"></a>Application Insights Java 3.0 Preview からアップグレードする

Java 3.0 Preview エージェントからアップグレードする場合は、すべての[構成オプション](./java-standalone-config.md)を注意深く確認してください。 JSON 構造体は、3.0 一般公開 (GA) リリースで完全に変更されています。

次のような変更です。

-  構成ファイル名自体が `ApplicationInsights.json` から `applicationinsights.json` に変更されました。
-  `instrumentationSettings` ノードがなくなりました。 `instrumentationSettings` のすべてのコンテンツがルート レベルに移動されました。 
-  `sampling`、`jmxMetrics`、`instrumentation`、`heartbeat` などの構成ノードは、`preview` からルート レベルに移動されました。

## <a name="some-logging-is-not-auto-collected"></a>一部のログ記録が自動収集されない

ログ記録は、ログ記録フレームワークに構成されているレベルを最初に満たし、次に Application Insights に構成されているレベルも満たす場合にのみキャプチャされます。

たとえば、パッケージ `com.example` から `WARN` (以上) をログに記録するようにログ記録フレームワークが構成されており、`INFO` (以上) をキャプチャするように Application Insights が構成されている場合、Application Insights では、パッケージ `com.example` から `WARN` (以上) しかキャプチャしません。

特定のログ記録ステートメントがログ記録フレームワークで構成されているしきい値を満たしているかどうかを確認する最善の方法は、通常のアプリケーション ログ (ファイルやコンソールなど) に表示されているかどうかを確認することです。

また、ロガーに例外オブジェクトが渡されると、Azure portal 内で `traces` テーブルではなく `exceptions` テーブルの下にログ メッセージ (および例外オブジェクトの詳細) が表示されることにも注意してください。

詳細については、[自動収集されたログ記録の構成](./java-standalone-config.md#auto-collected-logging)に関するページをご覧ください。

## <a name="import-ssl-certificates"></a>SSL 証明書をインポートする

このセクションは、Java エージェント使用時に、SSL 証明書に関連する例外をトラブルシューティングし、できる限り修正する場合に役立ちます。

この問題を解決するには、次の 2 つの異なるパスがあります。
* 既定の Java キーストアを使用している場合
* カスタム Java キーストアを使用している場合

どちらのパスに従えばよいかわからない場合は、JVM の引数 `-Djavax.net.ssl.trustStore=...` があるかどうかを調べます。
そのような JVM 引数が "_ない_" 場合は、おそらく既定の Java キーストアを使用しています。
そのような JVM 引数が "_ある_" 場合は、おそらくカスタム キーストアを使用しており、その JVM 引数でカスタム キーストアが参照されています。

### <a name="if-using-the-default-java-keystore"></a>既定の Java キーストアを使用している場合:

通常、既定の Java キーストアには、すべての CA ルート証明書が既にあります。 ただし、インジェスト エンドポイントの証明書が別のルート証明書によって署名されているなど、いくつかの例外が発生する可能性があります。 この問題を解決するために、次の 3 つの手順をお勧めします。

1.  Application Insights エンドポイントに署名するために使用されたルート証明書が、既定のキーストアに既に存在するかどうかを確認します。 既定では、信頼された CA 証明書は `$JAVA_HOME/jre/lib/security/cacerts` に格納されます。 Java キーストア内の証明書を一覧表示するには、次のコマンドを使用します。
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    このような一時ファイルに出力をリダイレクトできます (後で簡単に検索できます)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. 証明書の一覧を取得したら、これらの[手順](#steps-to-download-ssl-certificate)に従って、Application Insights エンドポイントへの署名に使用されたルート証明書をダウンロードします。

    証明書をダウンロードしたら、下のコマンドを使用して、証明書で SHA-1 ハッシュを生成します。
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    SHA-1 値をコピーし、前に保存した "temp.txt" ファイルにこの値があるかどうかを確認します。  一時ファイルに SHA-1 値が見つからない場合は、ダウンロードしたルート証明書が既定の Java キーストアに存在しないことを示しています。


3. 次のコマンドを使用して、ルート証明書を既定の Java キーストアにインポートします。
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    この例の場合、次のようになります
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>カスタム Java キーストアを使用している場合:

カスタム Java キーストアを使用している場合、Application Insights エンドポイントのルート SSL 証明書をそのキーストアにインポートする必要がある場合があります。
この問題を解決するために、次の 2 つの手順をお勧めします。
1. これらの[手順](#steps-to-download-ssl-certificate)に従って、Application Insights エンドポイントからルート証明書をダウンロードします。
2. 次のコマンドを使用して、ルート SSL 証明書をカスタム Java キーストアにインポートします。
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>SSL 証明書をダウンロードする手順

1.  任意のブラウザーを開き、アプリケーションのインストルメント化に使用する接続文字列に含まれる `IngestionEndpoint` URL にアクセスします。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Application Insights の接続文字列を示したスクリーンショット。" lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  ブラウザーの **[サイト情報を表示]** (鍵) アイコンを選択し、 **[証明書]** オプションを選択します。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="サイト情報の証明書オプションのスクリーンショット。" lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  "リーフ" 証明書をダウンロードするのではなく、下のように "ルート" 証明書をダウンロードする必要があります。 後で、[証明書パス] をクリックし、ルート証明書を選択し、[証明書の表示] をクリックする必要があります。 これにより、[新しい証明書] メニューがポップアップ表示され、[新規] メニューから証明書をダウンロードできます。

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="ルート証明書を選択する方法のスクリーンショット。" lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  **[詳細]** タブにアクセスし、 **[ファイルへコピー]** を選択します。
5.  **[次へ]** ボタンをクリックし、"**Base-64 encoded X.509 (.CER)** " 形式を選択してから **[次へ]** を選択します。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="形式が選択された、証明書のエクスポート ウィザードのスクリーンショット。" lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  SSL 証明書の保存先ファイルを指定します。 次に、 **[次へ]**  >  **[完了]** の順に選択します。 "エクスポートに成功しました" というメッセージが表示されます。

> [!WARNING]
> 現在の証明書の有効期限が切れる前に新しい証明書を取得するには、これらの手順を繰り返す必要があります。 有効期限の情報は、 **[証明書]** ダイアログ ボックスの **[詳細]** タブで確認できます。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 証明書の詳細を示すスクリーンショット。" lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
