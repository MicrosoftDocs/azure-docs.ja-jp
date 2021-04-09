---
title: Azure 上での IBM ワークロード | Microsoft Docs
description: メインフレーム エミュレーターや Microsoft パートナーからの他のサービスを利用して、Microsoft Azure を使用する IBM z/OS ワークロードをリホストします。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: d834b1fccf2951fffa8d00f4deb21434b9fea4ac
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955213"
---
# <a name="ibm-workloads-on-azure"></a>Azure 上での IBM ワークロード

z/OS に基づく多くの IBM メインフレーム ワークロードは、機能の損失を伴わず、かつ、ユーザーが基になるシステムの変更を意識しなくても、Azure 上でレプリケートすることが可能です。 Azure 上でアプリケーションをリホストすると、ユーザーが必要としているメインフレームのような機能を利用でき、さらには、弾力性、可用性、および潜在的なクラウドのコスト削減を実現できます。

Azure では、既存の IBM メインフレーム環境との統合をサポートしており、有用なアプリケーションの移行、必要に応じたハイブリッド ソリューションの実行、および経時的な移行を可能にします。 Azure に対する既存のメインフレームベース プログラムを完全に書き換えることは可能ですが、リホストするのがより一般的です。 書き換えた場合、コスト、複雑性、およびプロジェクトの移行にかかる時間が増大します。 リホストした場合、次のことが可能です。

- アプリケーションをクラウドベースのエミュレーターに移動する。

- データベースをクラウドベースのデータベースに移行する。

- コード変換エンジンを使用して、モジュールとコードを置き換える。

さらに、WebSphere や MQ を含む IBM ソフトウェアは現在、Azure Marketplace 内にあります。 IBM ソフトウェア用のライセンスがあれば、Azure が提供するオンデマンドのインフラストラクチャ スケーリングを活用して、仮想マシンを簡単に起動できます。

IBM メインフレーム システムを Azure へ移行するために、充実したパートナー エコシステムを利用できます。 ほとんどの場合は、アプリケーションの書き換えや置き換えという段階的なデプロイに着手する前に、できる限り、実用的な再利用の手法に従います。 パートナーからの詳しいガイダンスとヘルプについては、[Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)のページで入手してください。

**次の手順**

- [メインフレームの移行: 通説と事実](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上に IBM zD&T 開発/テスト環境をインストールする](./install-ibm-z-environment.md)
- [IBM zD&T v1 の Application Developers Controlled Distribution (ADCD) を設定する](./demo.md)
- [Azure 上の IBM DB2 pureScale](ibm-db2-purescale-azure.md)
