---
title: デプロイの概要 - Avere vFXT for Azure
description: この概要では、Avere vFXT for Azure クラスターをデプロイする方法について説明します。 具体的なデプロイ手順は、関連記事にあります。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271228"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - デプロイの概要

この記事では、Avere vFXT for Azure クラスターを起動して実行するために必要な手順の概要を示します。

Azure Marketplace から vFXT クラスターを作成する前後には、いくつかのタスクが必要となります。 開始から終了までのプロセスを明確に理解すると、必要な作業量の範囲を絞り込むことができます。

## <a name="deployment-steps"></a>デプロイメントの手順

[システムを計画](avere-vfxt-deploy-plan.md)した後は、自分の Avere vFXT クラスターの作成を開始できます。

Azure Marketplace の Azure Resource Manager テンプレートは、必要な情報を収集し、クラスター全体を自動的にデプロイします。

vFXT クラスターが稼働状態になった後も、使用前にいくつかの構成手順を実行する必要があります。 新しい Blob Storage コンテナーを作成した場合は、そこにデータを移動する必要があります。 NAS ストレージ システムを使用する場合は、クラスターの作成後に追加する必要があります。 クライアントをクラスターに接続します。

すべての手順の概要は次のとおりです。

1. 構成の前提条件

   VM を作成する前に、Avere vFXT プロジェクトに対して新しいサブスクリプションを作成し、サブスクリプションの所有権を構成し、クォータを確認して必要に応じて増やすように要求し、Avere vFXT ソフトウェアの使用に対する使用条件に同意する必要があります。 詳細な手順については、「[Avere vFXT の作成準備](avere-vfxt-prereqs.md)」を参照してください。

1. Avere vFXT クラスターを作成する

   Azure Marketplace を使用して、Azure クラスター用の Avere vFXT を作成します。 テンプレートが、必要な情報を収集し、最終的な製品を作成するスクリプトを実行します。

   クラスターの作成には、以下の手順が伴います。これらはすべて、マーケットプレースのテンプレートによって行われます。

   * (必要な場合) 新しいネットワーク インフラストラクチャとリソース グループを作成する
   * クラスター コントローラーを作成する

     クラスター コントローラーは、Avere vFXT クラスターと同じ仮想ネットワークにあるシンプルな VM で、クラスターの作成と管理のために必要なカスタム ソフトウェアを備えています。 コントローラーによって vFXT ノードが作成され、クラスターが形成されます。また、その有効期間中にクラスターを管理するために、コマンド ライン インターフェイスも提供されます。

     デプロイ中に新しい仮想ネットワークまたはサブネットを作成すると、コントローラーがパブリック IP アドレスを持ちます。 つまり、仮想ネットワークの外部からクラスターに接続するためのジャンプ ホストとしてコントローラーを使用できます。

   * クラスター ノード VM を作成する

   * 個々のノードからクラスターを作成する

   * 必要に応じて、新しい BLOB コンテナーを作成し、クラスター用バックエンド ストレージとして構成する

   クラスターの作成の詳細については、「[vFXT クラスターのデプロイ](avere-vfxt-deploy.md)」を参照してください。

1. クラスターを構成する

   Avere vFXT の構成インターフェイス (Avere Control Panel) に接続して、クラスターの設定をカスタマイズします。 サポートの監視を許可し、ハードウェア ストレージまたは追加の BLOB コンテナーを使用している場合は、ご利用のストレージ システムを追加します。

   * [vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)
   * [サポートの有効化](avere-vfxt-enable-support.md)
   * [ストレージの構成](avere-vfxt-add-storage.md) (必要に応じて)

1. クライアントをマウントする

   負荷分散について、およびクライアント マシンでクラスターをマウントする方法については、「[Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md)」 (Avere vFXT クラスターをマウントする) のガイドラインに従います。

1. データの追加 (必要に応じて)

   Avere vFXT はスケーラブルな複数のクライアントのキャッシュであるため、新しいバックエンド ストレージ コンテナーにデータを移動する最適な方法は、複数のクライアント (マルチスレッドのファイル コピーの戦略) を使用することです。

   ワーキング セットのデータを新しい BLOB コンテナーまたはその他のバックエンド ストレージ システムに移動する必要がある場合は、「[vFXT クラスターへのデータの移動](avere-vfxt-data-ingest.md)」の指示に従ってください。

## <a name="next-steps"></a>次のステップ

「[Avere vFXT の作成を準備する](avere-vfxt-prereqs.md)」に進み、前提条件となるタスクを完了します。
