---
title: SSL/TLS 接続 - Azure Database for MariaDB
description: SSL 接続を適切に使用できるように、Azure Database for MariaDB と関連アプリケーションを構成するための情報
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 6c4ee8c6d12a6350c319e40bbe30046af4932311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550642"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での SSL/TLS 接続
Azure Database for MariaDB では、Secure Sockets Layer (SSL) を使用して、データベース サーバーをクライアント アプリケーションに接続できます。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

>[!NOTE]
> お客様からのフィードバックに基づいて、既存の Baltimore Root CA のルート証明書の非推奨化を、2021 年 2 月 15 日 (02/15/2021) まで延長しました。

> [!IMPORTANT] 
> SSL ルート証明書は、2021 年 2 月 15 日 (02/15/2021) から期限切れになるように設定されています。 [新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)が使用されるようにアプリケーションを更新してください。 詳細については、[証明書の計画的な更新](concepts-certificate-rotation.md)に関するページをご覧ください

## <a name="default-settings"></a>既定の設定
既定では、MariaDB サーバーへの接続時に SSL 接続が要求されるようにデータベース サービスを構成する必要があります。  可能な場合は、SSL オプションを無効にしないことをお勧めします。

Azure portal や CLI を使用して新しい Azure Database for MariaDB サーバーをプロビジョニングする場合、SSL 接続の適用が既定で有効になります。

安全に接続するために、信頼された証明機関 (CA) 証明書ファイルから生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 ユーザーが https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem にある Azure Database for MariaDB サーバーに接続するには、現在、定義済みの証明書 **のみを使用** できます。 

同様に、次のリンクは、ソブリン クラウド内のサーバーの証明書を指しています。[Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)、[Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)、[Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) の各リンクを参照してください。

さまざまなプログラミング言語の接続文字列が Azure Portal に表示されます。 これらの接続文字列には、データベースへの接続に必要な SSL パラメーターが含まれます。 Azure Portal で、お使いのサーバーを選択します。 **[設定]** の見出しにある **[接続文字列]** を選択します。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

アプリケーションの開発時に SSL 接続を有効または無効にする方法については、[SSL の構成方法](howto-configure-ssl.md)に関するページをご覧ください。

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での TLS の適用

Azure Database for MariaDB では、トランスポート層セキュリティ (TLS) を使用してデータベース サーバーに接続するクライアントの暗号化をサポートしています。 TLS は、データベース サーバーとクライアント アプリケーションとの間のセキュリティで保護されたネットワーク接続を確保する業界標準のプロトコルであり、コンプライアンス要件への準拠を可能にします。

### <a name="tls-settings"></a>TLS の設定

Azure Database for MariaDB には、クライアント接続に TLS バージョンを適用する機能が用意されています。 TLS バージョンを適用するには、 **[TLS の最小バージョン]** オプション設定を使用します。 このオプション設定には次の値を使用できます。

|  TLS の最小設定             | サポートされているクライアント TLS バージョン                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (既定値) | TLS は不要                      |
| TLS1_0                           | Tls 1.0、TLS 1.1、TLS 1.2 以降         |
| TLS1_1                           | TLS 1.1、TLS 1.2 以降              |
| TLS1_2                           | TLS バージョン 1.2 以降                  |


たとえば、[Minimum TLS setting version]\(TLS の最小設定バージョン\) の値を TLS 1.0 に設定した場合、サーバーは TLS 1.0、1.1、および 1.2+ を使用しているクライアントからの接続を許可することになります。 また、これを 1.2 に設定すると、TLS 1.2+ を使用するクライアントからの接続だけが許可され、TLS 1.0 および TLS 1.1 を使用した接続はすべて拒否されます。

> [!Note] 
> 既定では、Azure Database for MariaDB ではTLS の最小バージョン (設定 `TLSEnforcementDisabled`) は強制されません。
>
> TLS の最小バージョンを強制すると、後で最小バージョンの強制を無効にすることはできません。

Azure Database for MariaDB の TLS 設定を行う方法については、[TLS 設定の構成方法](howto-tls-configurations.md)に関するページを参照してください。

## <a name="cipher-support-by-azure-database-for-mariadb"></a>Azure Database for MariaDB でサポートされる暗号

SSL/TLS 通信の一部として、暗号スイートが検証され、サポートされている暗号スイートのみがデータベース サーバーとの通信を許可されます。 暗号スイートの検証は、[ゲートウェイ レイヤー](concepts-connectivity-architecture.md#connectivity-architecture)で制御され、ノード自体では明示的には行われません。 暗号スイートが以下の一覧に示されているスイートのいずれかと一致しない場合、受信クライアント接続は拒否されます。

### <a name="cipher-suite-supported"></a>サポートされている暗号スイート

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>次のステップ
- [サーバー ファイアウォール規則](concepts-firewall-rules.md)について説明します。
- [SSL の構成](howto-configure-ssl.md)方法について
- [TLS の構成](howto-tls-configurations.md)方法について
