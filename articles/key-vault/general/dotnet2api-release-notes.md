---
title: Key Vault .NET 2.x API リリース ノート| Microsoft Docs
description: C# および .NET 向けの Azure Key Vault ライブラリの 2.0 バージョンと連動するように、以前のバージョンの Azure Key Vault 向けに開発されたアプリを更新する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013957"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 - リリース ノートおよび移行ガイド
次の情報は、C# および .NET 対応の Azure Key Vault ライブラリ 2.0 バージョンに移行するのに役立ちます。  以前のバージョン用に記述されたアプリは、最新バージョンをサポートするように更新する必要があります。  これらの変更では、**Key Vault 証明書** など、改善後の新機能を完全にサポートする必要があります。

## <a name="key-vault-certificates"></a>Key Vault 証明書

Key Vault 証明書では x509 証明書を管理し、次の動作をサポートします。  

* Key Vault 作成プロセス経由で証明書を作成するか、または既存の証明書をインポートします。 これには、自己署名と証明機関 (CA) によって生成された証明書の両方が含まれます。
* 秘密キー マテリアルを使用した操作を伴わずに、x509 証明書ストレージを安全に保存および管理します。  
* 証明書のライフ サイクルを管理するには、Key Vault を指示するポリシーを定義します。  
* 期限切れの警告や更新通知など、ライフサイクル イベントに関するお問い合わせ先を提示します。  
* 選択されている発行元 (Key Vault パートナー X509 証明書発行元および証明機関) で証明書を自動的に更新します。* その他の発行元 (非パートナー) や証明機関 (自動更新をサポートしていない) からの証明書をサポートします。  

## <a name="net-support"></a>.NET のサポート

* **.NET 4.0** は、Azure Key Vault .NET ライブラリのバージョン 2.0 ではサポートされません
* **.NET Framework 4.5.2** は、Azure Key Vault .NET ライブラリのバージョン 2.0 ではサポートされません
* **.NET Standard 1.4** は、Azure Key Vault .NET ライブラリのバージョン 2.0 ではサポートされません

## <a name="namespaces"></a>名前空間

* **モデル** の名前空間は、**Microsoft.Azure.KeyVault** から **Microsoft.Azure.KeyVault.Models** に変更されます。
* **Microsoft.Azure.KeyVault.Internal** 名前空間は削除されます。
* 次の Azure SDK 依存関係の名前空間は、以下を含みます。 

    - **Hyak.Common** は現在、**Microsoft.Rest** です。
    - **Hyak.Common.Internals** は現在、**Microsoft.Rest.Serialization** です。

## <a name="type-changes"></a>型の変更

* *Secret* が *SecretBundle* に変更
* *Dictionary* が *IDictionary* に変更
* *List\<T>, string []* が *IList\<T>* に変更
* *NextList* が *NextPageLink* に変更

## <a name="return-types"></a>戻り値の型

* **KeyList** と **SecretList** は現在、*ListKeysResponseMessage* ではなく *IPage\<T>* を返します。
* 生成される **BackupKeyAsync** は現在、*Value* (バックアップ BLOB) を含む *BackupKeyResult* を返します。 以前は、メソッドがラップされ、値のみが返されました。

## <a name="exceptions"></a>例外

* *KeyVaultClientException* は *KeyVaultErrorException* に変更されます。
* サービス エラーは *exception.Error* から *exception.Body.Error.Message* に変更されます。
* **[JsonExtensionData]** のエラー メッセージの追加情報が削除されました。

## <a name="constructors"></a>コンストラクター

* *HttpClient* をコンストラクター引数として受け入れる代わりに、コンストラクターは *HttpClientHandler* または *DelegatingHandler[]* のみを受け入れます。

## <a name="downloaded-packages"></a>ダウンロードされるパッケージ

クライアントが Key Vault の依存関係を処理するとき、次のパッケージがダウンロードされます。

### <a name="previous-package-list"></a>以前のパッケージ リスト

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>現在のパッケージ リスト

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>クラスの変更

* **UnixEpoch** クラスは削除されました。
* **Base64UrlConverter** クラスの名前が **Base64UrlJsonConverter** に変更されました。

## <a name="other-changes"></a>その他の変更点

* 一時的な障害に関する KV 操作再試行ポリシーの構成のサポートが、このバージョンの API に追加されました。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* "*コンテナー*" を返した操作の場合、以前の戻り値の型は **Vault** プロパティを含むクラスでした。 現在、戻り型が *Vault* です。
* *PermissionsToKeys* と *PermissionsToSecrets* は現在は *Permissions.Keys* と *Permissions.Secrets* です。
* 特定の戻り値の型の変更は、コントロールプレーンにも適用されます。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 暗号化処理では、パッケージが **Microsoft.Azure.KeyVault.Extensions** と **Microsoft.Azure.KeyVault.Cryptography** に分割されています。

