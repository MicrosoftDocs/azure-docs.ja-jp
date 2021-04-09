---
title: Azure Database for PostgreSQL シングル サーバーでの証明書のローテーション
description: Azure Database for PostgreSQL シングル サーバーに影響するルート証明書の今後の変更について説明します
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123991"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL シングル サーバーのルート CA の変更について

Azure Database for PostgreSQL シングル サーバーにより、標準のメンテナンスとセキュリティのベスト プラクティスの一環として、**2021 年 2 月 15 日 (2021/02/15)** にルート証明書の変更が正常に完了しました。 この記事では、変更、影響を受けるリソース、アプリケーションでデータベース サーバーへの接続を確実に維持するために必要な手順について、詳しく説明します。

## <a name="why-root-certificate-update-is-required"></a>ルート証明書の更新が必要な理由

Azure Database for PostgreSQL のユーザーが PostgreSQL サーバーへの接続に使用できるのは、[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)にある定義済みの証明書のみです。 ただし、[証明機関 (CA) ブラウザー フォーラム](https://cabforum.org/)から最近、CA ベンダーによって発行された複数の証明書が準拠していないことを示すレポートが公開されました。

業界のコンプライアンス要件に従い、CA ベンダーは準拠していない CA の CA 証明書の取り消しを始めており、サーバーでは、準拠している CA で発行され、それらの準拠している CA からの CA 証明書によって署名された証明書が使用される必要があります。 Azure Database for MySQL にはこれらの準拠していない証明書の 1 つが使用されていたため、MySQL サーバーへの潜在的な脅威を最小限に抑えるために、証明書を準拠バージョンにローテーションする必要がありました。

新しい証明書は 2021 年 2 月 15 日 (2021/02/15) からロールアウトされ、有効になっています。 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021 年 2 月 15 日 (2021/02/15) にどのような変更が行われましたか?

2021 年 2 月 15 日、既存のお客様が何も変更する必要がなく、サーバーへの接続に影響が生じないように、[BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)が同じ [BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)の **準拠バージョン** に置き換えられました。 この変更時に、[BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)は [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) に **置き換えられませんでした**。この変更は、お客様が変更を行う時間を確保できるように、延期されました。

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>接続を維持するためにクライアントに変更を加える必要はありますか?

クライアント側での変更は必要ありません。 以下に示す以前の推奨事項に従った場合でも、結合された CA 証明書から **BaltimoreCyberTrustRoot 証明書が削除されない限り**、接続を継続できます。 **接続を維持するための通知があるまで、結合された CA 証明書から BaltimoreCyberTrustRoot を削除しないことをお勧めします。**

### <a name="previous-recommendation"></a>以前の推奨事項

*   以下のリンクから、BaltimoreCyberTrustRoot と DigiCertGlobalRootG2 のルート CA をダウンロードします。
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方の証明書が含まれる、結合された CA 証明書ストアを生成します。
    *   DefaultJavaSSLFactory を使用する Java (PostgreSQL JDBC) ユーザーの場合は、以下を実行します。

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          次に、元のキーストア ファイルを、新しく生成されたものに置き換えます。
        *   System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","password");
        
    *   Windows の .NET (Npgsql) ユーザーの場合は、**Baltimore CyberTrust Root** と **DigiCert Global Root G2** の両方が Windows 証明書ストアの信頼されたルート証明機関に存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書をインポートします。

        ![Azure Database for PostgreSQL の .net 証明書](media/overview/netconnecter-cert.png)

    *   SSL_CERT_DIR を使用する Linux 上の .NET (Npgsql) ユーザーの場合は、**BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方が SSL_CERT_DIR によって示されるディレクトリに存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書ファイルを作成します。

    *   その他の PostgreSQL クライアント ユーザーの場合は、次の形式のような 2 つの CA 証明書ファイルをマージできます

        </br>-----BEGIN CERTIFICATE-----
 </br>(ルート CA1: BaltimoreCyberTrustRoot.crt.pem)
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(ルート CA2: DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   元のルート CA pem ファイルを、結合されたルート CA ファイルに置き換えて、アプリケーションやクライアントを再起動します。
*    将来的に、サーバー側に新しい証明書がデプロイされた後は、CA pem ファイルを DigiCertGlobalRootG2.crt.pem に変更することができます。

> [!NOTE]
> 証明書の変更が行われるまで、**Baltimore 証明書** を破棄または変更しないでください。 変更が完了すると、Microsoft からメッセージが送信されます。その後、Baltimore 証明書を安全に削除できます。 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>2021 年 2 月 15 日のこの変更時に、BaltimoreCyberTrustRoot 証明書が DigiCertGlobalRootG2 に置き換えられなかったのはなぜですか?

この変更に対するお客様の準備状況を評価したところ、多くのお客様が、この変更の管理に追加のリード タイムを必要としていることがわかりました。 準備のためにお客様により多くのリード タイムを提供するという目的から、お客様とエンド ユーザーが十分なリード タイムを確保できるように証明書の変更を DigiCertGlobalRootG2 に少なくとも 1 年間延期することを決定しました。 

ユーザーへの推奨事項は、前述の手順を使用して結合証明書を作成し、サーバーに接続しますが、削除するように Microsoft からメッセージが送信されるまで BaltimoreCyberTrustRoot 証明書を削除しないことです。 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>BaltimoreCyberTrustRoot 証明書を削除した場合はどうなりますか?

Azure Database for PostgreSQL サーバーへの接続時に、接続エラーが発生し始めます。 接続を維持するには、[BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 証明書を使用してもう一度 SSL を構成する必要があります。


## <a name="frequently-asked-questions"></a>よく寄せられる質問

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.SSL/TLS を使用していない場合でも、ルート CA を更新する必要がありますか?
SSL/TLS を使用していない場合は、何も行う必要は必要ありません。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.SSL/TLS を使用している場合、ルート CA を更新するには、データベース サーバーを再起動する必要がありますか?
いいえ。新しい証明書を使い始めるために、データベース サーバーを再起動する必要はありません。 これはクライアント側の変更であり、確実にデータベース サーバーに接続できるよう、受信クライアント接続で新しい証明書を使用する必要があります。

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3.ルート証明書の検証で SSL/TLS を使用しているかどうかはどうすればわかりますか?

お使いの接続で、接続文字列を確認するとルート証明書が検証されているかどうかを、確認することができます。
-    接続文字列に `sslmode=verify-ca` または `sslmode=verify-full` が含まれている場合は、証明書を更新する必要があります。
-    接続文字列に `sslmode=disable`、`sslmode=allow`、`sslmode=prefer`、または `sslmode=require` が含まれている場合は、証明書を更新する必要はありません。 
-    接続文字列で sslmode が指定されていない場合は、証明書を更新する必要はありません。

接続文字列が抽象化されるクライアントを使用している場合、証明書が検証されているかどうかを確認するには、クライアントのドキュメントを参照してください。 PostgreSQL の sslmode を理解するには、PostgreSQL のドキュメントで [SSL モードの説明](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions)をご確認ください。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4.Azure Database for PostgreSQL で App Service を使用している場合、どのような影響がありますか?
Azure App Services で Azure Database for PostgreSQL に接続している場合、アプリケーションでの SSL の使用方法により、2 つのシナリオが考えられます。
*   この新しい証明書は、プラットフォーム レベルで App Service に追加されています。 App Service プラットフォームに含まれる SSL 証明書をアプリケーションで使用している場合は、何もする必要はありません。
*   SSL 証明書ファイルへのパスをコードに明示的に含めている場合は、新しい証明書をダウンロードし、新しい証明書を使用するようにコードを更新する必要があります。このシナリオの好例は、[App Service ドキュメント](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)で共有されている、App Service でカスタム コンテナーを使用する場合です。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5.Azure Database for PostgreSQL で Azure Kubernetes Services (AKS) を使用している場合、どのような影響がありますか?
Azure Kubernetes Services (AKS) を使用して Azure Database for PostgreSQL に接続しようとしている場合は、専用の顧客ホスト環境からのアクセスに似ています。 [こちら](../aks/ingress-own-tls.md)の手順を参照してください。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6.Azure Data Factory を使用して Azure Database for PostgreSQL に接続している場合、どのような影響がありますか?
Azure Integration Runtime を使用しているコネクタでは、Azure でホストされる環境内の Windows 証明書ストアの証明書が利用されています。 これらの証明書は、新しく適用される証明書と既に互換性があるため、何もする必要はありません。

接続文字列に SSL 証明書ファイルへのパスを明示的に含めるセルフホステッド統合ランタイムを使用しているコネクタでは、[新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)をダウンロードし、それを使用するように接続文字列を更新する必要があります。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.この変更では、データベース サーバーのメンテナンス ダウンタイムを計画する必要がありますか?
いいえ。 ここでの変更は、データベース サーバーに接続するためにクライアント側でのみ行うものなので、この変更のためにデータベース サーバーでメンテナンス ダウンタイムは必要ありません。

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8.2021 年 2 月 15 日 (2021/02/15) より後に新しいサーバーを作成する場合、影響はありますか?
2021 年 2 月 15 日 (2021/02/15) より後に作成されたサーバーの場合、SSL を使用して接続するアプリケーションには引き続き [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) が使用されます。

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9.Microsoft はどのくらいの頻度で証明書を更新しますか、または有効期限ポリシーはどのようなものですか?
Azure Database for PostgreSQL によって使用されるこれらの証明書は、信頼された証明機関 (CA) によって提供されます。 そのため、これらの証明書のサポートは、CA によるこれらの証明書のサポートに関連付けられています。 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 証明書は 2025 年に有効期限が切れる予定であるため、Microsoft は有効期限が切れる前に証明書の変更を実行する必要があります。 また、これらの事前定義された証明書に予期しないバグがある場合、Microsoft は、サービスが常に安全で準拠していることを確保するために、2021 年 2 月 15 日に実行された変更と同様に、できるだけ早く証明書のローテーションを行う必要があります。

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10。読み取りレプリカを使用している場合、この更新を行う必要があるのはプライマリ サーバーだけですか? または、読み取りレプリカでも必要ですか?
この更新はクライアント側の変更であるため、レプリカ サーバーからデータを読み取るためにクライアントを使用している場合は、それらのクライアントにも変更を適用する必要があります。 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11.SSL が使用されているかどうかを確認するためのサーバー側クエリはありますか?
サーバーへの接続に SSL 接続を使用しているかどうかを確認するには、[SSL の検証](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)に関する記事を参照してください。

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12.証明書ファイルに DigiCertGlobalRootG2 が既に含まれている場合、必要なアクションはありますか?
いいえ。 証明書ファイルに **DigiCertGlobalRootG2** が既に含まれている場合、必要なアクションはありません。

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13.Microsoft によって提供される PgBouncer サイドカーの docker イメージを使用している場合はどうすればよいですか?
[**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) と [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) の両方をサポートする新しい docker イメージは、[こちら](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar)に公開されます (最新のタグ)。 2021 年 2 月 15 日以降の接続の中断を回避するために、この新しいイメージをプルすることができます。 

### <a name="14-what-if-i-have-further-questions"></a>14. さらに質問がある場合はどうすればよいですか?
質問がある場合は、[Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) でコミュニティの専門家から回答を得ることができます。 サポート プランをお持ちで技術的なヘルプが必要な場合は、[こちらまでお問い合わせください](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
