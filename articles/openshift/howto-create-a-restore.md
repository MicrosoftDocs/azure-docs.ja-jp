---
title: Velero を使用して Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成する
description: Velero を使用して Azure Red Hat OpenShift クラスターアプリケーションの復元を作成する方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 8a9bb191390056caf7d5ba3b42c278205177d5a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213014"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成する

この記事では、Azure Red Hat OpenShift 4 クラスター アプリケーションの復元を作成するための環境を準備します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件を設定し、必要なツールをインストールする
> * Azure Red Hat OpenShift 4 アプリケーションの復元を作成する

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Azure Red Hat OpenShift 4 アプリケーションのバックアップを作成する

Azure Red Hat OpenShift 4 アプリケーションのバックアップを作成するには、「[Azure Red Hat OpenShift 4 アプリケーションのバックアップを作成する](./howto-create-a-backup.md)」を参照してください

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Azure Red Hat OpenShift 4 アプリケーションを復元する

これらの手順により、以前に Velero でバックアップされたアプリケーションを復元できます。
クラスターによって現在認識されているバックアップの一覧を確認して、復元に使用できるバックアップを確認できます。  この手順を実行するには、次のコマンドを実行する必要があります。

_(この手順では、"Velero" という名前のプロジェクトに Velero をインストールしていることを前提としています)_

```bash
oc get backups -n velero
```

復元するバックアップを作成したら、次のコマンドを使用して復元を実行する必要があります。

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

この手順では、前の手順でバックアップの作成時にバックアップされた Kubernetes オブジェクトを作成します。

復元の状態を確認するには、次の手順を実行します。

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
フェーズで `Completed` と表示されたら、Azure Red Hat 4 アプリケーションが復元されているはずです。

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>スナップショットを含む Azure Red Hat OpenShift 4 アプリケーションを復元する


Velero を使用して永続ボリュームを含む Azure Red Hat OpenShift 4 アプリケーションの復元を作成するには、次のコマンドを使用して復元を実行する必要があります。

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
この手順では、前の手順でバックアップの作成時にバックアップされた Kubernetes オブジェクトを作成します。

復元の状態を確認するには、次の手順を実行します。

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
フェーズで `Completed` と表示されたら、Azure Red Hat 4 アプリケーションが復元されているはずです。

Velero を使用してバックアップと復元を作成する方法の詳細については、[ネイティブな方法による OpenShift リソースのバックアップ](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)に関するページを参照してください

## <a name="next-steps"></a>次の手順

この記事では、Azure Red Hat OpenShift 4 クラスター アプリケーションを復元しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Velero を使用して OpenShift v4 クラスター アプリケーションの復元を作成する
> * Velero を使用してスナップショットを含む OpenShift v4 クラスター アプリケーションの復元を作成する


次の記事に進んで、Azure Red Hat OpenShift 4 でサポートされているリソースについて確認してください。

* [Azure Red Hat OpenShift v4 でサポートされているリソース](supported-resources.md)