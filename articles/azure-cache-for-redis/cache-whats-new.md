---
title: Azure Cache for Redis の新機能
description: Azure Cache for Redis の最近の更新
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91491708"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis の新機能

## <a name="azure-tls-certificate-change"></a>Azure TLS 証明書の変更

Microsoft では、異なる証明機関 (CA) のセットからの TLS サーバー証明書を使用するように、Azure サービスが更新されています。 この変更は、2020 年 8 月 13 日から 2020 年 10 月 26 日 (推定) のフェーズでロール アウトされます。 Azure では、現在の CA 証明書が [CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していない](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)ため、この変更が行われます。 この問題は 2020 年 7 月 1 日に報告され、世界中の複数の一般的な公開キー基盤 (PKI) プロバイダーに適用されます。 現在、Azure サービスで使用されている TLS 証明書の多くは、*Baltimore CyberTrust Root* PKI に由来します。 Azure Cache for Redis サービスは、引き続き Baltimore CyberTrust Root に結び付けられます。 これは TLS サーバー証明書ですが、TLS サーバー証明書は、2020 年 10 月 12 日以降、新しい中間証明機関 (ICA) によって発行されます。

> [!NOTE]
> この変更は、パブリック [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)のサービスに限定されます。 ソブリン (中国など) や政府のクラウドは除外されます。
>
>

### <a name="does-this-change-affect-me"></a>この変更は私に影響しますか?

ほとんどの Azure Cache for Redis のお客様は、この変更の影響を受けないと想定されます。 ご利用のアプリケーションが、受け入れ可能な証明書のリストを明示的に指定する場合 ("証明書のピン留め" と呼ばれる実践方法)、そのアプリケーションは影響を受ける可能性があります。 Baltimore CyberTrust Root の代わりに中間証明書またはリーフ証明書をピン留めしている場合、証明書の構成を変更するには、**直ちに対応する** 必要があります。

次の表に、ロール アウトされる証明書に関する情報を示します。 アプリケーションが使用する証明書によっては、Azure Cache for Redis インスタンスへの接続が失われないように、アプリケーションを更新する必要がある場合があります。

| CA の種類 | Current | ローリング後 (2020 年 10 月 12 日) | アクション |
| ----- | ----- | ----- | ----- |
| Root | 拇印: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 有効期限: 2025 年 5 月 12 日月曜日、午後 4:59:00<br><br> 件名:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | 変更なし | None |
| 中間 | 拇印:<br> CN = Microsoft IT TLS CA 1<br> Thumbprint:417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Thumbprint:54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Thumbprint:8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Thumbprint:Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 有効期限: ‎2024 年 ‎5 月 ‎20 日‎金曜日、午前 5:52:38<br><br> 件名:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | 拇印:<br> CN = Microsoft RSA TLS CA 01<br> Thumbprint:703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 拇印: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 有効期限: ‎2024 年 ‎10 月 ‎8 日火曜日、‎午前 12:00:00;<br><br> 件名:<br> O = Microsoft Corporation<br> C = US<br> | 必須 |

### <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?

アプリケーションでオペレーティング システムの証明書ストアを使用する場合、またはその他の間で Baltimore ルートをピン留めする場合は、何もする必要はありません。 一方、アプリケーションで中間証明書またはリーフ TLS 証明書をピン留めする場合は、次のルートをピン留めすることをお勧めします。

| Certificate | Thumbprint |
| ----- | ----- |
| [Baltimore Root CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 中間証明書とリーフ証明書の両方が頻繁に変更されることが想定されます。 それらに対して依存関係を使用しないことをお勧めします。 代わりに、アプリケーションをルート証明書にピン留めします。これは、ロール アウトの頻度が低いためです。
>
>

中間証明書を引き続きピン留めするには、ピン留めされた中間証明書リストに次を追加します。これには、将来の変更を最小限に抑えるための追加がいくつか含まれます。

| CA の共通名 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS 発行元 CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 発行元 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 発行元 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 発行元 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

アプリケーションでコード内の証明書が検証される場合は、新しくピン留めされた証明書のプロパティ (発行者、拇印など) を認識するように変更する必要があります。 この追加の検証では、すべてのピン留めされた証明書を今後に対応するように扱う必要があります。

## <a name="next-steps"></a>次の手順

その他の質問がある場合は、[サポート](https://azure.microsoft.com/support/options/)を通じてご連絡ください。  
