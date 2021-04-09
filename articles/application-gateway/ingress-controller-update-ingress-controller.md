---
title: Helm を使用してイングレス コントローラーをアップグレードする
description: この記事では、Helm を使用して Application Gateway イングレスをアップグレードする方法に関する情報を提供します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84806777"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Helm を使用して Application Gateway イングレス コントローラーをアップグレードする方法 

Kubernetes の Azure Application Gateway イングレス コントローラー (AGIC) は、Azure Storage でホストされている Helm リポジトリを使用してアップグレードできます。

アップグレード手順を開始する前に、必要なリポジトリが追加されていることを確認します。

- 次を使用して、現在追加されている Helm リポジトリを表示します。

    ```bash
    helm repo list
    ```

- 次を使用して、AGIC リポジトリを追加します。

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>アップグレード

1. AGIC Helm リポジトリを更新して、最新のリリースを取得します。

    ```bash
    helm repo update
    ```

1. `application-gateway-kubernetes-ingress` グラフの使用可能なバージョンを表示します。

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    応答のサンプル:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    上記の一覧からの利用可能な最新バージョンは: `0.7.0-rc1` です

1. 現在インストールされている Helm グラフを次のように表示します。

    ```bash
    helm list
    ```

    応答のサンプル:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    上記のサンプル応答からの Helm グラフのインストールには `odd-billygoat` という名前が付けられます。 残りのコマンドには、この名前を使用します。 実際のデプロイ名は異なる可能性が高くなります。

1. Helm デプロイを新しいバージョンにアップグレードします。

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>ロールバック

Helm のデプロイが失敗した場合は、以前のリリースにロールバックできます。

1. 最後の既知の正常なリリース番号を取得します。

    ```bash
    helm history odd-billygoat
    ```

    サンプル出力:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    `helm history` コマンドのサンプル出力からは、最後に正常にデプロイされた `odd-billygoat` がリビジョン `1` であるように見えます

1. 最後に成功したリビジョンにロールバックします。

    ```bash
    helm rollback odd-billygoat 1
    ```
