---
title: Azure TLS 証明書の変更
description: Azure TLS 証明書の変更
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 9e4edbcfd9e4180e727cd885902d9f0150a967ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389401"
---
# <a name="azure-tls-certificate-changes"></a>Azure TLS 証明書の変更  

Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 この変更は、現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠しておらず、2021 年 2 月 15 日に失効するために行われています。

## <a name="when-will-this-change-happen"></a>この変更はいつ行われますか?

既存の Azure エンドポイントは、2020 年 8 月 13 日以降、段階的に移行されました。 新しく作成されるすべての Azure TLS/SSL エンドポイントには、新しいルート CA にチェーンする更新された証明書が含まれています。

すべての Azure サービスがこの変更の影響を受けます。 ここでは、特定のサービスに関する追加の詳細を示します。

- 2020 年 7 月 7 日に、[Azure Active Directory](../../active-directory/index.yml) (Azure AD) サービスでこの移行が開始されました。
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) と [DPS](../../iot-dps/index.yml) は Baltimore CyberTrust ルート CA に残りますが、中間 CA は変更されます。 詳細については、[こちら](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456)をクリックしてください。
- [Azure Storage](../../storage/index.yml) は Baltimore CyberTrust ルート CA に残りますが、中間 CA は変更されます。 詳細については、[こちら](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)をクリックしてください。
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) は Baltimore CyberTrust ルート CA に残りますが、中間 CA は変更されます。 詳細については、[こちら](../../azure-cache-for-redis/cache-whats-new.md)をクリックしてください。
- Azure Instance Metadata Service は Baltimore CyberTrust ルート CA に残りますが、中間 CA は変更されます。 詳細については、[こちら](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html)をクリックしてください。

> [!IMPORTANT]
> お客様は、Azure サービスに接続しようとしたときの接続エラーを防ぐため、この変更後にアプリケーションを更新することが必要になる場合があります。

## <a name="what-is-changing"></a>何が変わるのですか?

現在、Azure サービスで使用されている TLS 証明書のほとんどは、次のルート CA にチェーンされています。

| CA の共通名 | サムプリント (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure サービスによって使用される TLS 証明書は、次のいずれかのルート CA にチェーンされるようになります。

| CA の共通名 | サムプリント (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>古い中間サムプリントはいつ廃止できますか?

現在の CA 証明書は、2021 年 2 月 15 日まで取り消され "*ません*"。 その期日の後で、古いサムプリントをコードから削除できます。

この期日が変更された場合、新しい失効日が通知されます。

## <a name="will-this-change-affect-me"></a>この変更による影響はありますか? 

**Azure のほとんどのお客様には影響がない** ものと思われます。  ただし、受け入れ可能な CA のリストが明示的に指定されているアプリケーションでは、影響がある可能性があります。 この方法は、証明書のピン留めと呼ばれます。

アプリケーションが影響を受けるかどうかを検出するには、次の方法があります。

- ソース コードで、[こちら](https://www.microsoft.com/pki/mscorp/cps/default.htm)に記載されているいずれかの Microsoft IT TLS CA のサムプリント、共通名、および他の証明書プロパティを検索します。 一致するものがある場合、アプリケーションは影響を受けます。 この問題を解決するには、新しい CA を含むようにソース コードを更新します。 ベスト プラクティスとして、短期間で CA を追加または編集できるようにすることをお勧めします。 業界の規制では、CA 証明書を 7 日以内に交換する必要があるため、ピン留めを利用しているお客様は迅速に対応する必要があります。

- Azure API または他の Azure サービスと統合されているアプリケーションがあり、証明書のピン留めが使用されているかどうか不明な場合は、アプリケーション ベンダーに確認してください。

- Azure サービスと通信するさまざまなオペレーティング システムと言語ランタイムでは、新しいルートを使用して証明書チェーンを正しく構築するために追加の手順が必要になる場合があります。
    - **Linux**:多くのディストリビューションでは、CA を /etc/ssl/certs に追加する必要があります。 具体的な手順については、ディストリビューションのドキュメントを参照してください。
    - **Java**: Java キース トアに上記の CA が含まれていることを確認します。
    - **切断された環境で実行されている Windows**: 切断された環境で実行されているシステムでは、新しいルートを信頼されたルート証明機関ストアに追加し、中間を中間証明機関ストアに追加する必要があります。
    - **Android**: デバイスのドキュメントと Android のバージョンを確認してください。
    - **その他のハードウェア デバイス、特に IoT**: デバイスの製造元に問い合わせてください。

- 特定の証明書失効リスト (CRL) のダウンロードやオンライン証明書状態プロトコル (OCSP) の検証場所のみに対する発信呼び出しが許可されるようにファイアウォール規則が設定されている環境の場合。 次の CRL と OCSP の URL を許可する必要があります。

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## <a name="next-steps"></a>次の手順

その他の質問がある場合は、[サポート](https://azure.microsoft.com/support/options/)を通じてご連絡ください。
