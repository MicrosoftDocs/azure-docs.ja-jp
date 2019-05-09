---
title: Habitat を使用してアプリケーションを Azure にデプロイする
description: Azure 仮想マシンとコンテナーにアプリケーションを一貫した方法でデプロイする方法を説明します
keywords: azure、chef、devops、仮想マシン、概要、自動化、habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358526"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Habitat を使用してアプリケーションを Azure にデプロイする
[Habitat](https://www.habitat.sh/) はアプリケーション パッケージ化およびランタイム システムであり、アプリケーションとその自動化をデプロイの単位としてバンドルします。 これにより、アプリケーションのための究極の移植性が作成され、書き換えや再パッケージ化を行う必要なしに、コンテナー、仮想マシン、ベア メタル、または PaaS にアプリケーションをデプロイできます。

この記事では、Habitat を使用する主な利点について説明します。

## <a name="modernize-and-move-legacy-applications"></a>レガシ アプリケーションを最新化して移行する
Habitat で再パッケージ化することにより、レガシ アプリケーションが古いオペレーティング システムやミドルウェアから解放されます。 結果として得られる成果物は移植可能であり、クラウドで実行されている仮想マシンやコンテナーのような新しいインフラストラクチャに、簡単にプラットフォームを変更できます。

## <a name="accelerate-container-adoption"></a>コンテナーの導入を促進する
Habitat を使用すると、ランタイムの依存関係が正確に表されるため、複雑なマイクロサービス指向のアプリケーションの継続的なデプロイが解決されます。 個々のコンポーネントの単純なブルー/グリーン デプロイにとどまらず、複雑なオーケストレーション フローを生成せずに高度なデプロイ動作を構築できます。

## <a name="run-any-application-anywhere"></a>任意の場所で任意のアプリケーションを実行
Habitat では、アプリケーションは任意のランタイム環境で変更せずに実行できます。 これには、ベア メタルや仮想マシンから、コンテナー (Docker など)、クラスター管理システム (Mesosphere や Kubernetes など)、PaaS システム (Pivotal Cloud Foundry) に至るすべてのものが含まれます。

## <a name="integrate-into-the-chef-devops-workflow"></a>Chef DevOps ワークフローとの統合
Habitat プロジェクトは Chef Software によるオープン ソース プロジェクトの 1 つであり、強力なサポート コミュニティがあります。 Habitat は、インフラストラクチャの自動化に関する Chef の豊富な経験を活用して、これまでにない自動化機能をアプリケーションに提供します。 Chef は Habitat の商用サポートを提供し、Habitat と Chef Automate 間のシームレスな統合を構築して、開発からデプロイまでのアプリケーション リリース サイクルを完全に自動化します。

## <a name="next-steps"></a>次の手順

* [Habitat を試す](https://www.habitat.sh/learn/)
