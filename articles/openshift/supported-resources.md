---
title: Azure Red Hat OpenShift 3.11 でサポートされているリソース
description: Microsoft Azure Red Hat OpenShift でサポートされている Azure リージョンと仮想マシンのサイズについて説明します。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635610"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift のリソース

このトピックでは、Microsoft Azure Red Hat OpenShift 3.11 サービスでサポートされている Azure リージョンと仮想マシンのサイズを示します。

## <a name="azure-regions"></a>Azure Azure リージョン

Azure Red Hat OpenShift クラスターをデプロイできる最新のリージョン一覧については、[リージョン別の使用できる製品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)に関する記事を参照してください。

## <a name="virtual-machine-sizes"></a>仮想マシン サイズ

Azure Red Hat OpenShift クラスター内のコンピューティング ノードに対して指定できる、サポートされている仮想マシンのサイズは次のとおりです。

> [!Important]
> それぞれの VM で接続できるドライブ数は異なります。 これは、メモリや CPU サイズほどすぐには明らかにならない場合があります.
> すべてのリージョンですべての VM サイズを使用できるわけではありません。 指定したサイズが API でサポートされていても、指定したリージョンでそのサイズが利用できない場合は、エラーが発生する可能性があります。
> 詳細については、[リージョンごとのサポートされている VM サイズの最新一覧](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを参照してください。

## <a name="compute-node-sizes"></a>コンピューティング ノードのサイズ

Azure Red Hat OpenShift の REST API では、次のコンピューティング ノードのサイズがサポートされています。

|サイズ|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>マスター ノードのサイズ

Azure Red Hat OpenShift の REST API では、次のマスター/インフラストラクチャ ノードのサイズがサポートされています。

|サイズ|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>次のステップ

[Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)に関するチュートリアルを試してください。
