---
title: Azure Storage を追加する (コンテナー)
description: Azure App Service 内のコンテナー化されたアプリにカスタム ネットワーク共有をアタッチする方法について説明します。 アプリ間でのファイルの共有、静的コンテンツのリモート管理、ローカルでのアクセスなどを行います。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c3e5ca30b7f0f00b6d647f5fdaeb772900996adc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720241"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>App Service 内のコンテナーからネットワーク共有としての Azure Storage (プレビュー) にアクセスする

::: zone pivot="container-windows"

このガイドでは、App Service で Azure Storage ファイルをネットワーク共有として Windows コンテナーに接続する方法について説明します。 [Azure Files Shares](../storage/files/storage-how-to-use-files-cli.md) および [Premium ファイル共有](../storage/files/storage-how-to-create-file-share.md)のみがサポートされています。 利点としては、セキュリティで保護されたコンテンツ、コンテンツの移植性、複数のアプリへのアクセス、複数の転送方法などがあります。

> [!NOTE]
>App Service の Azure Storage は **プレビュー段階** であり、**運用シナリオ** では **サポートされていません**。

::: zone-end

::: zone pivot="container-linux"

このガイドでは、Azure Storage を Linux コンテナーの App Service にアタッチする方法について説明します。 利点としては、セキュリティで保護されたコンテンツ、コンテンツの移植性、永続ストレージ、複数のアプリへのアクセス、複数の転送方法などがあります。

> [!NOTE]
>App Service の Azure Storage は、App Service on Linux と Web App for Containers では **プレビュー段階** にあります。 **運用シナリオ** では **サポートされていません**。

::: zone-end

## <a name="prerequisites"></a>前提条件

::: zone pivot="container-windows"

- [Azure App Service の既存の Windows コンテナー アプリ](quickstart-custom-container.md)
- [Azure ファイル共有の作成](../storage/files/storage-how-to-use-files-cli.md)
- [Azure File 共有へのファイルのアップロード](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- 既存の [App Service on Linux アプリ](index.yml)。
- [Azure Storage アカウント](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure のファイル共有とディレクトリ](../storage/files/storage-how-to-use-files-cli.md)。

::: zone-end

> [!NOTE]
> Azure Files は、既定ではないストレージであり、別途請求され、Web アプリには含まれません。 インフラストラクチャの制限のため、ファイアウォール構成の使用はサポートされません。
>

## <a name="limitations"></a>制限事項

::: zone pivot="container-windows"

- App Service の Azure Storage は、現在、独自のコードを使用するシナリオ (コンテナー化されていない Windows アプリ) では **サポートされていません**。
- App Service の Azure Storage は、インフラストラクチャの制限により、**ストレージ ファイアウォール** 構成の使用を **サポートしていません**。
- App Service を使用する Azure Storage では、アプリあたり **最大 5 つ** のマウント ポイントを指定できます。
- アプリにマウントされた Azure Storage に App Service の FTP または FTPS エンドポイント経由でアクセスすることはできません。 [Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)を使用します。

::: zone-end

::: zone pivot="container-linux"

- App Service の Azure Storage は、**Azure Files コンテナー** (読み取り/書き込み) と **Azure Blob コンテナー** (読み取り専用) のマウントをサポートしています。
- App Service の Azure Storage では、アプリあたり **最大 5 つ** のマウント ポイントを指定できます。
- アプリにマウントされた Azure Storage に App Service の FTP または FTPS エンドポイント経由でアクセスすることはできません。 [Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)を使用します。

::: zone-end

## <a name="link-storage-to-your-app"></a>ストレージをアプリにリンクする

::: zone pivot="container-windows"

[Azure Storage アカウント、ファイル共有、ディレクトリ](#prerequisites)を作成したら、Azure Storage を使用してアプリを構成できるようになります。

Azure Files 共有を App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類は、AzureFiles である必要があります。

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Azure Files 共有にリンクする他のすべてのディレクトリについて、これを行う必要があります。

::: zone-end

::: zone pivot="container-linux"

[Azure Storage アカウント、ファイル共有、ディレクトリ](#prerequisites)を作成したら、Azure Storage を使用してアプリを構成できるようになります。

ストレージ アカウントを App Service アプリのディレクトリにマウントするには、[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) コマンドを使用します。 ストレージの種類としては、AzureBlob または AzureFiles を使用できます。 この例では、AzureFiles が使用されています。 マウント パス設定は、Azure Storage にマウントするコンテナー内のフォルダーに対応します。 "/" に設定すると、Azure Storage にコンテナー全体がマウントされます。


> [!CAUTION]
> Web アプリでマウント パスとして指定されたディレクトリは、空である必要があります。 このディレクトリに格納されているコンテンツは、外部マウントが追加されるときに削除されます。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

ストレージ アカウントにリンクする他のすべてのディレクトリについて、これを行う必要があります。

::: zone-end

## <a name="verify-linked-storage"></a>リンクされたストレージを確認する

共有をアプリにリンクしたら、次のコマンドを実行してこれを確認できます。

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>次の手順

::: zone pivot="container-windows"

- [カスタム コンテナーを使用してカスタム ソフトウェアを Azure App Service に移行する](tutorial-custom-container.md?pivots=container-windows)。

::: zone-end

::: zone pivot="container-linux"

- [カスタム コンテナーを構成する](configure-custom-container.md?pivots=platform-linux)。

::: zone-end