---
title: Azure Key Vault の新機能 | Microsoft Docs
description: Azure Key Vault の最近の更新
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b469ea0d0a91cc4ca7f0602dffbcc63b31c60855
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588366"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault の新機能

> [!WARNING]
> **2020 年 7 月**: サービスの実装に影響する可能性がある、Key Vault に対する 2 つの更新があります: [既定で有効になるキー コンテナーの論理的な削除](#soft-delete-on-by-default)、[Azure TLS 証明書の変更](#azure-tls-certificate-changes)。 詳細については、以下をご覧ください。

ここでは、Azure Key Vault の新機能について説明します。 新しい機能と改善点については、[Azure の更新情報の Key Vault チャンネル](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)でも発表されます。

## <a name="soft-delete-on-by-default"></a>既定で有効になる論理的な削除

2020 末までに、新規と既存両方の**すべてのキー コンテナーに対して論理的な削除が既定でオンになります**。 この破壊的変更の可能性に関する詳細、および影響を受けるキー コンテナーを見つけて事前に更新する手順については、「[すべてのキー コンテナーでの論理的な削除の有効化](soft-delete-change.md)」を参照してください。 

## <a name="azure-tls-certificate-changes"></a>Azure TLS 証明書の変更  

Microsoft では、異なるルート証明機関 (CA) のセットからの TLS 証明書を使用するように、Azure サービスが更新されています。 この変更は、現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つに準拠していないため行われています。

### <a name="when-will-this-change-happen"></a>この変更はいつ行われますか?

- 2020 年 7 月 7 日に、Azure Active Directory (Azure AD) サービスでこの移行が開始されました。
- 新しく作成されるすべての Azure TLS/SSL エンドポイントには、新しいルート CA にチェーンする更新された証明書が含まれています。 
- 既存の Azure エンドポイントは、2020 年 8 月 13 日から 2020 年 10 月 26 日までの期間に、段階的に移行されます。

> [!IMPORTANT]
> お客様は、Azure サービスに接続しようとしたときの接続エラーを防ぐため、この変更後にアプリケーションを更新することが必要になる場合があります。 

### <a name="what-is-changing"></a>何が変わるのですか?

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
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>古い中間サムプリントはいつ廃止できますか?

現在の CA 証明書は、2021 年 2 月 15 日まで取り消され "*ません*"。 その期日の後で、古いサムプリントをコードから削除できます。

この期日が変更された場合、新しい失効日が通知されます。

### <a name="will-this-affect-me"></a>どのような影響がありますか?

**Azure のほとんどのお客様には影響がない**ものと思われます。  ただし、受け入れ可能な CA のリストが明示的に指定されているアプリケーションでは、影響がある可能性があります。 この方法は、証明書のピン留めと呼ばれます。

アプリケーションが影響を受けるかどうかを検出するには、次の方法があります。

- ソース コードで、[こちら](https://www.microsoft.com/pki/mscorp/cps/default.htm)に記載されているいずれかの Microsoft IT TLS CA のサムプリント、共通名、および他の証明書プロパティを検索します。 一致するものがある場合、アプリケーションは影響を受けます。 この問題を解決するには、新しい CA を含むようにソース コードを更新します。 ベスト プラクティスとして、短期間で CA を追加または編集できるようにすることをお勧めします。 業界の規制では、CA 証明書を 7 日以内に交換する必要があるため、ピン留めを利用しているお客様は迅速に対応する必要があります。

- Azure API または他の Azure サービスと統合されているアプリケーションがあり、証明書のピン留めが使用されているかどうか不明な場合は、アプリケーション ベンダーに確認してください。

- Azure サービスと通信するさまざまなオペレーティング システムと言語ランタイムでは、新しいルートを使用して証明書チェーンを正しく構築するために追加の手順が必要になる場合があります。 
    - **Linux**:多くのディストリビューションでは、上に列記されている CA を /etc/ssl/certs に追加する必要があります。 具体的な手順については、ディストリビューションのドキュメントを参照してください。
    - **Java**: Java キース トアに上記の CA が含まれていることを確認します。
    - **切断された環境で実行されている Windows**: 切断された環境で実行されているシステムでは、上記のルートを信頼されたルート証明機関ストアに追加し、中間を中間証明機関ストアに追加する必要があります。
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

## <a name="june-2020"></a>2020 年 6 月

Azure Monitor for Key Vault がプレビュー段階になりました。  Azure Monitor ではキー コンテナーの要求、パフォーマンス、エラー、待機時間の統合ビューにより、キー コンテナーの包括的な監視が提供されます。 詳細については、[Azure Monitor for Key Vault (プレビュー)](../../azure-monitor/insights/key-vault-insights-overview.md) に関するページを参照してください。

## <a name="may-2020"></a>2020 年 5 月

Key Vault の "Bring Your Own Key" (BYOK) が一般提供されるようになりました。 [Azure Key Vault の BYOK の仕様](../keys/byok-specification.md)に関するページを参照し、[HSM で保護されたキーを Key Vault (BYOK) にインポートする](../keys/hsm-protected-keys-byok.md)方法について学習してください。

## <a name="march-2020"></a>2020 年 3 月

プライベート エンドポイントをプレビューで使用できるようになりました。 Azure Private Link サービスを使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure Key Vault および Azure でホストされている顧客サービスやパートナー サービスにアクセスできます。  [Key Vault と Azure Private Link を統合する](private-link-service.md)方法を学習してください。

## <a name="2019"></a>2019

- 次世代 Azure Key Vault SDK のリリース。 使用例については、[Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md)、[Node.js](../secrets/quick-create-node.md) の Azure Key Vault シークレット クイックスタートを参照してください
- Key Vault 証明書を管理するための新しい Azure ポリシー。 「[Key Vault 用の Azure Policy 組み込み定義](../policy-samples.md)」を参照してください。
- Azure Key Vault 仮想マシン拡張機能が一般提供されるようになりました。  「[Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md)」および「[Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md)」を参照してください。
- Azure Key Vault のイベントドリブン シークレット管理が Azure Event Grid で使用できるようになりました。 詳細については、[Azure Key Vault でのイベントの Event Grid スキーマ](../../event-grid/event-schema-key-vault.md)に関するページを参照し、[Azure Event Grid でキー コンテナー通知を受信して応答する](event-grid-tutorial.md)方法を学習してください。

## <a name="2018"></a>2018

今年リリースされた新機能と統合:

- Azure Functions との統合。 キー コンテナーの操作に [Azure Functions](../../azure-functions/index.yml) を利用するシナリオの例については、[シークレットのローテーションの自動化](../secrets/tutorial-rotation.md)に関するページを参照してください。 
- [Azure Databricks との統合](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 これにより、Azure Databricks では次の 2 種類のシークレット スコープがサポートされるようになります: Azure Key Vault に基づくものと、Databricks に基づくもの。 詳細については、「[Azure Key Vault に基づくシークレット スコープを 作成する](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)」を参照してください
- [Azure Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

今年リリースされた新機能:

- マネージド ストレージ アカウント キー。 Azure Storage と簡単に統合できるように、ストレージ アカウント キーの機能が追加されました。 詳しくは、[マネージド ストレージ アカウント キーの概要](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)に関するトピックをご覧ください。
- 論理的な削除。 論理削除機能により、キー コンテナーおよびキー コンテナー オブジェクトのデータ保護が強化されます。 詳しくは、[論理的な削除の概要](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)に関するトピックをご覧ください。

## <a name="2015"></a>2015

今年リリースされた新機能:
- 証明書管理。 2016 年 9 月 26 日に、GA バージョン 2015-06-01 に対する機能として追加されました。

一般提供 (バージョン 2015-06-01) が、2015 年 6 月 24 日に発表されました。 このリリースでは、次の点が変更されました。 
- キーの削除 - "use" フィールドが削除されました。
- キーについての情報の取得 - "use" フィールドが削除されました。
- コンテナーへのキーのインポート - "use" フィールドが削除されました。
- キーの復元 - "use" フィールドが削除されました。     
- RSA アルゴリズムの "RSA_OAEP" が "RSA-OAEP" に変更されました。 「[About keys, secrets, and certificates](about-keys-secrets-certificates.md)」(キー、シークレット、証明書について) をご覧ください。    
 
2 番目のプレビュー バージョン (バージョン 2015-02-01-preview) が、2015 年 4 月 20 日に発表されました。 詳しくは、[REST API の更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update)に関するブログ記事をご覧ください。 次のタスクが更新されました。
 
- コンテナー内のキーの一覧表示 - 改ページ位置の自動修正のサポートが操作に追加されました。
- キーのバージョンの一覧表示 - キーのバージョンを一覧表示する操作が追加されました。  
- コンテナー内のシークレットの一覧表示 - 改ページ位置の自動修正のサポートが追加されました。
- シークレットのバージョンの一覧表示 - シークレットのバージョンを一覧表示する操作が追加されました。  
- すべての操作 - 作成/更新タイムスタンプが属性に追加されました。  
- シークレットの作成 - Content-Type がシークレットに追加されました。
- キーの作成 - オプション情報としてタグが追加されました。
- シークレットの作成 - オプション情報としてタグが追加されました。
- キーの更新 - オプション情報としてタグが追加されました。
- シークレットの更新 - オプション情報としてタグが追加されました。
- シークレットの最大サイズが、10 K バイトから 25 K バイトに変更されました。 「[About keys, secrets, and certificates](about-keys-secrets-certificates.md)」(キー、シークレット、証明書について) をご覧ください。    

## <a name="2014"></a>2014
 
最初のプレビュー バージョン (バージョン 2014-12-08-preview) が、2015 年 1 月 8 日に発表されました。  

## <a name="next-steps"></a>次のステップ

その他の質問がある場合は、[サポート](https://azure.microsoft.com/support/options/)を通じてご連絡ください。  
