---
title: Windows Virtual Desktop プレビューとは  - Azure
description: Windows Virtual Desktop プレビューの概要です。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6385838064c408ccfa23dacbd5785f8e82f3cc8b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049440"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Windows Virtual Desktop プレビューとは 

このたびパブリック プレビューを迎えた Windows Virtual Desktop プレビューは、クラウド上で運営されるデスクトップとアプリの仮想化サービスです。

Azure 上の Windows Virtual Desktop でできることは次のとおりです。

* マルチセッションの Windows 10 デプロイを設定し、スケーラビリティを備えた完全版の Windows 10 を提供
* Office 365 ProPlus を仮想化および最適化し、マルチユーザーの仮想シナリオで運用
* Windows 7 仮想デスクトップに無料の延長セキュリティ更新プログラムを提供
* 既存のリモート デスクトップ サービス (RDS) と Windows Server のデスクトップやアプリをあらゆるコンピューターで利用
* デスクトップとアプリの両方を仮想化
* 統一的な管理エクスペリエンスを使って Windows 10、Windows Server、Windows 7 のデスクトップとアプリを管理

## <a name="key-capabilities"></a>主な機能

Windows Virtual Desktop を使うと、スケーラブルで柔軟な環境を設定することができます。

* ゲートウェイ サーバーを追加で稼働させることなく、Azure サブスクリプション内に完全な仮想デスクトップ環境を作成できます。
* 多種多様なワークロードに対応するうえで必要なホスト プールをいくつでも公開できます。
* Azure ギャラリーから独自のイメージを取得して運用ワークロードに使用したり、テストしたりできます。
* プールしたマルチセッション リソースを使ってコストを節約できます。 Windows Virtual Desktop 限定の新しい Windows 10 Enterprise マルチセッション機能と、Windows Server 上のリモート デスクトップ セッション ホスト (RDSH) ロールを併用すれば、ユーザーにこれまでと同じリソースを提供しつつ、仮想マシンの数やオペレーティング システム (OS) のオーバーヘッドを大幅に削減できます。
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

まず、デプロイを予定しているデスクトップおよびアプリに応じて、ユーザーのための[適切なライセンス](https://azure.microsoft.com/pricing/details/virtual-desktop/)を持っていることを確認してください。

|OS|必要とされるライセンス|
|---|---|
|Windows 10 Enterprise マルチセッションまたは Windows 10 シングルセッション|Microsoft 365 E3、E5、A3、A5、Business、F1<br>Windows E3、E5、A3、A5|
|Windows 7|Microsoft 365 E3、E5、A3、A5、Business、F1<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|ソフトウェア アシュアランス付きの RDS クライアント アクセス ライセンス (CAL)|

インフラストラクチャが Windows Virtual Desktop をサポートするうえで必要なものは次のとおりです。

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Azure Active Directory と同期している Windows Server Active Directory。 これは、次のいずれかを使うことにより可能になります。
  * Azure AD Connect
  * Azure AD Domain Services
* Azure サブスクリプション (Windows Server Active Directory を含むか、Windows Server Active Directory に接続している仮想ネットワークが含まれるもの)
  
Windows Virtual Desktop 用に作成する Azure 仮想マシンに必要な条件は次のとおりです。

* [標準ドメイン参加済み](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison)または [Hybrid AD 参加済み](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)であること。 仮想マシンを Azure AD に参加させることはできません。
* サポート対象となる次のいずれかの OS イメージを実行していること。
  * Windows 10 (Enterprise マルチセッション)
  * Windows Server 2016

>[!NOTE]
>Azure サブスクリプションが必要な場合には、[1 か月間の無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。 無料試用版の Azure をご利用中の場合には、Windows Server Active Directory が Azure Active Directory と同期した状態を保つことができるよう、Azure AD Domain Services を使用する必要があります。

Windows Virtual Desktop の構成要素には、お客様がユーザーに配信する Windows のデスクトップとアプリのほか、Microsoft が Azure 上でサービスとしてホストしている管理ソリューションがあります。 パブリック プレビューの期間中は、デスクトップとアプリは任意の Azure リージョンの仮想マシン (VM) 上にデプロイできるのに対して、管理ソリューションと VM 関連のデータはどちらも、米国 (米国東部 2 リージョン) でホストされます。 このため、パブリック プレビュー期間中にサービスをテストするときは、米国を宛先とするデータ転送が発生することがあります。 管理ソリューションとデータを Azure の全リージョンにローカライズして展開していく作業は、一般提供が始まってから開始する予定です。

最適なパフォーマンスを実現するために、お使いのネットワークが次の要件を満たしていることを確認してください。

* クライアントのネットワークからホスト プールをデプロイした Azure リージョンまでのラウンドトリップ (RTT) 待ち時間は、150 ミリ秒を下回っている必要があります。
* デスクトップとアプリをホストしている VM が管理サービスに接続する際には、ネットワーク トラフィックが国外に流れることがあります。
* ネットワークのパフォーマンスを最適化するために、セッション ホストの VM を管理サービスと同じ Azure リージョンに配置することをお勧めします。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。 Windows Virtual Desktop がプレビューの間は、サポート ケースを受け付けていません。

## <a name="next-steps"></a>次の手順

まずは、テナントを作成する必要があります。 テナントの作成方法については、テナント作成に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Windows Virtual Desktop プレビューでテナントを作成する](tenant-setup-azure-active-directory.md)
