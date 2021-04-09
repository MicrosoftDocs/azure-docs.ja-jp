---
title: Azure Red Hat OpenShift クラスターをアップグレードする
description: OpenShift 4 を実行する Azure Red Hat OpenShift のクラスターをアップグレードする方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro、openshift、az aro、red hat、cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720887"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) クラスターをアップグレードする

ARO クラスター ライフサイクルの一部には、最新の OpenShift バージョンへの定期的なアップグレードの実行が含まれます。 最新のセキュリティ リリースを適用するか、アップグレードして最新の機能を入手することが重要です。 この記事では、OpenShift Web コンソールを使用して OpenShift クラスター内のすべてのコンポーネントをアップグレードする方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 現在のバージョンを調べるには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください

この記事では、既存の Azure Red Hat OpenShift クラスターに、`admin` 特権を持つユーザーとしてアクセスできることを前提としています。

## <a name="check-for-available-aro-cluster-upgrades"></a>利用できる ARO クラスターのアップグレードを確認する

OpenShift Web コンソールで、 **[管理]**  >  **[クラスター設定]** を選択し、 **[詳細]** タブを開きます。

お使いのクラスターの **[更新の状態]** に **[更新が利用可能]** が反映されている場合は、そのクラスターを更新できます。

## <a name="upgrade-your-aro-cluster"></a>ARO クラスターをアップグレードする

前の手順の Web コンソールから、 **[チャネル]** を、更新先のバージョンの正しいチャネル (`stable-4.5` など) に設定します。

更新先のバージョンを選択し、 **[更新]** を選択します。 更新の状態が `Update to <product-version> in progress` に変わります。 オペレーターとノードの進行状況バーを見ることで、クラスター更新の進行状況を確認できます。

## <a name="next-steps"></a>次のステップ
- [OC CLI を使用して ARO クラスターをアップグレードする方法について確認します](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- 使用可能な OpenShift Container Platform のアドバイザリと更新プログラムに関する情報については、カスタマー ポータルの [errata セクション](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata)を参照してください。
