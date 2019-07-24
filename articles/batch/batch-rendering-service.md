---
title: レンダリングの概要 - Azure Batch
description: Azure を使用したレンダリングと Azure Batch Rendering 機能の概要
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790342"
---
# <a name="rendering-using-azure"></a>Azure を使用したレンダリング

レンダリングとは、3D モデルを取得して 2D 画像に変換するプロセスです。 3D シーン ファイルの作成には、Autodesk 3DS Max、Autodesk Maya、Blender などのアプリケーションが使用されます。  Autodesk Maya、Autodesk Arnold、Chaos Group V-Ray、Blender Cycles などのレンダリング アプリケーションでは、2D 画像が生成されます。  単一の画像がシーン ファイルから作成されることもあります。 しかし、一般的には、複数の画像がモデル化およびレンダリングされた後、それがアニメーションに結合されます。

レンダリング ワークロードは、メディアおよびエンターテイメント業界で特殊効果 (VFX) によく使用されています。 また、広告、小売、石油とガス、製造など、他にもレンダリングが使用されている業界は多数あります。

レンダリングのプロセスは演算の負荷が大きく、生成されるフレームや画像が大量になるため、各画像をレンダリングするのに何時間もかかることがあります。  このため、レンダリングは、Azure および Azure Batch を利用して多数のレンダーを並列実行できる、最適なバッチ処理ワークロードです。

## <a name="why-use-azure-for-rendering"></a>レンダリングに Azure を使用する理由

レンダリングが Azure および Azure Batch に最適なワークロードである理由は多数あります。

* レンダリング ジョブを複数の処理に分割し、複数の VM を使って並列実行できる。
  * アニメーションは多数のフレームで構成され、各フレームを並列レンダリングできます。  各フレームの処理に使用できる VM が多いほど、すべてのフレームとアニメーションを速く生成できます。
  * レンダリング ソフトウェアの中には、1 つのフレームをタイルやスライスなどの複数の部分に分割できるものがあります。  各部分を個別にレンダリングし、すべてのレンダリングが完了した時点で、最終的な画像に結合できます。  使用できる VM が多いほど、フレームを速くレンダリングできます。
* レンダリング プロジェクトには膨大なスケールが必要。
  * 個々のフレームは複雑で、ハイエンドのハードウェアでもレンダリングに相当な時間がかかる場合があります。また、アニメーションは何十万というフレームで構成されていることもあります。  高品質のアニメーションを妥当な時間でレンダリングするには、膨大なコンピューティングが必要です。  場合によっては、100,000 を超えるコアが、数千のフレームを並列でレンダリングするために使用されています。
* レンダリング プロジェクトはプロジェクト ベースであり、さまざまな量のコンピューティングが必要。
  * 必要に応じてコンピューティングとストレージ容量を割り当て、プロジェクト中に負荷に応じてスケールアップまたはスケールダウンし、プロジェクトが完了したら削除します。
  * 割り当てられている容量に対して課金されます。プロジェクトとプロジェクトの間など、負荷がかかっていない容量については課金されません。
  * 予期しない変更によるバーストに対応します。プロジェクトの後半に予期しない変更があり、それらの変更を厳しいスケジュールで処理する必要がある場合は、スケールアップします。
* アプリケーション、ワークロード、および期間に従って、さまざまなハードウェアから選択可能。
  * Azure では様々なハードウェアが用意されており、Batch での割り当ておよび管理が可能です。
  * プロジェクトごとに、要件として最適な価格やパフォーマンスが求められる場合や、最適な全体的パフォーマンスが求められる場合があります。  シーンやレンダリング アプリケーションが違えば、メモリ要件も異なってきます。  レンダリング アプリケーションの中には、最高のパフォーマンスまたは特定の機能を実現するために GPU を活用するものもあります。 
* 低優先度 VM によるコスト削減。
  * 低優先度 VM により、通常のオンデマンド VM と比較して大幅な割引が実現します。これは一部のジョブの種類に適しています。
  * Azure Batch によって低優先度 VM を割り当てることができます。この Batch では、VM を柔軟に使用して広範な要件に対応できます。  Batch プールは専用 VM と低優先度 VM の両方で構成されます。VM の種類の組み合わせはいつでも変更できます。

## <a name="options-for-rendering-on-azure"></a>Azure でのレンダリング オプション

Azure ではさまざまな機能をレンダリング ワークロードに使用できます。  どの機能を使用するかは、既存の環境および要件によって異なります。

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>レンダー管理アプリケーションを使用した既存のオンプレミス レンダリング環境

最もよくあるのは、PipelineFX Qube、Royal Render、Thinkbox Deadline などのレンダー管理アプリケーションによって管理されている既存のオンプレミス レンダー ファームです。  要件は、Azure VM を使用してオンプレミス レンダー ファームの容量を拡張することです。

レンダー管理ソフトウェアには Azure サポートが組み込まれている場合と、Microsoft で Azure サポートを追加するプラグインを利用できるようにする場合があります。 サポートされているレンダー マネージャーおよび有効な機能の詳細については、[レンダー マネージャーの使用](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)に関する記事をご覧ください。

### <a name="custom-rendering-workflow"></a>カスタム レンダリング ワークフロー

要件は、VM で既存のレンダー ファームを拡張することです。  Azure Batch プールでは多数の VM を割り当てて、低優先度 VM の使用を許可し、フルプライスの VM とともに動的に自動スケーリングできます。また、一般的なレンダリング アプリケーションについては従量課金制のライセンスが用意されています。

### <a name="no-existing-render-farm"></a>既存のレンダー ファームなし

クライアント ワークステーションでレンダリングを実行できる場合もありますが、レンダリング ワークロードが増加してしまい、ワークステーションの能力を占有するには時間がかかり過ぎることがあります。  Azure Batch を使用すると、レンダー ファームのコンピューティングをオンデマンドで割り当てることも、レンダリング ジョブのスケジュールを Azure レンダー ファームに設定することもできます。

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch Rendering の機能

Azure Batch を使用すると、Azure で並列ワークロードを実行できます。  これにより、アプリケーションがインストールおよび実行されている VM を多数作成して管理することができます。  また、これらのアプリケーションのインスタンスを実行するための包括的なジョブ スケジュール機能も用意されており、VM へのタスクの割り当て、キュー登録、アプリケーション監視などが可能です。

多くのワークロードで Azure Batch を使用できますが、次の機能は、特にレンダリング ワークロードをよりすばやく簡単に実行するために利用できます。

* 事前インストールされたグラフィックスおよびレンダリング アプリケーションを含む VM イメージ:
  * Azure Marketplace VM イメージを使用でき、これには一般的なグラフィックスおよびレンダリング アプリケーションが含まれます。自分でアプリケーションをインストールしたり、アプリケーションがインストールされている独自のカスタム イメージを作成したりする必要はありません。 
* レンダリング アプリケーション用の従量課金制ライセンス:
  * コンピューティング VM に対する支払いに加え、アプリケーションへの分単位での課金を選択できます。これにより、ライセンスを購入したり、アプリケーション用にライセンス サーバーを構成したりする必要がなくなります。  また、従量課金制では、ライセンス数が固定されていないため、負荷の変化や予期しない負荷に対応することもできます。
  * 従量課金制ライセンスを使用せずに、事前インストールされたアプリケーションを、ご自身のライセンスで使用することもできます。 通常､このためには、オンプレミスまたは Azure ベースのライセンス サーバーをインストールし､Azure 仮想ネットワークを使用して、ライセンス サーバーにレンダリング プールを接続します。
* クライアント設計およびモデリング アプリケーション用のプラグイン:
  * プラグインを使用すると、エンドユーザーが、Autodesk Maya などのクライアント アプリケーションから直接 Azure Batch を利用して、プールの作成やジョブの送信を行うことができます。また、より多くのコンピューティング能力を活用すれば、レンダリング処理を高速化できます。
* レンダー マネージャーの統合:
  * Azure Batch がレンダー管理アプリケーションに統合されているか、プラグインを使用して Azure Batch 統合が提供されます。

Azure Batch は複数の方法で使用できますが、そのすべての方法が Azure Batch Rendering に適用されます。

* API:
  * [REST](https://docs.microsoft.com/rest/api/batchservice)、[.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch)、[Python](https://docs.microsoft.com/python/api/overview/azure/batch)、[Java](https://docs.microsoft.com/java/api/overview/azure/batch)、またはその他のサポートされている API を使用してコードを記述します。  開発者は、クラウドかオンプレミス ベースかにかかわらず、既存のアプリケーションまたはワークフローに Azure Batch 機能を統合できます。  たとえば、[Autodesk Maya プラグイン](https://github.com/Azure/azure-batch-maya)では、Batch の呼び出しに Batch Python API が使用され、プールの作成と管理、ジョブおよびタスクの送信、ステータスの監視が行われます。
* コマンドライン ツール:
  * [Azure コマンド ライン](https://docs.microsoft.com/cli/azure/)または[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) を使用すると、Batch の使用のスクリプトを作成できます。
  * 特に、Batch CLI テンプレートのサポートにより、プールの作成とジョブの送信が大幅に容易になります。
* UI:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) は、Batch アカウントを管理および監視することもできるクロス プラットフォーム クライアント ツールですが、Azure portal の UI よりも充実した機能がいくつか用意されています。  サポートされている各アプリケーション用にカスタマイズされた一連のプールおよびジョブ テンプレートがあり、このようなテンプレートを使うことで、プールの作成やジョブの送信を容易に行うことができます。
  * Azure portal を使用して、Azure Batch を管理および監視できます。
* クライアント アプリケーション プラグイン:
  * Batch Rendering をクライアント設計およびモデリング アプリケーション内から直接使えるようにするプラグインを使用できます。 このプラグインでは、主に、現在の 3D モデルに関するコンテキスト情報が含まれる Batch Explorer アプリケーションが呼び出されます。
  * 次のプラグインを使用できます。
    * [Azure Batch for Maya](https://github.com/Azure/azure-batch-maya)
    * [3DS Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Azure Batch Rendering の使用開始

Azure Batch Rendering を試すには、次の入門チュートリアルを参照してください。

* [Batch Explorer を使って Blender シーンをレンダリングする](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Batch CLI を使用して、Autodesk 3DS Max シーンをレンダリングする](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>次の手順

[こちらの記事](https://docs.microsoft.com/azure/batch/batch-rendering-applications)で、Azure Marketplace VM イメージに含まれているレンダリング アプリケーションとバージョンの一覧を確認します。