---
title: Azure Image Builder (プレビュー) について
description: Azure の仮想マシンの Azure Image Builder についての詳細を参照してください。
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604068"
---
# <a name="preview-azure-image-builder-overview"></a>プレビュー:Azure Image Builder の概要

組織で標準化された仮想マシン (VM) イメージを使用すると、クラウドに移行し、デプロイの一貫性を保つことができます。 通常、イメージには、事前に定義されたセキュリティと構成設定と、必要なソフトウェアが含まれています。 独自のイメージング パイプラインを設定するには、時間、インフラストラクチャ、設定が必要ですが、Azure VM Image Builder を使用すると、イメージを説明する構成を用意し、それをサービスに送信するだけで、イメージが構築され、配布されます。
 
Azure VM Image Builder (Azure Image Builder) を使用すると、Windows または Linux ベースの Azure Marketplace イメージ、既存のカスタム イメージから始めて、独自のカスタマイズを追加することができます。 Image Builder は [HashiCorp Packer](https://packer.io/) 上に構築されているため、いくつかの類似点がありますが、マネージド サービスの利点があります。 [Azure Shared Image Gallery](shared-image-galleries.md) で、イメージのホスト先としてマネージド イメージまたは VHD を指定することもできます。

> [!IMPORTANT]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="preview-features"></a>プレビュー機能

プレビューでは、次の機能がサポートされています。

- ベースライン イメージの作成。最低限のセキュリティと企業の構成を含み、各部門がさらにカスタマイズできます。
- コア アプリケーションの統合。これにより、VM は作成後にワークロードを引き受けたり、Windows Virtual Desktop のイメージをサポートする構成を追加したりできます。
- 既存のイメージへの修正プログラム適用。Image Builder では既存のカスタム イメージに継続的に修正プログラムを適用できます。
- Image Builder を既存の仮想ネットワークに接続します。それにより、既存の構成サーバー (DSC、Chef、Puppet など)、ファイル共有、またはその他のルーティング可能なサーバー/サービスに接続できます。
- Azure 共有イメージ ギャラリーとの統合。イメージをグローバルに配布、バージョン管理、およびスケールすることができます。また、イメージ管理システムを利用できます。
- 既存のイメージ ビルド パイプラインとの統合。パイプラインから Image Builder を呼び出すか、単純な Preview Image Builder Azure DevOps Task を使用するだけです。
- 既存のイメージ カスタマイズ パイプラインを Azure に移行します。 既存のスクリプト、コマンド、およびプロセスを使用してイメージをカスタマイズします。
- Azure Stack をサポートするための VHD 形式のイメージの作成。
 

## <a name="regions"></a>リージョン
Azure Image Builder Service は、これらのリージョンでプレビューできるようになる予定です。 イメージは、これらのリージョン以外に配布できます。
- 米国東部
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2
- 北ヨーロッパ
- 西ヨーロッパ

## <a name="os-support"></a>OS のサポート
AIB は Azure Marketplace のベース OS イメージをサポートします。
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise マルチセッション/Professional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>しくみ

Azure VM Image Builder は、Azure リソース プロバイダーからアクセスできるフル マネージド Azure サービスです。 ソース イメージ、実行するカスタマイズ、および新しいイメージの配布先を指定する構成をサービスに提供します。下の図は、大まかなのワークフローを示しています。

![ソース (Windows/Linux)、カスタマイズ (シェル、PowerShell、Windows の [再起動して更新]、ファイルの追加)、および Azure Shared Image Gallery を使用したグローバル分散を示す、Azure Image Builder のプロセスの概念図](./media/image-builder-overview/image-builder-flow.png)

テンプレート構成は、PowerShell、Az CLI、ARM テンプレートを使用して、および Azure VM Image Builder DevOps タスクを使用して渡すことができ、それをサービスに送信すると、イメージ テンプレートのリソースが作成されます。 イメージ テンプレートのリソースが作成されると、サブスクリプションに作成されたステージング リソース グループが、IT_\<DestinationResourceGroup> _\<TemplateName>_ \(GUID) という形式で表示されます。 ステージング リソース グループには、ScriptURI プロパティの File、Shell、PowerShell カスタマイズで参照されるファイルとスクリプトが含まれています。

イメージ テンプレートのリソースで `Run` を呼び出すビルドを実行するために、サービスにより、ビルドのために追加のリソース (VM、ネットワーク、ディスク、ネットワーク アダプターなど) がデプロイされます。既存の VNET Image Builder を使用せずにイメージをビルドすると、パブリック IP と NSG もデプロイされ、このサービスは SSH または WinRM を使用してビルド VM に接続します。 既存の VNET を選択した場合、サービスは Azure Private Link を使用してデプロイされ、パブリック IP アドレスは必要ありません。Image Builder のネットワークの詳細については、[詳細](./linux/image-builder-networking.md)に関するページを確認してください。

ビルドが完了すると、すべてのリソースが削除されますが、ステージング リソース グループとストレージ アカウントを除きます。これらを削除するには、イメージ テンプレートのリソースを削除するか、そのままにしてビルドを再度実します。

このドキュメントには、[Azure Image Builder GitHub リポジトリ](https://github.com/azure/azvmimagebuilder)の構成テンプレートとソリューションを参照する複数の例とステップバイステップ ガイドがあります。

### <a name="move-support"></a>移動のサポート
イメージ テンプレートのリソースは変更不可であり、リソースとステージング リソース グループへのリンクが含まれているため、リソースの種類は移動に対応していません。 イメージ テンプレートのリソースを移動する場合は、構成テンプレートのコピーがあることを確認し (ない場合は、リソースから既存の構成を抽出し)、新しい名前で新しいリソース グループに新しいイメージ テンプレートのリソースを作成して、以前のイメージ テンプレートのリソースを削除します。 

## <a name="permissions"></a>アクセス許可
(AIB) に登録すると、ステージング リソース グループ (IT_*) を作成、管理、削除するためのアクセス許可が AIB サービスに付与され、イメージのビルドに必要なリソースを追加する権限が与えられます。 これは、登録が成功したときに、AIB サービス プリンシパル名 (SPN) がサブスクリプションで使用可能になることによって行われます。

Azure VM Image Builder で、マネージド イメージまたは Shared Image Gallery にイメージを配布できるようにするには、イメージの読み取りと書き込みのアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Azure Storage にアクセスする場合は、プライベートおよびパブリック コンテナーを読み取るためのアクセス許可が必要です。

アクセス許可の詳細については、[PowerShell](./linux/image-builder-permissions-powershell.md) および [AZ CLI](./linux/image-builder-permissions-cli.md) に関するページを参照してください。

## <a name="costs"></a>コスト
Azure Image Builder を使用してイメージの作成、構築、保存を行うと、コンピューティング、ネットワーク、ストレージのコストがかかります。 これらのコストは、手動でカスタム イメージを作成する際に発生するコストと同様です。 リソースについては、Azure の料金で課金されます。 

イメージ作成プロセス中に、ファイルがダウンロードされ、`IT_<DestinationResourceGroup>_<TemplateName>` リソース グループに格納されます。これにより、小額のストレージ コストが発生します。 これらを維持しない場合は、イメージの構築後に **イメージ テンプレート** を削除してください。
 
Image Builder では、VM に必要な D1v2 VM サイズ、ストレージ、ネットワークを使用して VM を作成します。 これらのリソースは、構築プロセスの間は存続し、Image Builder のイメージ作成が終了すると削除されます。 
 
Azure Image Builder によって、選択したリージョンにイメージが配布されます。これにより、ネットワークのエグレス料金が発生する可能性があります。

## <a name="hyper-v-generation"></a>Hyper-V の世代
現在、Image Builder でネイティブにサポートされているのは、Azure Shared Image Gallery (SIG) の Hyper-V 第 1 世代 (Gen1) のイメージまたはマネージド イメージの作成のみです。 
 
## <a name="next-steps"></a>次のステップ 
 
Azure Image Builder を試すには、[Linux](./linux/image-builder.md) または [Windows](./windows/image-builder.md) イメージの構築に関する記事を参照してください。