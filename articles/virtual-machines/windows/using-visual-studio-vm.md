---
title: Azure 仮想マシンでの Visual Studio の使用| Microsoft Docs
description: Azure 仮想マシンでの Visual Studio の使用。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 06/24/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 31b856b4f6937b57f0a2b82d94fc0fb02936f83f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449367"
---
# <a name="visual-studio-images-on-azure"></a>Azure 上の Visual Studio のイメージ
事前に構成済みの Azure 仮想マシン (VM) 上で Visual Studio を使用することは、ゼロから稼働状態の開発環境を構築するための簡単かつ迅速な方法です。 さまざまな Visual Studio 構成のシステム イメージは、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure) で入手できます。

Azure を利用するのが初めてであれば、 [無料の Azure アカウントを作成します](https://azure.microsoft.com/free)。

## <a name="what-configurations-and-versions-are-available"></a>どんな構成とバージョンを使用できますか。
最新のメジャー バージョン (Visual Studio 2019、Visual Studio 2017、Visual Studio 2015) のイメージは、Azure Marketplace で提供されています。  リリースされたメジャー バージョンごとに、最初に Web にリリースされたバージョン (RTW: Released To Web) と最新の更新バージョンが表示されます。  これらの各バージョンでは、Visual Studio Enterprise エディションと Visual Studio Community エディションが提供されます。  これらのイメージは、少なくとも月に 1 回は更新され、最新の Visual Studio と Windows の更新プログラムが適用されます。  イメージの名前は変わりませんが、各イメージの説明には、インストールされている製品のバージョンと、その時点のイメージの日付が記載されます。

| リリース バージョン                                                                                                                                                | エディション              | 製品バージョン   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: 最新 (バージョン 16.1)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise、Community | バージョン 16.1.3    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Enterprise、Community | バージョン 16.0.5    |
| [Visual Studio 2017: 最新 (バージョン 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Enterprise、Community | バージョン 15.9.13   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Enterprise、Community | バージョン 15.0.24   |
| [Visual Studio 2015: 最新 (Update 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Enterprise、Community | Version 14.0.25431.01 |

> [!NOTE]
> Microsoft サービス ポリシーに従って、最初にリリースされた Visual Studio 2015 のバージョン (RTW) のサービスは、期限切れになりました。 Visual Studio 2015 Update 3 は、Visual Studio 2015 製品ラインに提供されているそれ以外のバージョンだけです。

詳細については、[Visual Studio サービス ポリシー](https://www.visualstudio.com/productinfo/vs-servicing-vs)に関するページをご覧ください。

## <a name="what-features-are-installed"></a>どのような機能がインストールされていますか。
各イメージには、その Visual Studio エディションで推奨される機能セットが含まれています。 一般的に、インストールには以下が含まれます。

* 各ワークロードで推奨されているオプションのコンポーネントを含む、使用可能なすべてのワークロード
* .NET 4.6.2 および .NET 4.7 SDK、Targeting Packs、および 開発者ツール
* Visual F#
* Visual Studio 向け GitHub 拡張
* LINQ to SQL ツール

イメージを作成するときに Visual Studio のインストールに使用するコマンド ラインは次のとおりです。

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

イメージに必要な Visual Studio 機能が含まれていない場合は、ページの右上隅にあるフィードバック ツールからフィードバックを提供してください。

## <a name="what-size-vm-should-i-choose"></a>どの VM サイズを選択すればいいですか。
Azure では、さまざまなサイズの仮想マシンが提供されています。 Visual Studio は強力なマルチスレッド アプリケーションなので、2 つ以上のプロセッサと 7 GB 以上のメモリを含む VM サイズを検討してください。 Visual Studio のイメージに推奨される VM サイズは以下のとおりです。

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
最新のマシン サイズについては、「[ Azure の Windows 仮想マシンのサイズ](/azure/virtual-machines/windows/sizes)」をご覧ください。

Azure では、VM サイズを変更して、最初の選択を再調整することもできます。 より適切なサイズで新しい VM をプロビジョニングすることもできますし、既存の VM のサイズを別の基本ハードウェアへと変更することもできます。 詳しくは、「[Windows VM のサイズ変更](/azure/virtual-machines/windows/resize-vm)」をご覧ください。

## <a name="after-the-vm-is-running-whats-next"></a>VM を実行したら、次に何をすればよいですか。
Visual Studio は、Azure の "ライセンス持ち込み" モデルに従って動作します。 私有するハードウェア上のインストールと同様、まずは、Visual Studio インストールのラインセンス付与を行います。 Visual Studio のロックを解除するには、次のいずれかを行います。
- Visual Studio サブスクリプションに関連付けられた Microsoft アカウントでサインインする 
- 最初の購入時に提供されたプロダクト キーを使って Visual Studio のロックを解除する

詳しくは、[Visual Studio へのサインイン](/visualstudio/ide/signing-in-to-visual-studio)と [Visual Studio のロック解除方法](/visualstudio/ide/how-to-unlock-visual-studio)に関するページをご覧ください。

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>今後の使用やチームでの使用のために、開発用 VM を保存するにはどうすればよいですか。

開発環境のスペクトルは幅広く、より複雑な環境の構築に関連する実質的なコストがあります。 構成済みの仮想マシンは、環境の構成に関係なく、将来使用したり、チームの他のメンバーに提供するための "基本イメージ" として保存 (またはキャプチャ) できます。 その後、新しい VM を起動する際には、Azure Marketplace イメージからではなく、基本イメージから VM をプロビジョニングします。

簡単な概要:システム準備ツール (Sysprep) を使用し、実行中の VM をシャットダウンした後、Microsoft Azure portal の UI を使用して、VM をイメージとしてキャプチャ *(図 1)* します。 Azure は、選択したストレージ アカウントのイメージを格納した `.vhd` ファイルを保存します。 その後、サブスクリプションのリソース一覧に、新しいイメージがイメージ リソースとして表示されます。

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center> *(図 1) Azure portal の UI を使用してイメージをキャプチャする*</center>

詳しくは、「[Azure で一般化された VM の管理対象イメージを作成する](/azure/virtual-machines/windows/capture-image-resource)」をご覧ください。

> [!IMPORTANT]
> 必ず、Sysprep を使用して VM を準備してください。 この手順を実行しなかった場合、Azure ではイメージから VM をプロビジョニングできません。

> [!NOTE]
> イメージを保存する分のストレージ コストはかかりますが、VM を必要とするチーム メンバーごとに VM をゼロから再構築するコストに比べれば、追加されるコストは少額で済みます。 たとえば、チーム全体で再利用可能な 127 GB のイメージを作成して 1 か月間保管するには、数ドルのコストがかかります。 しかし、これらのコストは、各従業員が個別使用のために適切に構成した dev ボックスを作成して検証する時間に比べれば、少ないものです。

また、開発タスクや使用するテクノロジによっては、より大規模な環境が必要になる場合があります (多様な開発構成や複数のマシン構成が必要になるなど)。 Azure DevTest Labs を使用すれば、"ゴールデン イメージ" の作成を自動化するための _"レシピ"_ を作成できます。 また、DevTest Labs では、チームで実行する VM のポリシーを管理することもできます。 DevTest Labs の詳細については、「[開発者のための Azure DevTest Labs の使用](/azure/devtest-lab/devtest-lab-developer-lab)」が最適な資料です。

## <a name="next-steps"></a>次の手順
事前構成済みの Visual Studio イメージについて確認したので、次の手順では新しい VM を作成します。

* [Azure Portal を使用した VM の作成](quick-create-portal.md)
* [Windows 仮想マシンの概要](overview.md)
