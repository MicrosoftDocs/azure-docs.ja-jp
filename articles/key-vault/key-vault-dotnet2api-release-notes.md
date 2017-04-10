---
title: "Key Vault .NET 2.x API リリース ノート| Microsoft Docs"
description: ".NET 開発者はこの API を使用して Azure Key Vault のコーディングを行います。"
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4dc8dea5c6e371db29fb9f85cfa635802cc0c52


---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - リリース ノートおよび移行ガイド
Azure Key Vault .NET / C# ライブラリを使用する開発者のための注意事項とガイダンスを次に説明します。 バージョン 1.0 からバージョン 2.0 に変わるまでん、いくつもの更新が行われており、Key Vault 証明書のサポートなど、機能の改善や追加の恩恵を受けるためにはコードの移行作業が必要になります。

Key Vault 証明書のサポートにより、x509 証明書が管理されるようになります。動作は次のとおりです。  

* 証明書の所有者が、Key Vault 作成プロセスを介して、または既存の証明書をインポートして、証明書を作成できます。 これには、自己署名証明書と証明機関によって生成された証明書の両方が含まれます。
* Key Vault 証明書の所有者は、秘密キー マテリアルを操作せずに、X509 証明書のセキュリティ保護されたストレージと管理を実装できます。  
* 証明書所有者が、証明書のライフサイクルを管理するように Key Vault が指示するポリシーを作成できます。  
* 証明書所有者が、証明書の失効や更新のライフサイクル イベントを通知するために証明書の連絡先情報を指定できます。  
* 選択した発行者 (Key Vault パートナー X509 証明書プロバイダーまたは証明機関) による自動更新をサポートします。
  
  * 注意 - パートナーではないプロバイダー/証明機関も許可されますが、自動更新機能はサポートされません。

## <a name="net-support"></a>.NET のサポート
* **.NET 4.0** は、Azure Key Vault .NET/C# ライブラリのバージョン 2.0 ではサポートされません。
* **.NET Core** は、Azure Key Vault .NET/C# ライブラリのバージョン 2.0 ではサポートされません。

## <a name="namespaces"></a>名前空間
* **モデル**の名前空間は、**Microsoft.Azure.KeyVault** から **Microsoft.Azure.KeyVault.Models** に変更されます。
* **Microsoft.Azure.KeyVault.Internal** 名前空間は削除されます。
* Azure SDK 依存関係の名前空間は、**Hyak.Common** と **Hyak.Common.Internals** から **Microsoft.Rest** と **Microsoft.Rest.Serialization** に変更されます。

## <a name="type-changes"></a>型の変更
* *Secret* が *SecretBundle* に変更
* *Dictionary* が *IDictionary* に変更
* *List<T>, string []* が *IList<T>* に変更
* *NextList* が *NextPageLink* に変更

## <a name="return-types"></a>戻り値の型
* **KeyList** と **SecretList** は、*ListKeysResponseMessage* ではなく *IPage<T>* を返します。
* 生成される **BackupKeyAsync** は *Value* (バックアップ BLOB) を含む *BackupKeyResult* を返します。 メソッドがラップされる前に、値のみを返します。

## <a name="exceptions"></a>例外
* *KeyVaultClientException* は *KeyVaultErrorException* に変更されます。
* サービス エラーは *exception.Error* から *exception.Body.Error.Message* に変更されます。
* **[JsonExtensionData]**のエラー メッセージの追加情報が削除されました。

## <a name="constructors"></a>コンストラクター
* *HttpClient* をコンストラクター引数として受け入れる代わりに、コンストラクターは *HttpClientHandler* または *DelegatingHandler[]* のみを受け入れます。

## <a name="downloaded-packages"></a>ダウンロードされるパッケージ
クライアントが Key Vault に関する依存関係を処理しているとき、次のパッケージがダウンロードされています。

#### <a name="previous-package-list"></a>以前のパッケージ リスト
* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

#### <a name="current-package-list"></a>現在のパッケージ リスト
* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>クラスの変更
* **UnixEpoch** クラスは削除されました。
* **Base64UrlConverter** クラスの名前が **Base64UrlJsonConverter** に変更されました。

## <a name="other-changes"></a>その他の変更点
* 一時的な障害に関する KV 操作再試行ポリシーの構成のサポートが、このバージョンの API に追加されました。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
* "*コンテナー*" を返した操作の場合、以前の戻り型は Vault プロパティを含むクラスでした。 現在、戻り型が *Vault* です。
* *PermissionsToKeys* と *PermissionsToSecrets* は現在は *Permissions.Keys* と *Permissions.Secrets* です。
* 戻り型の変更の一部は、コントロールプレーンにも適用されます。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
* 暗号化処理では、パッケージが **Microsoft.Azure.KeyVault.Extensions** と **Microsoft.Azure.KeyVault.Cryptography** に分割されています。




<!--HONumber=Nov16_HO3-->


