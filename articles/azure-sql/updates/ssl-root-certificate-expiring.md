---
title: Azure SQL Database と SQL Managed Instance のための証明書ローテーション
description: Azure SQL Database と SQL Managed Instance に影響を与えるルート証明書の変更に関する今後の変更点について説明します
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590853"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance のルート CA の変更における変更点について

Azure SQL Database と SQL Managed Instance では、TDS 接続を確立するために使用される、SSL で有効にされるクライアント アプリケーション/ドライバー用のルート証明書が変更される予定です。 [現在のルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)は、標準メンテナンスおよびセキュリティのベスト プラクティスの一環で、2020 年 10 月 26 日に有効期限が切れることになっています。 この記事では、予定されている変更、影響を受けるリソース、アプリケーションでデータベース サーバーへの接続を確実に維持するために必要な手順について、詳しく説明します。

## <a name="what-update-is-going-to-happen"></a>予定されている更新

最近、[証明機関 (CA) ブラウザー フォーラム](https://cabforum.org/)から、CA ベンダーによって発行された複数の証明書が準拠していないことを示すレポートが公開されました。

業界のコンプライアンス要件に従い、CA ベンダーは準拠していない CA の CA 証明書の取り消しを始めており、サーバーでは、準拠している CA で発行され、それらの準拠している CA からの CA 証明書によって署名された証明書が使用される必要があります。 現在、Azure SQL Database と SQL Managed Instance では、クライアント アプリケーションが SSL 接続を検証するために使用している、これらの非準拠の証明書のいずれかが使用されているため、Azure SQL サーバーへの潜在的な影響を最小限にするため、確実に適切なアクションが行われるようにする必要があります (後述)。

新しい証明書は、2020 年 10 月 26 日から使用される予定です。 SQL クライアントからの接続時にサーバー証明書の完全な検証を使用する (TrustServerCertificate = true) 場合は、2020 年 10 月 26 日より前に、SQL クライアントが新しいルート証明書を検証できることを確認する必要があります。

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>アプリケーションが影響を受けるかどうかを確認する方法

Azure SQL Database と SQL Managed Instance に接続するためには、SSL/TLS を使用し、ルート証明書を検証しているすべてのアプリケーションでルート証明書を更新する必要があります。 

SSL/TLS を現在使用していない場合、アプリケーションの可用性に影響はありません。 お使いのクライアント アプリケーションが、ルート証明書を検証しようとしているかどうかを確認できます。それには、接続文字列を確認します。 TrustServerCertificate が明示的に true に設定されていれば、影響を受けません。

大多数のドライバーでそうであるように、クライアント ドライバーが OS 証明書ストアを利用しており、OS のメンテナンスが定期的に行われている場合、この変更の影響はおそらくありません。切り替え後のルート証明書は、お客様の信頼されたルート証明書ストアで既に使用可能になっているためです。 Baltimore CyberDigiCert GlobalRoot G2 を調べて、それが存在することを確認してください。

クライアント ドライバーがローカルのファイル証明書ストアを利用している場合は、証明書の予期しない取り消しによってアプリケーションの可用性が中断されないようにするため、または取り消された証明書を更新するため、「[**接続を維持するために必要な作業**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)」セクションを参照してください。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>接続を維持するために必要な作業

証明書の予期しない取り消しによってアプリケーションの可用性が中断されないようにするため、または取り消された証明書を更新するため、以下の手順に従います。

*   下記のリンクから、Baltimore CyberTrust Root と DigiCert GlobalRoot G2 のルート CA をダウンロードします。
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方の証明書が含まれる、結合された CA 証明書ストアを生成します。

## <a name="what-can-be-the-impact"></a>どのような影響が考えられるか
ここで説明したようにサーバー証明書を検証しようとしている場合は、データベースに到達できなくなるため、アプリケーションの可用性が中断される可能性があります。 アプリケーションによっては、次のようなさまざまなエラー メッセージが表示される場合があります。
*   無効な証明書/失効した証明書
*   接続がタイムアウトしました
*   該当する場合はエラー

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>SSL/TLS を使用していない場合でも、ルート CA を更新する必要がありますか?
SSL/TLS を使用していない場合は、この変更に関して何も行う必要はありません。 それでも、Microsoft では近い将来の TLS の適用を計画しているため、最新の TLS バージョンの使用を開始するための計画を定めてください。

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>2020 年 10 月 26 日までにルート証明書を更新しないとどうなりますか?
2020 年 11 月 30 日までにルート証明書を更新しないと、SSL/TLS 経由で接続し、ルート証明書の検証を行うアプリケーションは、Azure SQL Database および SQL Managed Instance と通信できなくなり、アプリケーションで、Azure SQL Database と SQL Managed Instance への接続の問題が発生します。

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>この変更のためにメンテナンスのダウンタイムを計画する必要がありますか?<BR>
いいえ。 ここでの変更は、サーバーに接続するクライアント側に対するものなので、この変更のためにメンテナンスのダウンタイムは必要ありません。

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>2020 年 10 月 26 日より前に、この変更のためのダウンタイムをスケジュールできない場合はどうすればよいですか?
修正に関する[こちら](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)のセクションで説明されているように、サーバーへの接続に使用されるクライアントで証明書情報を更新する必要があるため、この場合、サーバーのダウンタイムは必要ありません。

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>2020 年 11 月 30 日より後に新しいサーバーを作成する場合、影響はありますか?
2020 年 10 月 26 日より後に作成されるサーバーでは、アプリケーションが SSL を使用して接続するために、新しく発行された証明書を使用できます。

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft はどのくらいの頻度で証明書を更新しますか、または有効期限ポリシーはどのようなものですか?
Azure SQL Database と SQL Managed Instance によって使用されるこれらの証明書は、信頼された証明機関 (CA) によって提供されます。 そのため、Azure SQL Database と SQL Managed Instance でのこれらの証明書のサポートは、CA によるこれらの証明書のサポートに結び付いています。 ただし、今回のケースのように、事前に定義されたこれらの証明書に予期しないバグが存在する可能性があり、その場合はできるだけ早く修正する必要があります。

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>読み取りレプリカを使用している場合、この更新を行う必要があるのはプライマリ サーバーだけですか? または、すべての読み取りレプリカでも必要ですか?
この更新はクライアント側の変更であるため、レプリカ サーバーからデータを読み取るために使用されていたクライアントの場合、それらのクライアントにも変更を適用する必要があります。 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>SSL が使用されているかどうかを確認するためのサーバー側クエリはありますか?
この構成はクライアント側であるため、サーバー側では情報を使用できません。

### <a name="what-if-i-have-further-questions"></a>さらに質問がある場合はどうすればよいですか?
サポート プランをご利用で、技術的支援が必要な場合は、[Azure サポート リクエストを作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)に関するページを参照し、Azure サポート リクエストを作成してください。