---
title: Azure Key Vault に格納されているシークレット、キー、または証明書をバックアップする | Microsoft Docs
description: このドキュメントは、Azure Key Vault に格納されたシークレット、キー、証明書をバックアップする際の参考としてご利用ください。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/22/2020
ms.author: sudbalas
ms.openlocfilehash: c55c87cd8ab1b2cd1dbaf2c877eb341744d822ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93287459"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault のバックアップ

このドキュメントでは、キー コンテナーに格納されているシークレット、キー、および証明書をバックアップする方法について説明します。 バックアップの目的は、キー コンテナーにアクセスできなくなるといった不測の事態が発生した場合に、すべてのシークレットのオフライン コピーを入手できることにあります。

## <a name="overview"></a>概要

Azure Key Vault は、可用性を維持し、データの損失を防ぐうえで役立つ機能を自動的に提供します。 業務上の正当かつ重要な理由がある場合にのみ、シークレットをバックアップするようにしてください。 キー コンテナーにシークレットをバックアップすると、シークレットの有効期限が切れたりシークレットのローテーションを行ったりした際に、ログ、アクセス許可、およびバックアップの一式を複数維持しなければならないなど、運用上の負担が発生します。

Key Vault を使用すると、障害状況下で可用性を維持し、ユーザーの介入なしに自動的に要求をペア リージョンにフェールオーバーできます。 詳細については、「[Azure Key Vault の可用性と冗長性](./disaster-recovery-guidance.md)」を参照してください。

不注意や悪意によってシークレットが削除されないようにする場合は、キー コンテナーに論理的な削除と消去保護の機能を構成します。 詳細については、「[Azure Key Vault の論理的な削除の概要](./soft-delete-overview.md)」を参照してください。

## <a name="limitations"></a>制限事項

> [!IMPORTANT]
> Key Vault で、500 を超える過去のバージョンのキー、シークレット、または証明書オブジェクトをバックアップする機能はサポートされていません。 キー、シークレット、または証明書オブジェクトをバックアップしようとすると、エラーが発生するおそれがあります。 キー、シークレット、または証明書の以前のバージョンを削除することはできません。

Key Vault では現在、キー コンテナー全体を 1 回の操作でバックアップすることはできません。 このドキュメントに記載されているコマンドを使用してキー コンテナーの自動バックアップを実行しようとするとエラーが発生する可能性があります。これは、Microsoft でも Azure Key Vault チームでもサポートしていません。 

また、以下のような影響についても考慮してください。

* 複数のバージョンがあるシークレットをバックアップすると、タイムアウト エラーが発生する可能性があります。
* バックアップによって、ポイントインタイム スナップショットが作成されます。 バックアップ中にシークレットが更新された場合、暗号化キーの不一致が生じることがあります。
* 1 秒あたりの要求数に関するキー コンテナー サービスの制限を超えると、キー コンテナーがスロットルされ、バックアップが失敗する原因となります。

## <a name="design-considerations"></a>設計上の考慮事項

キー コンテナー オブジェクト (シークレット、キー、証明書など) をバックアップすると、そのオブジェクトは、バックアップ操作によって、暗号化された BLOB としてダウンロードされます。 Azure の外部でこの BLOB の暗号化を解除することはできません。 この BLOB から有効なデータを取得するには、同じ Azure サブスクリプションと [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)内のキー コンテナーに BLOB を復元する必要があります。

## <a name="prerequisites"></a>前提条件

キー コンテナー オブジェクトをバックアップするには、次のものが必要です。 

* Azure サブスクリプションに対する共同作成者レベル以上のアクセス許可。
* バックアップ対象のシークレットを格納するプライマリ キー コンテナー。
* シークレットの復元先となるセカンダリ キー コンテナー

## <a name="back-up-and-restore-from-the-azure-portal"></a>Azure portal からのバックアップと復元

このセクションの手順に従い、Azure portal を使用してオブジェクトをバックアップおよび復元します。

### <a name="back-up"></a>バックアップ

1. Azure Portal にアクセスします。
2. キー コンテナーを選択します。
3. バックアップするオブジェクト (シークレット、キー、または証明書) に移動します。

    ![キー コンテナーのキー設定とオブジェクトを選択する場所を示すスクリーンショット。](../media/backup-1.png)

4. オブジェクトを選択します。
5. **[バックアップのダウンロード]** を選択します。

    ![キー コンテナーの [バックアップのダウンロード] ボタンを選択する場所を示すスクリーンショット。](../media/backup-2.png)
    
6. **[Download]** を選択します。

    ![キー コンテナーの [ダウンロード] ボタンを選択する場所を示すスクリーンショット。](../media/backup-3.png)
    
7. 暗号化された BLOB を安全な場所に保存します。

### <a name="restore"></a>復元

1. Azure Portal にアクセスします。
2. キー コンテナーを選択します。
3. 復元するオブジェクトの種類 (シークレット、キー、または証明書) に移動します。
4. **[バックアップの復元]** を選択します。

    ![キー コンテナーの [バックアップの復元] を選択する場所を示すスクリーンショット。](../media/backup-4.png)
    
5. 暗号化された BLOB の保存先に移動します。
6. **[OK]** を選択します。

## <a name="back-up-and-restore-from-the-azure-cli"></a>Azure CLI からのバックアップと復元

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>次のステップ

Key Vault の[ログ記録と監視](./logging.md)を有効にします。