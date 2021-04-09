---
title: Azure Key Vault のバージョン
description: Azure Key Vault のさまざまなバージョンです
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792380"
---
# <a name="key-vault-versions"></a>Key Vault のバージョン

ここでは、Azure Key Vault の新機能について説明します。 新しい機能と改善点については、[Azure の更新情報の Key Vault チャンネル](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)でも発表されます。

## <a name="june-2020"></a>2020 年 6 月

Azure Monitor for Key Vault がプレビュー段階になりました。  Azure Monitor ではキー コンテナーの要求、パフォーマンス、エラー、待機時間の統合ビューにより、キー コンテナーの包括的な監視が提供されます。 詳細については、[Azure Monitor for Key Vault (プレビュー)](../../azure-monitor/insights/key-vault-insights-overview.md) に関するページを参照してください。

## <a name="may-2020"></a>2020 年 5 月

Key Vault の "Bring Your Own Key" (BYOK) が一般提供されるようになりました。 [Azure Key Vault の BYOK の仕様](../keys/byok-specification.md)に関するページを参照し、[HSM で保護されたキーを Key Vault (BYOK) にインポートする](../keys/hsm-protected-keys-byok.md)方法について学習してください。

## <a name="march-2020"></a>2020 年 3 月

プライベート エンドポイントをプレビューで使用できるようになりました。 Azure Private Link サービスを使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure Key Vault および Azure でホストされている顧客サービスやパートナー サービスにアクセスできます。  [Key Vault と Azure Private Link を統合する](private-link-service.md)方法を学習してください。

## <a name="2019"></a>2019

- 次世代 Azure Key Vault SDK のリリース。 使用例については、[Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md)、[Node.js](../secrets/quick-create-node.md) の Azure Key Vault シークレット クイックスタートを参照してください
- Key Vault 証明書を管理するための新しい Azure ポリシー。 「[Key Vault 用の Azure Policy 組み込み定義](../policy-reference.md)」を参照してください。
- Azure Key Vault 仮想マシン拡張機能が一般提供されるようになりました。  「[Linux 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-linux.md)」および「[Windows 用の Key Vault 仮想マシン拡張機能](../../virtual-machines/extensions/key-vault-windows.md)」を参照してください。
- Azure Key Vault のイベントドリブン シークレット管理が Azure Event Grid で使用できるようになりました。 詳細については、[Azure Key Vault でのイベントの Event Grid スキーマ](../../event-grid/event-schema-key-vault.md)に関するページを参照し、[Azure Event Grid でキー コンテナー通知を受信して応答する](event-grid-tutorial.md)方法を学習してください。

## <a name="2018"></a>2018

今年リリースされた新機能と統合:

- Azure Functions との統合。 キー コンテナーの操作に [Azure Functions](../../azure-functions/index.yml) を利用するシナリオの例については、[シークレットのローテーションの自動化](../secrets/tutorial-rotation.md)に関するページを参照してください。 
- [Azure Databricks との統合](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 これにより、Azure Databricks では次の 2 種類のシークレット スコープがサポートされるようになります: Azure Key Vault に基づくものと、Databricks に基づくもの。 詳細については、「[Azure Key Vault に基づくシークレット スコープを 作成する](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)」を参照してください
- [Azure Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

今年リリースされた新機能:

- マネージド ストレージ アカウント キー。 Azure Storage と簡単に統合できるように、ストレージ アカウント キーの機能が追加されました。 詳しくは、[マネージド ストレージ アカウント キーの概要](../secrets/overview-storage-keys.md)に関するトピックをご覧ください。
- 論理的な削除。 論理削除機能により、キー コンテナーおよびキー コンテナー オブジェクトのデータ保護が強化されます。 詳しくは、[論理的な削除の概要](./soft-delete-overview.md)に関するトピックをご覧ください。

## <a name="2015"></a>2015

今年リリースされた新機能:
- 証明書管理。 2016 年 9 月 26 日に、GA バージョン 2015-06-01 に対する機能として追加されました。

一般提供 (バージョン 2015-06-01) が、2015 年 6 月 24 日に発表されました。 このリリースでは、次の点が変更されました。 
- キーの削除 - "use" フィールドが削除されました。
- キーについての情報の取得 - "use" フィールドが削除されました。
- コンテナーへのキーのインポート - "use" フィールドが削除されました。
- キーの復元 - "use" フィールドが削除されました。     
- RSA アルゴリズムの "RSA_OAEP" が "RSA-OAEP" に変更されました。 「[About keys, secrets, and certificates](about-keys-secrets-certificates.md)」(キー、シークレット、証明書について) をご覧ください。    
 
2 番目のプレビュー バージョン (バージョン 2015-02-01-preview) が、2015 年 4 月 20 日に発表されました。 詳しくは、[REST API の更新](/archive/blogs/kv/rest-api-update)に関するブログ記事をご覧ください。 次のタスクが更新されました。
 
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

- [キー、シークレット、証明書について](about-keys-secrets-certificates.md)
- [[キー]](../keys/index.yml)
- [シークレット](../secrets/index.yml)
- [証明書](../certificates/index.yml)