---
title: お使いの Azure および Red Hat OpenShift v4 クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、Azure Monitor for containers でお使いの Azure Red Hat OpenShift および Red Hat OpenShift バージョン 4 クラスターの監視を停止する方法について説明します。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091149"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>お使いの Azure および Red Hat OpenShift v4 クラスターの監視を停止する方法

お使いの Azure Red Hat OpenShift および Red Hat OpenShift バージョン 4 クラスターの監視を有効にした後に、Azure Monitor for containers でクラスターを監視する必要がなくなった場合は、それでの監視を停止することができます。 この記事では、これを行う方法について説明します。  

## <a name="how-to-stop-monitoring-using-helm"></a>Helm を使用して停止する方法

1. まず、helm の次のコマンドを実行し、お使いのクラスターに Azure Monitor for containers の helm チャートがインストールされていることを確認します。

    ```
    helm list
    ```

    出力は次のようになります。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* は、Azure Monitor for containers の helm チャートのリリースです。

2. 次の helm コマンドを実行して、このチャート リリースを削除します。

    `helm delete <releaseName>`

    例:

    `helm delete azmon-containers-release-1`

    これにより、クラスターからこのリリースが削除されます。 これは、`helm list` コマンドを実行して確認できます。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

設定の変更が完了するまで数分かかります。 Helm では、お使いのリリースの削除後もそれを追跡するので、クラスターの履歴を監査し、`helm rollback` を使用して、リリースの削除を取り消すこともできます。

## <a name="next-steps"></a>次のステップ

クラスターを監視するためだけに作成した Log Analytics ワークスペースが不要になった場合は、それを手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、[Azure Log Analytics ワークスペースの削除](../platform/delete-workspace.md)に関するページを参照してください。
