---
title: "Azure の Windows VM 用リソース グループ | Microsoft Docs"
description: "Azure インフラストラクチャ サービスでのリソース グループのデプロイに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0fbcabcd-e96d-4d71-a526-431984887451
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d0576b965bd2e96f377aeb33765e71fa20140d7d
ms.lasthandoff: 03/03/2017


---
# <a name="azure-resource-group-guidelines-for-windows-vms"></a>Windows VM 用の Azure リソース グループのガイドライン

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

この記事では、環境を論理的に構築し、リソース グループ内のすべてのコンポーネントをグループ化する方法について説明します。

## <a name="implementation-guidelines-for-resource-groups"></a>リソース グループに関する実装ガイドライン
決めること:

* リソース グループを構築するために、コア インフラストラクチャのコンポーネントを使用しますか。あるいはアプリケーションを完全デプロイしますか。
* ロールベースのアクセス制御を使用してリソース グループへのアクセスを制限する必要がありますか。

タスク:

* 必要なコア インフラストラクチャのコンポーネントと、専用のリソース グループを定義します。
* 一貫性があり、再現可能なデプロイのための Resource Manager テンプレートを実装する方法を確認します。
* リソース グループへのアクセスを制御するために必要なユーザー アクセス ロールを定義します。
* 名前付け規則を使用してリソース グループのセットを作成します。 Azure PowerShell またはポータルを使用できます。

## <a name="resource-groups"></a>リソース グループ
Azure では、ストレージ アカウント、仮想ネットワーク、仮想マシン (VM) などの関連リソースを論理的にグループ化し、単一のエンティティとしてデプロイ、管理、保守します。 このアプローチにより、管理上の観点からすべての関連リソースをまとめた状態でアプリケーションをデプロイすること、あるいは他者にリソースのグループへのアクセス権を付与することが容易になります。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

リソース グループの機能で重要なのは、テンプレートを使用して環境を構築できることです。 テンプレートは、ストレージ、ネットワーク、コンピューティング リソースを宣言する JSON ファイルにすぎません。 適用する関連カスタム スクリプトや構成を定義することもできます。 このテンプレートを使用して、一貫性があり、再現可能なアプリケーションのデプロイを作成します。 このアプローチにより、開発環境を構築し、同じテンプレートを使用して運用環境のデプロイを作成できます。また、その逆も可能です。 テンプレートの使用に関する理解を深めるには、[テンプレートのチュートリアル](../resource-manager-template-walkthrough.md)に関するページをご覧ください。このページでは、テンプレートの構築の手順について説明しています。

リソース グループを使用して環境を設計するには、2 つの異なるアプローチがあります。

* ストレージ アカウント、仮想ネットワーク、サブネット、VM、ロード バランサーなどを組み合わせた各アプリケーションのデプロイのためのリソース グループ。
* コア仮想ネットワーク、サブネット、またはストレージ アカウントを含む一元的なリソース グループ。 アプリケーションは、VM、ロード バランサー、ネットワーク インターフェイスなどのアイテムのみを含む独自のリソース グループに含まれます。

スケールアウトするとき、仮想ネットワークおよびサブネットの一元的なリソース グループを作成することで、ハイブリッド接続オプション用のクロスプレミス ネットワーク接続を容易に構築できます。 もう&1; つのアプローチでは、アプリケーションごとに、構成と保守が必要な独自の仮想ネットワークを使用します。  [ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md) により、リソース グループへのアクセスを詳細に制御できます。 運用アプリケーションの場合は、リソースにアクセスできるユーザーを制御でき、コア インフラストラクチャのリソースの場合は、インフラストラクチャ エンジニアだけが扱えるように制限することができます。 アプリケーション所有者はリソース グループ内のアプリケーション コンポーネントにのみアクセスでき、お使いの環境の Azure インフラストラクチャの中心となる部分にはアクセスできません。 環境を設計する際、リソースへのアクセスを必要とするユーザーと、それに応じたリソース グループの設計を考慮してください。 

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


