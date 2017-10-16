---
title: About Azure DevTest Labs | Microsoft Docs
description: "Azure Virtual Machines を簡単に作成、管理、監視するために DevTest ラボを使用する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.openlocfilehash: 62e2d214d6d685c7f27c8c45cae161eb25ed1cbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs とは
## <a name="overview"></a>概要
開発者やテスト担当者は、クラウドへの移行による環境の作成および管理の遅延を解決する方法を求めています。  Azure を使用することで、環境の遅延の問題を解決し、新しいコスト効率の高い構造内でセルフ サービスを使用できます。  ただし、開発者やテスト担当者は、セルフ サービス環境の構成に、依然としてかなりの時間を費やす必要があります。 また、意思決定者は、クラウドを使用して、プロセス オーバーヘッドを過剰に追加することなく、コストの削減を最大化する方法について確信を持てていません。

Azure DevTest Labs は、無駄を最小限に抑え、コストを管理しつつ、Azure で迅速に環境を作成するためのサポートを開発者とテスト担当者に提供するサービスです。 再利用可能なテンプレートとアーティファクトを使用して Windows と Linux の環境を迅速にプロビジョニングすることで、アプリケーションの最新バージョンをテストできます。 デプロイ パイプラインと DevTest ラボを簡単に統合し、オンデマンドの環境をプロビジョニングします。 複数のテスト エージェントをプロビジョニングしてロード テストをスケール アップし、トレーニングおよびデモの事前プロビジョニング済み環境を作成します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

DevTest ラボを使用することで、クラウド内の開発者とテスト向けの環境の作成、構成、管理において次の利点が提供されます。

## <a name="worry-free-self-service"></a>心配無用のセルフサービス
DevTest ラボを使用することで、ユーザーごとの仮想マシン (VM) の数やラボあたりの VM の数など、ラボでのポリシーの設定が可能になり、コストの管理が容易になります。 DevTest ラボでは、VM を自動的にシャット ダウンし、起動するためのポリシーを作成することもできます。

## <a name="quickly-get-to-ready-to-test"></a>すばやくテスト準備完了状態へ
DevTest ラボを使用することで、チームがアプリケーションの開発とテストを開始するために必要なものがすべて事前にプロビジョニングされている環境を作成できます。 アプリケーションの最新の良好なビルドがインストールされた環境を要求し、作業を開始するだけです。 あるいは、コンテナーを使えば環境の作成が一層迅速かつ効率的になります。

## <a name="create-once-use-everywhere"></a>1 回作成すれば、どこでも使える
チーム内や組織内で環境テンプレートとアーティファクトのキャプチャと共有を行い、開発者とテスト向けの環境を簡単に作成できます。これらすべてをソース管理の制御のもとで行えます。

## <a name="integrates-with-your-existing-toolchain"></a>既存のツール チェーンとの統合
事前作成済みのプラグインや Microsoft の API を活用して、好みの継続的インテグレーション (CI) ツール、統合開発環境 (IDE)、自動リリース パイプラインから、開発/テスト環境のプロビジョニングを直接行うことができます。 Microsoft の包括的なコマンド ライン ツールを使用することもできます。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
[DevTest ラボの概念](devtest-lab-concepts.md)

