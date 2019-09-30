---
title: Key Vault を使用するためのベスト プラクティス - Azure Key Vault | Microsoft Docs
description: このドキュメントでは、Key Vault を使用するためのいくつかのベスト プラクティスについて説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 654a9bb772c8a7426a335c98dfeca69515b9ce67
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881622"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault を使用するためのベスト プラクティス

## <a name="control-access-to-your-vault"></a>コンテナーへのアクセスの制御

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 このデータは機密性が高く、ビジネス上重要であるため、承認されたアプリケーションとユーザーだけを許可することで、ご利用のキー コンテナーへのアクセスをセキュリティで保護する必要があります。 この[記事](key-vault-secure-your-key-vault.md)では、Key Vault アクセス モデルの概要について説明します。 認証と承認について、およびご利用のキー コンテナーへのアクセスをセキュリティで保護する方法について説明します。

コンテナーへのアクセスを制御している間の推奨事項としては次のようなものがあります。
1. サブスクリプション、リソース グループ、および Key Vaults (RBAC) へのアクセスをロックダウンする
2. すべてのコンテナーのアクセス ポリシーを作成する
3. 最小特権アクセス プリンシパルを使用してアクセス権を付与する
4. ファイアウォールと [VNET サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)を有効にする

## <a name="use-separate-key-vault"></a>別々のキー コンテナーを使用する

環境 (開発、実稼働前、および実稼働) ごとにアプリケーションごとのコンテナーを使用することをお勧めします。 これにより複数の環境でシークレットを共有することがなくなり、セキュリティ違反が発生した際の脅威を軽減するのにも役立ちます。

## <a name="backup"></a>バックアップ

コンテナー内のオブジェクトの更新/削除/作成の際には、必ず[コンテナー](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)の定期的バックアップを取るようにしてください。

## <a name="turn-on-logging"></a>ログ記録の有効化

コンテナーの[ログ記録有効にします](key-vault-logging.md)。 また、アラートを設定します。

## <a name="turn-on-recovery-options"></a>回復オプションの有効化

1. [論理的な削除](key-vault-ovw-soft-delete.md)を有効にします。
2. 論理的な削除が有効になってもシークレット/コンテナーの強制削除を防ぐには、パージ保護を有効にします。
