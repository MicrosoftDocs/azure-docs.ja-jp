---
title: Azure Service Fabric イメージ ストア接続文字列
description: イメージ ストア接続文字列について説明します。これには、使用方法や Service Fabric クラスターへの適用も含まれます。
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246199"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>ImageStoreConnectionString 設定を理解する

いくつかのドキュメントで、"ImageStoreConnectionString" パラメーターの存在について、それが何を意味するかの説明なしで、簡単に触れられています。 「[PowerShell を使用してアプリケーションのデプロイと削除を実行する][10]」などの記事を読み進めると、自分がすることは、ターゲット クラスターのクラスター マニフェストに表示される値をコピー/貼り付けすることだけのように思えます。 そして、この設定はクラスターごとに構成可能ではありますが、[Azure portal][11] を使用してクラスターを作成する場合はこの設定を構成するというオプションはなく、常に "fabric: ImageStore" が使用されます。 それでは、この設定は何のためにあるのでしょうか。

![クラスター マニフェスト][img_cm]

Service Fabric は、マイクロソフトの社内で数多くの多様なチームが使用するためのプラットフォームとして始まったため、いくつかの側面は高度にカスタマイズ可能であり、"イメージ ストア" もそのような側面の 1 つです。 本質的に、イメージ ストアは、アプリケーション パッケージを格納するためのプラグ可能なリポジトリです。 アプリケーションがクラスター内のノードにデプロイされると、そのノードは、イメージ ストアからアプリケーション パッケージの内容をダウンロードします。 ImageStoreConnectionString は、クライアントとノードが、特定のクラスター用の適切なイメージ ストアを検索するために必要なすべての情報を含む設定です。

現時点では、次の 3 つの可能な種類のイメージ ストア プロバイダーがあり、対応する接続文字列は次のようになります。

1. イメージ ストア サービス: "fabric:ImageStore"

2. ファイル システム: "file:[ファイルシステムのパス]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

実稼働環境で使用されるプロバイダーの種類は、イメージ ストア サービスです。これは、Service Fabric Explorer で確認できるステートフルな永続化システム サービスです。 

![イメージ ストア サービス][img_is]

システム サービス内のイメージ ストアをクラスター自体の内部でホストすることで、パッケージのリポジトリに関する外部依存関係が排除され、ストレージをもっとローカルに制御することができます。 イメージ ストアに関する今後の改善は、まずイメージ ストア プロバイダーを対象とする可能性がありますが、これのみに限定されるわけではありません。 クライアントは既にターゲット クラスターに接続されているために、イメージ ストア サービス プロバイダーの接続文字列には固有の情報はありません。 クライアントが知る必要があるのは、システム サービスを対象とするプロトコルを使用する必要があることだけです。

ファイル システム プロバイダーは、ローカルのワンボックス クラスターの開発時に、クラスターのブート処理を少しでも速くするためにイメージ ストア サービスの代わりに使用されます。 通常、その違いは小さいものですが、開発時には有効な最適化です。 ローカルのワンボックス クラスターを他の種類のストレージ プロバイダーでデプロイすることもできますが、プロバイダーが何であっても、開発/テスト ワークフローは変わらないため、通常はそうする理由がありません。 Azure Storage プロバイダーは、イメージ ストア サービス プロバイダーがリリースされる前にデプロイされた古いクラスターをサポートする目的でのみ存在します。

また、ファイル システム プロバイダーも Azure Storage プロバイダーも、複数のクラスター間で 1 つの Image Store を共有する手段として使うことは避けてください。それぞれのクラスターによって Image Store に競合するデータが書き込まれる可能性があるため、クラスターの構成データの破損につながります。 プロビジョニングしたアプリケーション パッケージを複数のクラスター間で共有する場合は、代わりに [sfpkg][12] ファイルを使用してください。このファイルは、ダウンロード URI で任意の外部ストアにアップロードすることができます。

したがって、ImageStoreConnectionString を構成することはできますが、単純に既定の設定を使用します。 Visual Studio から Azure に発行する場合、パラメーターは、状況に応じて自動的に設定されます。 Azure でホストされるクラスターにプログラムでデプロイする場合、接続文字列は常に "fabric:ImageStore" です。 不確かな場合は、[PowerShell](/powershell/module/servicefabric/get-servicefabricclustermanifest)、[.NET](/previous-versions/azure/reference/mt161375(v=azure.100))、または[REST](/rest/api/servicefabric/get-a-cluster-manifest) でクラスター マニフェストを取得することで、その値を常に検証できます。 オンプレミス テストと運用クラスターはどちらも、常にイメージ ストア サービス プロバイダーを使用するように構成する必要があります。

### <a name="next-steps"></a>次のステップ
[PowerShell を使用してアプリケーションのデプロイと削除を実行する][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
