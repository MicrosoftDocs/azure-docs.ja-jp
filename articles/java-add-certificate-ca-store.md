---
title: "Java CA ストアへの証明書の追加 | Microsoft Docs"
description: "Twilio サービスまたは Azure Service Bus に必要な証明機関 (CA) の証明書を Java CA 証明書 (cacerts) ストアに追加する方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a967a522ebbdce57fa5b4f938369ddd88253701c


---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>証明書を Java CA 証明書ストアに追加する方法
次のステップでは、証明機関 (CA) の証明書を Java CA 証明書 (cacerts) ストアに追加する方法について説明します。 例として使用するのは Twilio サービスに必要な CA 証明書です。 このトピックの後半では、Azure Service Bus 用の CA 証明書をインストールする方法について説明します。 

JDK を圧縮して Azure プロジェクトの **approot** フォルダーに追加する前に、keytool を使用して CA 証明書を追加できます。または、keytool を使用して証明書を追加する Azure スタートアップ タスクを実行することもできます。 この例では、JDK を圧縮する前に CA 証明書を追加するとします。 また、特定の CA 証明書をこの例で使用しますが、別の CA 証明書を取得して使用する場合でも、証明書を cacerts ストアにインポートする手順は同様です。

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>証明書を cacerts ストアに追加するには
1. JDK の **jdk\jre\lib\security** フォルダーに設定されたコマンド プロンプトで、次のコマンドを実行して、インストールされている証明書を確認します。
   
    `keytool -list -keystore cacerts`
   
    ストアのパスワードを入力するように求められます。 既定のパスワードは **changeit** です  (パスワードを変更する場合は、<http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html> にある keytool のドキュメントを参照してください)。この例では、MD5 フィンガープリントが 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 の証明書が一覧に表示されないため、その証明書をインポートするとします (この証明書は Twilio API サービスに必要)。
2. [GeoTrust Root Certificates](http://www.geotrust.com/resources/root-certificates/)に掲載されている証明書の一覧から証明書を入手します。 シリアル番号が 35:DE:F4:CF の証明書のリンクを右クリックし、その証明書を **jdk\jre\lib\security** フォルダーに保存します。 この例では、**Equifax\_Secure\_Certificate\_Authority.cer** という名前のファイルに保存しました。
3. 次のコマンドを使用して証明書をインポートします。
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    この証明書を信頼するかどうかをたずねられたら、証明書の MD5 フィンガープリントが 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 であれば、「 **y**」と入力して応答します。
4. 次のコマンドを実行して CA 証明書が正常にインポートされたことを確認します。
   
    `keytool -list -keystore cacerts`
5. JDK を圧縮し、Azure プロジェクトの **approot** フォルダーに追加します。

keytool については、<http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html> を参照してください。

## <a name="azure-root-certificates"></a>Azure のルート証明書
Azure のサービス (Azure Service Bus など) を使用するアプリケーションでは、Baltimore CyberTrust Root 証明書を信頼する必要があります (2013 年 4 月 15 日より、Azure は GTE CyberTrust Global Root から Baltimore CyberTrust Root への移行を開始しました。 移行は数か月かけて完了しました)。

Baltimore 証明書が cacerts ストアに既にインストールされている場合があるため、まず **keytool -list** コマンドを実行して、それらの証明書が既に存在するかどうかを確認することを忘れないでください。

Baltimore CyberTrust Root を追加する必要がある場合、そのシリアル番号は ‎02:00:00:b9、SHA1 フィンガープリントは d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74 です。 上記のように、これは <https://cacert.omniroot.com/bc2025.crt> からダウンロードし、拡張子が **.cer** のローカル ファイルに保存して、**keytool** を使用してインポートできます。

## <a name="next-steps"></a>次のステップ
Azure で使用するルート証明書の詳細については、 [Azure ルート証明書の移行](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)に関するページを参照してください。

Java の詳細については、 [Java デベロッパー センター](/develop/java/)を参照してください。




<!--HONumber=Nov16_HO3-->


