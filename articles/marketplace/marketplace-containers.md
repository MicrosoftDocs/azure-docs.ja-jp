---
title: Azure Marketplace でのコンテナー オファーの発行ガイド
description: この記事では、Azure Marketplace でコンテナー オファーを発行するための要件を説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95741663"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Azure Container オファーの発行ガイド

Azure Container オファーは、Azure Marketplace にコンテナー イメージを発行するのに役立ちます。 このガイドでは、このオファーの種類の要件について説明します。

Azure Container オファーは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション オファーです。 ユーザーに表示されるリスト オプションは、"今すぐ入手する" です。

対象となるソリューションが、Kubernetes ベースの Azure Container インスタンスとして設定される Docker コンテナー イメージであるときは、Azure Container オファーの種類を使用します。

> [!NOTE]
> Azure Container インスタンスは、Azure でコンテナーを実行する最も高速で最も簡単な方法を提供する実行時の docker インスタンスです。仮想マシンを管理したり、より高度なサービスを採用したりする必要はありません。 コンテナー インスタンスは、Azure に直接デプロイすることも、Azure Kubernetes Services または Azure Kubernetes Service Engine によって調整することもできます。  

Microsoft は現在、無料およびライセンス持ち込み (BYOL) のライセンス モデルをサポートしています。

## <a name="container-offer-requirements"></a>コンテナー オファーの要件

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | 無料または BYOL のどちらかの課金モデルをサポートします。<br><br> |  
| Dockerfile からビルドされたイメージ | コンテナー イメージは、Docker イメージの仕様に基づき、Dockerfile からビルドされる必要があります。<br> <br>Docker イメージのビルドに関する詳細については、「[Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage)」(Dockerfile リファレンス) の「Usage」(使用方法) セクションを参照してください。<br><br> |  
| Azure Container Registry リポジトリでのホスティング | コンテナー イメージは、Azure Container Registry リポジトリでホストされる必要があります。<br> <br>Azure Container Registry の操作の詳細については、[クイック スタート: Azure portal を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-portal.md)に関するページを参照してください。<br><br> |  
| イメージのタグ付け | コンテナー イメージには、少なくとも 1 つのタグを含める必要があります (タグの最大数: 16)。<br><br>イメージへのタグ付けに関する詳細については、[Docker ドキュメント](https://docs.docker.com/engine/reference/commandline/tag) サイトの `docker tag` に関するページを参照してください。<br><br> |  

## <a name="next-steps"></a>次のステップ

- コンテナー オファーの技術資産を準備するには、[Azure Container の技術資産の作成](create-azure-container-technical-assets.md)に関するページを参照してください。

- Azure Container オファーを作成するには、「[Azure Marketplace で Azure Container オファーを作成する](create-azure-container-offer.md)」を参照してください。
