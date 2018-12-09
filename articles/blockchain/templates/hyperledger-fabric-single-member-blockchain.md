---
title: Hyperledger Fabric コンソーシアム
description: Hyperledger Fabric コンソーシアム ソリューション テンプレートを使用し、シングル メンバー ネットワークをデプロイし、構成します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: c08557156848d4e7fcf0b1adbe6c8faa4ee00c82
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231374"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric シングル メンバー ネットワーク

Hyperledger Fabric コンソーシアム ソリューション テンプレートを使用し、Hyperledger Fabric シングル メンバー (マルチノード) ネットワークをデプロイし、構成できます。

この記事を読むと、次のことができます。

- ブロックチェーン、Hyperledger Fabric、より複雑なコンソーシアム ネットワーク アーキテクチャの実用的知識を得る
- Azure Portal 内からシングル メンバー Hyperledger Fabric コンソーシアム ネットワークをデプロイし、構成する方法を学習する

## <a name="about-blockchain"></a>ブロックチェーンの概要

このソリューション テンプレートは、ブロックチェーン コミュニティに参加したばかりの方に最適です。ぜひこの機会にこの技術を学習してください。Azure で構成可能な方法で楽に学習できます。 ブロックチェーンは Bitcoin の背後にある基礎技術ですが、仮想通貨を可能にするだけの技術ではありません。 既存のデータベース、分散システム、暗号化技術を合わせたものであり、マルチパーティ秘密計算 (Secure Multi-party Computation) を可能にします。不変性、検証可能性、監査可能性、攻撃に対する回復力が約束されます。 さまざまなプロトコルでさまざまなメカニズムを採用することで、このような特性が与えられます。 [Hyperledger Fabric](https://github.com/hyperledger/fabric) はこのようなプロトコルの 1 つです。

## <a name="consortium-architecture-on-azure"></a>Azure のコンソーシアム アーキテクチャ

このテンプレートは、単一組織 (シングル メンバー) 内でのユーザーの運用環境をテストし、シミュレーションするトポロジをデプロイします。 このデプロイは、じきにマルチ リージョンに拡大される、シングル リージョンのマルチノード ネットワークで構成されます。

このネットワークは次の 3 種類のノードから成ります。

1. **Member ノード**: ネットワークのメンバーを登録し、管理する Fabric メンバーシップ サービスを実行するノード。 このノードは拡張性と高可用性のためにクラスター化できます。ただし、この演習ではシングル メンバー ノードが使用されます。
2. **Orderer ノード**: トータル オーダー ブロードキャストやアトミック トランザクションなど、配信保証を実装する通信サービスを実行するノード。
3. **Peer ノード**: トランザクションをコミットし、分散型台帳の状態とコピーを保守管理するノード。

## <a name="getting-started"></a>使用の開始

始めに、いくつかの仮想マシンと標準のストレージ アカウントをデプロイできる Azure サブスクリプションが必要になります。 Azure サブスクリプションをお持ちでない場合、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成できます。

既定では、ほとんどの種類のサブスクリプションで、割り当てを増やさなくても小規模のデプロイ トポロジを利用できます。 1 つのメンバーに対する最も小規模のデプロイには、次が必要になります。

- 5 つの仮想マシン (5 つのコア)
- 1 つの VNet
- 1 つのロード バランサー
- 1 つのパブリック IP アドレス

サブスクリプションを得たら、[Azure Portal](https://portal.azure.com) に進みます。 **+** を選択し、**[ブロックチェーン]** を選択し、**[Hyperledger Fabric Single Member Blockchain]\(Hyperledger Fabric シングル メンバー ブロックチェーン\)** を選択します。

![Hyperledger Fabric シングル メンバー ブロックチェーン Marketplace テンプレート](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Deployment

始めに、**[Hyperledger Fabric Single Member Blockchain]\(Hyperledger Fabric シングル メンバー ブロックチェーン\)** を選択し、**[作成]** をクリックして、ウィザードの **[基本]** ブレードを開きます。

テンプレート デプロイでは、マルチノード ネットワークを段階的に構成できます。 デプロイ フローは、基本、ネットワーク構成、Fabric 構成の 3 段階に分かれています。

### <a name="basics"></a>基本

**[基本]** ブレードで、デプロイの標準パラメーターの値を指定します。 たとえば、サブスクリプション、リソース グループ、基本仮想マシンのプロパティを指定します。

![基本](./media/hyperledger-fabric-single-member-blockchain/basics.png)

パラメーター名| 説明| 使用できる値|既定値
---|---|---|---
**[リソース プレフィックス]**| デプロイされたリソースの命名規則の基礎として使用される文字列。|6 文字以下|該当なし
**[VM ユーザー名]**| このメンバーに対してデプロイされている各仮想マシンの管理者のユーザー名。|1 から 64 文字|azureuser
**認証の種類**| 仮想マシンに対して認証する方法。|[パスワード] または [SSH 公開キー]|パスワード
**[パスワード] ([認証の種類] = [パスワード])**|デプロイされた各仮想マシンの管理者アカウントのパスワード。 パスワードには、大文字、小文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります。<br /><br />VM にはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます。|12 から 72 文字|該当なし
**[SSH キー] ([認証の種類] = [公開キー])**|リモート ログインに使用される Secure Shell キー。||該当なし
**[Restrict access by IP address]\(IP アドレスごとにアクセスを制限する\)**|クライアント エンドポイント アクセスが制限されるかどうかを決定する設定。|はい/いいえ| いいえ 
**[Allowed IP address or subnet]\(許可される IP アドレスまたはサブネット\) ([Restrict access by IP address]\(IP アドレスごとにアクセスを制限する\) = [はい])**|アクセス制御が有効になっているとき、クライアント エンドポイントへのアクセスが許可される IP アドレスまたは一連の IP アドレス。||該当なし
**サブスクリプション** |デプロイ対象のサブスクリプション。
**リソース グループ** |コンソーシアム ネットワークをデプロイするリソース グループ||該当なし
**場所** |最初のメンバー**のネットワーク フットプリントをデプロイする Azure リージョン。

### <a name="network-size-and-performance"></a>Network size and performance (ネットワークのサイズとパフォーマンス)

次に、**[Network Size and Performance]\(ネットワークのサイズとパフォーマンス\)** で、コンソーシアム ネットワークのサイズを入力します。 たとえば、Membership ノード、Orderer ノード、Peer ノードの数を指定します。 インフラストラクチャ オプションと仮想マシンのサイズを選択します。

![Network size and performance (ネットワークのサイズとパフォーマンス)](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

パラメーター名| 説明| 使用できる値|既定値
---|---|---|---
**[Number of Membership Nodes]\(Membership ノードの数\)**|メンバーシップ サービスを実行するノードの数。 メンバーシップ サービスの詳細については、[Hyperledger 文書](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf)の「Security & Membership Services」(セキュリティ & メンバーシップ サービス) をご覧ください。<br /><br />現在のところ、この値は 1 ノードに制限されていますが、次の見直しでクラスタリングを利用した拡張に対応する予定です。|1| 1
**[Number of Orderer Nodes]\(Orderer ノードの数\)** |トランザクションを整理してブロックにするノードの数。--> この一文は冗漫であり、わかりにくいです。 ordering サービスの詳細については、[Hyperledge 文書](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html)をご覧ください。<br /><br />現在のところ、この値は 1 ノードに制限されています。 |1 |1
**[Number of Peer Nodes]\(Peer ノードの数\)**| トランザクションを実行し、台帳の状態とコピーを保守管理するノード。コンソーシアム メンバーが所有しています。<br /><br />ordering サービスの詳細については、[Hyperledge 文書](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)をご覧ください。|3| 3 - 9
**[ストレージのパフォーマンス]**|デプロイされた各ノードをバックアップするストレージの種類。 ストレージの詳細については、[Microsoft Azure Storage の概要](https://docs.microsoft.com/azure/storage/common/storage-introduction)ページと [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) に関するページを参照してください。|Standard または Premium|標準
**[仮想マシンのサイズ]** |ネットワークの全ノードで使用される仮想マシンのサイズ|Standard A、<br />Standard D、<br />Standard D-v2、<br />Standard F シリーズ、<br />Standard DS、<br />Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Fabric 固有の設定

最後に、**[Fabric Settings]\(Fabric 設定\)** で、Fabric 関連の構成設定を指定します。

![Fabric 設定](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

パラメーター名| 説明| 使用できる値|既定値
---|---|---|---
**[Bootstrap User Name]\(ブートストラップ ユーザー名\)**| デプロイされたネットワークでメンバー サービスに登録される、最初の承認されたユーザー。|9 文字以下|admin
**[Bootstrap User Password for Fabric CA]\(Fabric CA のブートストラップ ユーザー パスワード\)**|Membership ノードにインポートされた Fabric CA アカウントを守るために使用される管理者パスワード。<br /><br />パスワードには、大文字、小文字、数字をそれぞれ 1 つ以上含める必要があります。|12 文字以上|該当なし

### <a name="deploy"></a>デプロイ

**[概要]** で、指定した入力を確認し、基本的なデプロイ前検証を実行します。

![まとめ](./media/hyperledger-fabric-single-member-blockchain/summary.png)

法律条項とプライバシー条項を確認し、**[購入]** をクリックしてデプロイします。 デプロイ時間は、プロビジョニングされる VM の数に基づき、数分から数十分かかります。

### <a name="accessing-nodes"></a>ノードへのアクセス

デプロイが完了すると、**[概要]** が表示されます。

![デプロイメント](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

この画面が自動的に表示されない場合 (デプロイの実行中に管理ポータル内を移動したなどの理由が考えられます)、左側にあるナビゲーション バーの [リソース グループ] タブからいつでも表示できます。 手順 1 で入力したリソース グループ名をクリックし、**[概要]** ページに進みます。

[概要] には、ソリューション テンプレートでデプロイされた全リソースが一覧表示されます。 リソースは自由に閲覧できますが、この画面からは、テンプレートによって生成された _出力パラメーター_ にアクセスすることもできます。 Hyperledger Fabric ネットワークに接続するとき、この出力パラメーターから役に立つ情報が得られます。

出力パラメーターにアクセスするには、最初に [リソース グループ] ブレードの **[デプロイ]** タブをクリックします。 デプロイ履歴が表示されます。

![デプロイ履歴](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

デプロイ履歴から、一覧にある最初のデプロイをクリックすると詳細が表示されます。

![デプロイ詳細](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

詳細画面にはデプロイの概要が表示されます。概要の下には、役に立つ出力パラメーターが 3 つ表示されます。

- _API-ENDPOINT_ は、ネットワークでアプリケーションをデプロイすると使用できます。
- _PREFIX_ は _デプロイ プレフィックス_ とも呼ばれていますが、リソースとデプロイを一意に識別するものです。 コマンドライン ベースのツールを使用するときに利用されます。
- _SSH-TO-FIRST-VM_ には、事前アセンブルされた ssh コマンドと、ネットワークの最初の VM に接続するために必要なすべてのパラメーターが表示されます。この最初の VM は、Hyperledger Fabric の場合、Fabric-CA ノードになります。

指定した管理者ユーザー名とパスワード/SSH キーを利用し、SSH 経由で各ノードの仮想マシンにリモート接続できます。 ノード VM には独自のパブリック IP アドレスがないため、ロード バランサーを通過し、ポート番号を指定する必要があります。 最初のトランザクション ノードにアクセスするための SSH コマンドは、3 つ目のテンプレート出力、**SSH-TO-FIRST-VM (サンプル デプロイの場合: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`) です。 トランザクション ノードを追加するには、ポート番号を 1 ずつ増やします (たとえば、最初のトランザクション ノードがポート 3000、2 番目が 3001、3 番目が 3002 のようになります)。

## <a name="next-steps"></a>次の手順

これで、Hyperledger コンソーシアム ブロックチェーン ネットワークに対するアプリケーションとチェーンコードの開発に集中的に取り組むことができます。
