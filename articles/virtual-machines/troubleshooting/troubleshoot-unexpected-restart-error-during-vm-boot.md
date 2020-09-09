---
title: OS 起動時 - コンピューターが予期せず再起動したか、予期しないエラーが発生しました
description: この記事では、Windows のインストール中に VM が予期せずに再起動したりエラーが発生したりする問題を解決する手順について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036184"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>OS 起動時 - コンピューターが予期せず再起動したか、予期しないエラーが発生しました

この記事では、Windows のインストール中に仮想マシン (VM) が予期せずに再起動したりエラーが発生したりする問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[起動診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、Windows インストールが失敗して、次のエラーが生成したことがスクリーンショットに示されます。

**コンピューターが予期せず再起動したか、予期しないエラーが発生しました。Windows のインストールを続行できません。Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。**

![Windows のインストール中に発生したエラー:コンピューターが予期せず再起動したか、予期しないエラーが発生しました。 Windows のインストールを続行できません。 Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows インストール セットアップがサービスを開始するときに発生したエラー:コンピューターが予期せず再起動したか、予期しないエラーが発生しました。 Windows のインストールを続行できません。 Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

コンピューターは [一般化されたイメージ](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)の初期ブートを実行しようとしていますが、処理中のカスタム応答ファイル (unattend.xml) が原因で問題が発生しました。 Azure では、カスタム応答ファイルはサポートされていません。 

応答ファイルは、Windows Server オペレーティング システムのインストール中に自動化する構成設定の定義と値を含む特殊な XML ファイルです。 構成オプションには、ディスクのパーティション分割方法、インストールする Windows イメージがある場所、適用するプロダクト キー、実行するその他のコマンドなどが含まれます。

Azure では、カスタム応答ファイルはサポートされていません。 `sysprep /unattend:<your file’s name>` オプションを使用して、カスタムの **Unattend.xml** ファイルを指定すると、このエラーが発生することがあります。

Azure では、Unattend.xml ファイルではなく、**Sysprep.exe**の **[システムの OOBE (Out-of-Box Experience) に入る]** オプションを使用するか、`sysprep /oobe` を使用します。

ほとんどの場合、この問題は、**Sysprep.exe** とオンプレミスの VM を使用して一般化された VM を Azure にアップロードしているときに発生します。 このような状況のため、一般化された VM を適切にアップロードする方法を確認することをお勧めします。

## <a name="solution"></a>解決策

### <a name="replace-unattended-answer-file-option"></a>無人応答ファイルを置き換える方法

この状況が発生するのは、Azure で使用されるイメージが用意されているが、Azure でサポートされていないカスタム応答ファイルが使用されているときに、次のコマンドのようにフラグを指定して **SYSPREP** を使用したときです。

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- 前のコマンドで、`<NameOfYourAnswerFile.XML>` をお使いのファイルの名前で置き換えます。

この問題を解決するには、[イメージの準備やキャプチャに関する Azure のガイダンス](../windows/upload-generalized-managed.md)の記事に従い、一般化された新しいイメージを準備します。 sysprep の際に `/unattend:<answerfile>` フラグを使用しないでください。 代わりに、以下のフラグのみを使用してください。

`sysprep /oobe /generalize /shutdown`

- **Out-of-box-experience** (OOBE) は Azure VM でサポートされている設定です。

**システム準備ツール GUI** を使用し、以下のオプションを選択して、上記のコマンドと同じタスクを実行することもできます。

- Out-of-Box-Experience に入る
- Generalize
- Shutdown
 
![OOBE、一般化、およびシャットダウンのオプションが選択されたシステム準備ツールのウィンドウ。](./media/unexpected-restart-error-during-vm-boot/3.png)
