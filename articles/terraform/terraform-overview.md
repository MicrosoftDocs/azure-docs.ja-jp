---
title: Azure で Terraform を使用する
description: Terraform を使用した Azure インフラストラクチャのバージョン管理およびデプロイの概要
services: terraform
ms.service: azure
keywords: Terraform, DevOps, 概要, 計画, 適用, 自動化
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 4e51b8b5aa1cd00fcdd2481d73c40458d01d38c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173076"
---
# <a name="terraform-with-azure"></a>Azure での Terraform

[Hashicorp Terraform](https://www.terraform.io/) は、クラウド インフラストラクチャのプロビジョニングおよび管理を行うためのオープン ソース ツールです。 このツールでは、仮想マシン、ストレージ アカウント、ネットワーク インターフェイスなどのクラウド リソースのトポロジを記述した構成ファイルで、インフラストラクチャを体系化します。 Terraform のコマンド ライン インターフェイス (CLI) は、Azure またはその他のサポートされている任意のクラウドに構成ファイルをデプロイしそのバージョンを管理するための簡単なメカニズムを提供します。

この記事では、Terraform を使用して Azure インフラストラクチャを管理する利点について説明します。

## <a name="automate-infrastructure-management"></a>インフラストラクチャの管理を自動化する

Terraform のテンプレート ベースの構成ファイルを使用すると、Azure リソースの定義、プロビジョニング、構成を反復的および計画的に行うことができます。 インフラストラクチャの自動化には、次に示すようないくつかの利点があります。

- インフラストラクチャのデプロイおよび管理中に人為的なミスが発生する可能性が低くなります。
- 同じテンプレートを複数回デプロイして、同一の開発環境、テスト環境、実稼働環境を作成することができます。
- 開発環境およびテスト環境を要求に応じて作成することで、コストを削減できます。

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>インフラストラクチャの変更を理解してから適用する 

リソースのトポロジが複雑になるに従い、インフラストラクチャの変更の意味と、変更が及ぼす影響を理解することが難しくなる場合があります。

Terraform に用意されているコマンド ライン インターフェイス (CLI) を使用することで、ユーザーはインフラストラクチャの変更を検証およびプレビューしてからデプロイできるようになります。 インフラストラクチャの変更を安全かつ生産的にプレビューすることには、次に示すいくつかの利点があります。
- チーム メンバーは、提示されている変更とその影響をすばやく理解することによってより効率的に共同作業ができます。
- 開発プロセスの早い段階で、意図しない変更を捕捉できます。


## <a name="deploy-infrastructure-to-multiple-clouds"></a>複数のクラウドにインフラストラクチャをデプロイする

Terraform は、複数クラウドのシナリオによく使用されるツールです。このシナリオでは、Azure に加えて、その他のクラウド プロバイダーまたはオンプレミス データ センターにも類似のインフラストラクチャがデプロイされます。 これにより、開発者は複数のクラウド プロバイダー上のインフラストラクチャを同じツールと構成ファイルを使用して管理できます。

## <a name="next-steps"></a>次の手順

これで Terraform の概要と利点に関する説明は終了です。推奨する次の手順は、以下のとおりです。

- [Azure を使用するために Terraform をインストールして構成する](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)ことから始める
- [Terraform を使用して Azure 仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- [Terraform 用の Azure Resource Manager モジュールを探索する](https://www.terraform.io/docs/providers/azurerm/) 