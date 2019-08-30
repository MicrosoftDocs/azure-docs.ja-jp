---
title: Windows Virtual Desktop プレビューとは  - Azure
description: Windows Virtual Desktop プレビューの概要です。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 8827aa660d348ef9e42c617067c2837b8697ba6e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876777"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Windows Virtual Desktop プレビューとは 

このたびパブリック プレビューを迎えた Windows Virtual Desktop プレビューは、クラウド上で運営されるデスクトップとアプリの仮想化サービスです。

Azure 上の Windows Virtual Desktop でできることは次のとおりです。

* マルチ セッションの Windows 10 デプロイを設定し、スケーラビリティを備えた完全版の Windows 10 を提供
* Office 365 ProPlus を仮想化および最適化し、マルチ ユーザーの仮想シナリオで運用
* Windows 7 仮想デスクトップに無料の延長セキュリティ更新プログラムを提供
* 既存のリモート デスクトップ サービス (RDS) と Windows Server のデスクトップやアプリをあらゆるコンピューターで利用
* デスクトップとアプリの両方を仮想化
* 統一的な管理エクスペリエンスを使って Windows 10、Windows Server、Windows 7 のデスクトップとアプリを管理

## <a name="introductory-video"></a>紹介ビデオ

Windows Virtual Desktop について、その独自性や新機能を、このビデオで学習してください。

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Windows Virtual Desktop に関するその他のビデオについては、[ プレイリスト ](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev) を参照してください。

## <a name="key-capabilities"></a>主な機能

Windows Virtual Desktop を使うと、スケーラブルで柔軟な環境を設定することができます。

* ゲートウェイ サーバーを追加で稼働させることなく、Azure サブスクリプション内に完全な仮想デスクトップ環境を作成できます。
* 多種多様なワークロードに対応するうえで必要なホスト プールをいくつでも公開できます。
* Azure ギャラリーから独自のイメージを取得して運用ワークロードに使用したり、テストしたりできます。
* プールしたマルチ セッション リソースを使ってコストを節約できます。 Windows Virtual Desktop 限定の新しい Windows 10 Enterprise マルチ セッション機能と、Windows Server 上のリモート デスクトップ セッション ホスト (RDSH) ロールを併用すれば、ユーザーにこれまでと同じリソースを提供しつつ、仮想マシンの数やオペレーティング システム (OS) のオーバーヘッドを大幅に削減できます。
* (永続的な) 個人用デスクトップを提供できるので、個々人が自分のマシンを保有しているような感覚を持たせることができます。

仮想デスクトップをデプロイおよび管理できます。

* ホスト プールの構成、アプリ グループの作成、ユーザーの割り当て、リソースの公開には、Windows Virtual Desktop の PowerShell インターフェイスと REST インターフェイスを利用できます。
* 単一のホスト プールから完全版のデスクトップと個々のリモート アプリのどちらも公開できます。また、ユーザー グループに応じてアプリ グループを個別に作成できるほか、ユーザーを複数のアプリ グループに割り当ててイメージの数を削減することも可能です。
* 環境の管理では、ロールの割り当てにあらかじめ用意されている委任アクセス権を使用したり、診断情報を収集してさまざまな構成やユーザー エラーに対する理解を深めたりすることができます。
* 新しい診断サービスを使ってエラーのトラブルシューティングが可能です。
* 管理するのはイメージと仮想マシンのみです。インフラストラクチャの管理は必要ありません。 お使いの Azure サブスクリプション内の仮想マシンだけを管理すれば良いので、リモート デスクトップ サービスの場合のように、リモート デスクトップ関連のロールを個人で管理する必要がありません。

このほか、仮想デスクトップにユーザーを割り当てて接続することもできます。

* 割り当てられたユーザーは、Windows Virtual Desktop クライアントを起動して、公開されている Windows デスクトップやアプリケーションに接続できるようになります。 デバイスにインストールされているネイティブ アプリケーションと Windows Virtual Desktop HTML5 Web クライアントのどちらかを使って、あらゆるデバイスから接続できます。
* サービスには逆接続を使って安全にユーザーを接続するので、受信ポートを開く必要が一切ありません。

## <a name="requirements"></a>必要条件

Windows Virtual Desktop を設定し、ユーザーを Windows のデスクトップやアプリケーションに正常に接続するうえで必要なことがいくつかあります。

以下の OS のサポートが追加される予定になっています。デプロイする予定のデスクトップとアプリに基づいて、ユーザーのための [ 適切なライセンス ](https://azure.microsoft.com/pricing/details/virtual-desktop/) があることを確認してください。

|OS|必要とされるライセンス|
|---|---|
|Windows 10 Enterprise マルチ セッションまたは Windows 10 Enterprise|Microsoft 365 E3、E5、A3、A5、F1、Business<br>Windows E3、E5、A3、A5|
|Windows 7 Enterprise |Microsoft 365 E3、E5、A3、A5、F1、Business<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|ソフトウェア アシュアランス付きの RDS クライアント アクセス ライセンス (CAL)|

インフラストラクチャが Windows Virtual Desktop をサポートするうえで必要なものは次のとおりです。

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Azure Active Directory と同期している Windows Server Active Directory。 これは、次のいずれかを使うことにより可能になります。
  * Azure AD Connect
  * Azure AD Domain Services
* Azure サブスクリプション (Windows Server Active Directory を含むか、Windows Server Active Directory に接続している仮想ネットワークが含まれるもの)
  
Windows Virtual Desktop 用に作成する Azure 仮想マシンに必要な条件は次のとおりです。

* [ 標準ドメイン参加済み ](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) または [Hybrid AD 参加済み ](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan) であること。 仮想マシンを Azure AD に参加させることはできません。
* 次のいずれかの [ サポート対象となる OS イメージ ](#supported-virtual-machine-os-images) を実行していること。

>[!NOTE]
>Azure サブスクリプションが必要な場合には、[1 か月間の無料試用版にサインアップ ](https://azure.microsoft.com/free/) できます。 無料試用版の Azure をご利用中の場合には、Windows Server Active Directory が Azure Active Directory と同期した状態を保つことができるよう、Azure AD Domain Services を使用する必要があります。

Windows Virtual Desktop の構成要素には、お客様がユーザーに配信する Windows のデスクトップとアプリのほか、Microsoft が Azure 上でサービスとしてホストしている管理ソリューションがあります。 パブリック プレビューの期間中は、デスクトップとアプリは任意の Azure リージョンの仮想マシン (VM) 上にデプロイできるのに対して、管理ソリューションと VM 関連のデータはどちらも、米国 (米国東部 2 リージョン) でホストされます。 このため、パブリック プレビュー期間中にサービスをテストするときは、米国を宛先とするデータ転送が発生することがあります。 管理ソリューションとデータを Azure の全リージョンにローカライズして展開していく作業は、一般提供が始まってから開始する予定です。

最適なパフォーマンスを実現するために、お使いのネットワークが次の要件を満たしていることを確認してください。

* クライアントのネットワークからホスト プールをデプロイした Azure リージョンまでのラウンドトリップ (RTT) 待ち時間は、150 ミリ秒を下回っている必要があります。
* デスクトップとアプリをホストしている VM が管理サービスに接続する際には、ネットワーク トラフィックが国/リージョン外に流れることがあります。
* ネットワークのパフォーマンスを最適化するために、セッション ホストの VM を管理サービスと同じ Azure リージョンに配置することをお勧めします。

## <a name="supported-remote-desktop-clients"></a>サポートされているリモート デスクトップ クライアント

次のリモート デスクトップ クライアントは、Windows Virtual Desktop をサポートします。

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>サポートされている仮想マシン OS イメージ

Windows Virtual Desktop は、次の OS イメージをサポートします。

* Windows 10 (Enterprise マルチ セッション)
* Windows Server 2016

## <a name="next-steps"></a>次の手順

まずは、テナントを作成する必要があります。 テナントの作成方法については、テナント作成に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Windows Virtual Desktop プレビューでテナントを作成する ](tenant-setup-azure-active-directory.md)
