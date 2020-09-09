---
title: レンダリングの機能
description: Azure Batch の標準の機能は、レンダリングのワークロードとアプリの実行に使用されます。 Batch には、レンダリングのワークロードをサポートする特定の機能が含まれています。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 6e34e0ef9035882a32ff46222686db4a948d7997
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957462"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch Rendering の機能

Azure Batch の標準の機能は、レンダリングのワークロードとアプリケーションの実行に使用されます。 また、Batch にはレンダリングのワークロードをサポートする特定の機能が含まれています。

プール、ジョブ、タスクなどの Batch の概念の概要については、[こちらの記事](./batch-service-workflow-features.md)をご覧ください。

## <a name="batch-pools"></a>Batch プール

### <a name="rendering-application-installation"></a>レンダリング アプリケーションのインストール

VM イメージをレンダリングする Azure Marketplace は、事前インストールされたアプリケーションを使用する必要がある場合にのみ、プール構成で指定できます。

Windows 2016 のイメージと CentOS のイメージがあります。  [Azure Marketplace](https://azuremarketplace.microsoft.com) で、VM イメージは「batch rendering」と検索することで見つかります。

プール構成の例については、[Azure CLI でのレンダリングのチュートリアル](./tutorial-rendering-cli.md)をご覧ください。  Azure portal と Batch Explorer には、プールを作成するときにレンダリングする VM イメージを選択する、GUI ツールが用意されています。  Batch API を使用している場合は、プールの作成時に [ImageReference](/rest/api/batchservice/pool/add#imagereference) に次のプロパティ値を指定します。

| Publisher | プラン | Sku | Version |
|---------|---------|---------|--------|
| batch (バッチ) | rendering-centos73 | rendering | latest |
| batch (バッチ) | rendering-windows2016 | rendering | latest |

プールの VM で追加のアプリケーションが要求されている場合は、その他のオプションを使用できます。

* Shared Image Gallery のカスタム イメージ
  * このオプションを使用すると、必要な目的のアプリケーションと特定のバージョンで VM を構成できます。 詳細については、「[Shared Image Gallery を使用してプールを作成する](batch-sig-images.md)」を参照してください。 Autodesk と Chaos Group では Azure Batch のライセンス サービスに対する検証を行うために Arnold と V-Ray をそれぞれ修正していることに注意してください。 所有しているアプリケーションがこのサポートに対応するバージョンであることを確認してください。対応していない場合、従量課金ライセンスが動作しません。 現在のバージョンの Maya と 3ds Max は、ヘッドレス (バッチ/コマンドライン モード) で実行されている場合、ライセンス サーバーを必要としません。 このオプションの使用方法について不明な点がある場合は、Azure サポートにお問い合わせください。
* [アプリケーション パッケージ](./batch-application-packages.md):
  * 1 つまたは複数の ZIP ファイルを使用してアプリケーション ファイルをパッケージ化し、Azure portal でアップロードして、プール構成でそのパッケージを指定します。 プール のVM が作成されると、ZIP ファイルがダウンロードされ、ファイルが抽出されます。
* リソース ファイル:
  * アプリケーション ファイルが Azure BLOB ストレージにアップロードされ、[プールの開始タスク](/rest/api/batchservice/pool/add#starttask)でファイルの参照を指定します。 プールの VM が作成されると、リソース ファイルが各 VM にダウンロードされます。

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>事前インストールされているアプリケーションの従量課金ライセンス

使用されライセンス料金が発生するアプリケーションは、プール構成で指定する必要があります。

* [プールの作成](/rest/api/batchservice/pool/add#request-body)時に `applicationLicenses` プロパティを指定します。  文字列の配列 "vray"、"arnold"、"3dsmax"、"maya" で、次の値を指定できます。
* 1 つまたは複数のアプリケーションを指定すると、それらのアプリケーションのコストが VM のコストに追加されます。  アプリケーションの料金は、[Azure Batch の料金ページ](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)で確認できます。

> [!NOTE]
> レンダリング アプリケーションを使用するためにライセンス サーバーに接続しない場合は、`applicationLicenses`プロパティを指定しないでください｡

Azure portal または Batch Explorer を使用してアプリケーションを選択し、そのアプリケーションの料金を表示できます。

プールの構成設定の `applicationLicenses` プロパティでアプリケーションを指定しないで､アプリケーションを使用しようとした場合、あるいはライセンス サーバーにアクセスできなかった場合､アプリケーションの実行はライセンス エラーとゼロ以外の終了コードで失敗します。

### <a name="environment-variables-for-pre-installed-applications"></a>事前インストールされているアプリケーションの環境変数

レンダリング タスクのコマンドラインを作成できるようにするには、レンダリング アプリケーションの実行可能ファイルのインストール場所を指定する必要があります。  Azure Marketplace の VM イメージにシステム環境変数が作成されており、これを実際のパスを指定する代わりに使用できます。  これらの環境変数は、[Batch の標準の環境変数](./batch-compute-node-environment-variables.md)に加えてタスクごとに作成されます。

|Application|アプリケーション実行可能ファイル|環境変数|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 コマンド ライン|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 コマンド ライン|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM のファミリ

他のワークロードと同様に、レンダリング アプリケーションのシステム要件はさまざまで、ジョブとプロジェクトによってパフォーマンス要件は異なります。  Azure では、最小のコスト、価格とパフォーマンスの最適なバランス、最高のパフォーマンスなど、ユーザーの要件に応じて各種 VM ファミリを利用できます。
Arnold などの一部のレンダリング アプリケーションは CPU ベースです。V-Ray や Blender Cycles は CPU か GPU、またはその両方を使用する場合があります。
利用可能な VM ファミリと VM のサイズについて詳しくは、「[VM の種類とサイズ](../virtual-machines/windows/sizes.md)」をご覧ください。

### <a name="low-priority-vms"></a>優先順位の低い VM

他のワークロードと同様に、Batch のプールでレンダリング用に優先順位の低い VM を利用できます。  優先順位の低い VM は通常の専用の VM と同じパフォーマンスを発揮しますが、Azure の余剰キャパシティを使用し、大幅な割引が適用されます。  優先順位の低い VM を使用するデメリットは、利用可能な容量によっては、これらの VM が割り当てられなかったりいつでも割り込まれたりする可能性がある点です。 この理由から、優先順位の低い VM はすべてのレンダリング ジョブには適しません。 たとえば、イメージのレンダリングに数時間かかる場合、VM が先取りされること が原因でそれらのイメージのレンダリングに中断や再起動が発生することは受け入れられない可能性があります。

優先順位の低い VM の特性と Batch を使用してそれらを構成するさまざまな方法について詳しくは、「[Batch で優先順位の低い VM を使用する](./batch-low-pri-vms.md)」をご覧ください。

## <a name="jobs-and-tasks"></a>ジョブとタスク

ジョブやタスクでレンダリング固有のサポートは不要です。  主な構成項目はタスク コマンドラインであり、必要なアプリケーションを参照する必要があります。
Azure Marketplace の VM イメージが使用されている場合は、ベスト プラクティスとして、環境変数を使用してパスとアプリケーション実行可能ファイルを指定します。

## <a name="next-steps"></a>次のステップ

Batch レンダリングの例については、次の 2 つのチュートリアルをお試しください。

* [Azure CLI を使用したレンダリング](./tutorial-rendering-cli.md)
* [Batch Explorer を使用したレンダリング](./tutorial-rendering-batchexplorer-blender.md)
