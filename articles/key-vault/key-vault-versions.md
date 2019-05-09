---
title: Key Vault のバージョン
description: Azure Key Vault のさまざまなバージョンです
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b7e3aca133e2e9614ab83be83c20a4dbc2ae5fe2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542388"
---
# <a name="key-vault-versions"></a>Key Vault のバージョン

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - マネージド ストレージ アカウント キー

2017 年夏 - Azure Storage と簡単に統合できるように、ストレージ アカウント キーの機能が追加されました。 詳しくは、[マネージド ストレージ アカウント キーの概要](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)に関するトピックをご覧ください。

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - 論理的な削除

2017 年夏 - キー コンテナーおよびキー コンテナー オブジェクトのデータ保護を強化するため、論理削除機能が追加されました。 詳しくは、[論理的な削除の概要](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)に関するトピックをご覧ください。

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - 証明書の管理

2016 年 9 月 26 日に、GA バージョン 2015-06-01 に対する機能として、証明書の管理が追加されました。

## <a name="2015-06-01---general-availability"></a>2015-06-01 - 一般公開

一般公開バージョン 2015-06-01 が、2015 年 6 月 24 日に発表されました。

このリリースでは、次の点が変更されました。

- キーの削除 - "use" フィールドが削除されました。
- キーについての情報の取得 - "use" フィールドが削除されました。
- コンテナーへのキーのインポート - "use" フィールドが削除されました。
- キーの復元 - "use" フィールドが削除されました。
- RSA アルゴリズムの "RSA_OAEP" が "RSA-OAEP" に変更されました。 「[About keys, secrets, and certificates](about-keys-secrets-and-certificates.md)」(キー、シークレット、証明書について) をご覧ください。

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

2 番目のプレビュー バージョン 2015-02-01-preview が、2015 年 4 月 20 日に発表されました。 詳しくは、[REST API の更新](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx)に関するブログ記事をご覧ください。

次のタスクが更新されました。

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
- シークレットの最大サイズが、10 K バイトから 25 K バイトに変更されました。 「[About keys, secrets, and certificates](about-keys-secrets-and-certificates.md)」(キー、シークレット、証明書について) をご覧ください。

## <a name="2014-12-08-preview"></a>2014-12-08-preview

最初のプレビュー バージョン 2014-12-08-preview が、2015 年 1 月 8 日に発表されました。

## <a name="see-also"></a>関連項目
- [キー、シークレット、証明書について](about-keys-secrets-and-certificates.md)
