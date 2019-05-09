---
title: Azure DevTest Labs インフラストラクチャのガバナンス
description: この記事では、Azure DevTest Labs インフラストラクチャのガバナンスのためのガイダンスを提供します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436580"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Azure DevTest Labs インフラストラクチャのガバナンス - アプリケーションの移行と統合
開発/テスト ラボ環境が確立された後は、次の質問について考慮する必要があります。

- プロジェクト チーム内で環境をどのように利用しますか。
- どのようにして必要な組織のポリシーに確実に従い、アプリケーションに機敏に価値を追加できるようにしますか。

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace イメージとカスタム イメージ

### <a name="question"></a>質問
Azure Marketplace イメージと組織独自のカスタム イメージをどのように使い分ける必要がありますか。

### <a name="answer"></a>Answer
特定の問題または組織の要件がない限り、Azure Marketplace を既定で使用する必要があります。 問題や要件とは、たとえば次のようなものです。

- 基本イメージの一部としてアプリケーションを含める必要がある複雑なソフトウェアのセットアップ。
- アプリケーションのインストールとセットアップに多くの時間がかかる場合。Azure Marketplace イメージに追加される計算時間の効率的な使用ではありません。
- 開発者やテスト担当者が迅速に仮想マシンにアクセスする必要があり、新しい仮想マシンのセットアップ時間を最小限にしたい場合。
- コンプライアンスまたは法規制の条件 (たとえば、セキュリティ ポリシー) をすべてのマシンに適用する必要がある場合。

カスタム イメージの使用を軽々しく考えてはいけません。 基になっている基本イメージの VHD ファイルを管理する必要が生じるため、複雑さが増します。 また、基本イメージにソフトウェア更新プログラムを定期的に適用する必要があります。 これらの更新プログラムには、新しいオペレーティング システム (OS) の更新と、ソフトウェア パッケージ自体に必要なすべての更新または構成変更が含まれます。

## <a name="formula-vs-custom-image"></a>定型イメージとカスタム イメージ

### <a name="question"></a>質問
定型イメージとカスタム イメージをどのように使い分ける必要がありますか。

### <a name="answer"></a>Answer
通常、このシナリオでの決定要因はコストと再利用です。

基本イメージに多くのソフトウェアが追加されたイメージを多くのユーザー/ラボが必要とするシナリオでは、カスタム イメージを作成することによってコストを減らすことができます。 つまり、イメージを 1 回だけ作成します。 仮想マシンのセットアップ時間が短縮され、セットアップ時に仮想マシンの実行が原因で発生するコストが減ります。

ただし、注意する必要のあるもう 1 つの要素は、ソフトウェア パッケージに対する変更の頻度です。 ビルドを毎日実行し、ソフトウェアをユーザーの仮想マシンに展開する必要がある場合は、カスタム イメージではなく定型イメージの使用を検討してください。

## <a name="use-custom-organizational-images"></a>カスタム組織イメージを使用する

### <a name="question"></a>質問
カスタム組織イメージを DevTest Labs 環境に展開する簡単に反復可能なプロセスをセットアップするにはどうすればよいですか。

### <a name="answer"></a>Answer
[イメージ ファクトリ パターンに関するこちらのビデオ](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)をご覧ください。 これは高度なシナリオであり、提供されているスクリプトはサンプル スクリプトのみです。 何らかの変更が必要な場合は、環境で使用されるスクリプトを自分で管理および保守する必要があります。

DevTest Labs を使用して、Azure Pipelines でカスタムのイメージ パイプラインを作成します。

- [概要:Azure DevTest Labs でイメージ ファクトリを設定して数分で VM を準備する](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [イメージ ファクトリ – パート 2:Azure Pipelines とファクトリ ラボを設定して VM を作成する](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [イメージ ファクトリ – パート 3:カスタム イメージを保存して複数のラボに配布する](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [ビデオ: Azure DevTest Labs でのカスタム イメージ ファクトリ](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>ネットワーク構成を設定するパターン

### <a name="question"></a>質問
開発用とテスト用の仮想マシンがパブリック インターネットに接続できないようにするにはどうすればよいですか。 ネットワーク構成の設定に推奨されるパターンはありますか。

### <a name="answer"></a>Answer
はい。 受信トラフィックと送信トラフィックの 2 つの側面を考慮する必要があります。

**受信トラフィック** – 仮想マシンがパブリック IP アドレスを持っていない場合、その仮想マシンにはインターネットから到達できません。 ユーザーがパブリック IP アドレスを作成できないように、サブスクリプション レベルのポリシーを設定するのが、一般的なアプローチです。

**送信トラフィック** – 仮想マシンがパブリック インターネットに直接接続できないようにし、企業のファイアウォールをトラフィックが通過するよう強制するには、強制ルーティングを使用して、オンプレミスのトラフィックを ExpressRoute または VPN 経由でルーティングできます。

> [!NOTE]
> プロキシ設定のないトラフィックをブロックするプロキシ サーバーがある場合は、ラボの成果物ストレージ アカウントに対する例外を追加することを忘れないでください。

仮想マシンまたはサブネットに対してネットワーク セキュリティ グループを使用することもできます。 このステップにより、トラフィックを許可/ブロックする保護レイヤーが追加されます。

## <a name="new-vs-existing-virtual-network"></a>新規の仮想ネットワークと既存の仮想ネットワーク

### <a name="question"></a>質問
どのような場合に DevTest ラボ環境用の新しい仮想ネットワークを作成する必要があり、どのような場合に既存の仮想ネットワークを使用できますか。

### <a name="answer"></a>Answer
VM で既存のインフラストラクチャとやりとりする必要がある場合は、DevTest Labs 環境内の既存の仮想ネットワークの使用を検討する必要があります。 さらに、ExpressRoute を使用する場合は、サブスクリプションで割り当てられている IP アドレス空間がフラグメント化しないように、VNet/サブネットの量を最小限に抑えることが必要な場合があります。 また、ここでは VNET ピアリング パターンの使用も考慮する必要があります (ハブ - スポーク モデル)。 この方法では、特定のリージョン内のサブスクリプション間で VNET/サブネット通信できますが、リージョンをまただピアリングは将来の Azure ネットワークで可能になります。

それ以外の場合は、各 DevTest Labs 環境で専用の仮想ネットワークを使用できます。 ただし、サブスクリプションあたりの仮想ネットワークの数には[制限](../azure-subscription-service-limits.md)があることに注意してください。 既定の数は 50 ですが、この制限は 100 まで増やすことができます。

## <a name="shared-public-or-private-ip"></a>共有 IP アドレス、パブリック IP アドレス、プライベート IP アドレス

### <a name="question"></a>質問
共有 IP アドレス、パブリック IP アドレス、プライベート IP アドレスはどのように使い分ける必要がありますか。

### <a name="answer"></a>Answer
サイト間 VPN または ExpressRoute を使用する場合は、マシンが内部ネットワーク経由ではアクセスできてもパブリック インターネット経由ではアクセスできないように、プライベート IP アドレスの使用を検討します。

> [!NOTE]
> ラボの所有者は、このサブネット ポリシーを変更して、ユーザーが各自の VM に誤ってパブリック IP アドレスを作成できないようにすることができます。 サブスクリプションの所有者は、パブリック IP アドレスが作成されるのを防ぐサブスクリプション ポリシーを作成する必要があります。

共有パブリック IP アドレスを使用すると、ラボ内の仮想マシンはパブリック IP アドレスを共有します。 この方法は、特定のサブスクリプションに対するパブリック IP アドレスの制限が違反されるのを防ぐのに役立ちます。

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>ユーザー単位またはラボ単位の仮想マシンの数の制限

### <a name="question"></a>質問
ユーザーごと、またはラボごとに設定する必要がある仮想マシンの数に関するルールはありますか。

### <a name="answer"></a>Answer
ユーザーごと、またはラボごとの仮想マシンの数を検討するときは、3 つの主な懸案事項があります。

- ラボのリソースにチームが費やすことのできる**総コスト**。 多くのマシンを起動するのは簡単です。 コストを制御するための 1 つのメカニズムは、ユーザーごとおよびラボごとの VM の数を制限することです。
- ラボ内の仮想マシンの総数は、使用できる[サブスクリプション レベルのクォータ](../azure-subscription-service-limits.md)の影響を受けます。 上限値の 1 つは、サブスクリプションあたり 800 リソース グループです。 現在、DevTest Labs では (共有パブリック IP アドレスが使用されていない場合) VM ごとに新しいリソース グループが作成されます。 サブスクリプションに 10 個のラボがある場合、ラボごとの仮想マシンの数は約 79 になります (上限値 800 から、10 個のラボ自体のリソース グループの数 10 を引いた値)。
- たとえば、ラボが ExpressRoute 経由でオンプレミスに接続されている場合は、VNET/サブネット用に**使用可能な IP アドレス空間が定義されています**。 ラボ内の VM の作成が失敗しないようにするには (エラー: IP アドレスを取得できない)、ラボの所有者は、使用可能な IP アドレス空間に合わせて、ラボあたりの最大 VM 数を指定できます。

## <a name="use-resource-manager-templates"></a>Resource Manager テンプレートを使用する

### <a name="question"></a>質問
DevTest Labs 環境で Resource Manager テンプレートを使用するにはどうすればよいですか。

### <a name="answer"></a>Answer
[DevTest Labs での環境機能](devtest-lab-test-env.md)に関する記事で説明されている手順を使用して、DevTest Labs 環境に Resource Manager テンプレートをデプロイします。 基本的には、Resource Manager テンプレートを Git リポジトリ (Azure Repos または GitHub) にチェックインし、[テンプレート用のプライベート リポジトリ](devtest-lab-test-env.md)をラボに追加します。

このシナリオは、開発マシンをホストするために DevTest Labs を使用している場合は役に立たないことがありますが、運用環境の典型であるステージング環境を構築している場合は役立つことがあります。

また、ラボごとまたはユーザーごとの仮想マシン数のオプションは、ラボ自体でネイティブに作成されるマシンの数のみを制限し、任意の環境 (Resource Manager テンプレート) によって作成されるマシンの数は制限しないこと注意することも重要です。

## <a name="next-steps"></a>次の手順
[DevTest Labs での環境の使用](devtest-lab-test-env.md)に関するページをご覧ください。