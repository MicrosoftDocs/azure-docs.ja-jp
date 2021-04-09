---
title: キー コンテナーを別のリージョンに移動する - Azure Key Vault | Microsoft Docs
description: この記事では、キー コンテナーを別のリージョンに移動するためのガイダンスを提供します。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 54a1894c88d12cfac1a2d8062425de9549c4292e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93285209"
---
# <a name="move-an-azure-key-vault-across-regions"></a>リージョン間で Azure キー コンテナーを移動する

Azure Key Vault では、キー コンテナーをリージョン間で移動することを許可するリソース移動操作はサポートされていません。 この記事では、業務上、キー コンテナーを別のリージョンに移動する必要がある組織に向けた回避策について説明します。 それぞれの回避策には制限があります。 これらの回避策の意味を十分に理解したうえで、運用環境で適用してみることが重要です。

キー コンテナーを別のリージョンに移動するには、その別のリージョンにキー コンテナーを作成し、既存のキー コンテナーから新しいキー コンテナーに個々のシークレットを手動でコピーします。 これは、次の 2 つのオプションのいずれかを使用して実行できます。

## <a name="design-considerations"></a>設計上の考慮事項

始める前に、次の概念に注意してください。

* キー コンテナーの名前はグローバルに一意です。 コンテナー名を再利用することはできません。
* 新しいキー コンテナー内に、アクセス ポリシーとネットワーク構成設定を再構成する必要があります。
* 新しいキー コンテナー内に、論理的な削除と消去保護を再構成する必要があります。
* バックアップと復元の操作では、オートローテーション設定は保持されません。 場合によっては、設定を再構成する必要があります。

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>オプション 1: キー コンテナーのバックアップ コマンドと復元コマンドを使用する

バックアップ コマンドを使用して、コンテナー内の個々のシークレット、キー、および証明書をバックアップできます。 シークレットは暗号化された BLOB としてダウンロードされます。 その後、その BLOB を新しいキー コンテナー内に復元できます。 コマンドの一覧については、「[Azure Key Vault のコマンド](/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)」をご覧ください。

バックアップ コマンドと復元コマンドの使用には、次の 2 つの制限があります。

* ある地域のキー コンテナーをバックアップし、別の地域に復元することはできません。 詳しくは、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」をご覧ください。

* バックアップ コマンドでは、各シークレットのすべてのバージョンがバックアップされます。 以前のバージョンが多数 (11 個以上) 存在するシークレットがある場合は、要求のサイズが許容される最大値を超え、操作が失敗する可能性があります。

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>オプション 2:キー コンテナー シークレットを手動でダウンロードしてアップロードする

特定のシークレットの種類は、手動でダウンロードできます。 たとえば、証明書を PFX ファイルとしてダウンロードできます。 このオプションを使用すると、証明書などの一部のシークレットの種類に対する地域の制限はなくなります。 任意のリージョンの任意のキー コンテナーに PFX ファイルをアップロードできます。 シークレットは、パスワードで保護されていない形式でダウンロードされます。 移動中のシークレットの保護は、お客様が責任を負うものとします。