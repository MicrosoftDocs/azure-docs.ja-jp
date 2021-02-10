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
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632692"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>OS 起動時 - コンピューターが予期せず再起動したか、予期しないエラーが発生しました

この記事では、Windows のインストール中に仮想マシン (VM) が予期せずに再起動したりエラーが発生したりする問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[起動診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、Windows インストールが失敗して、次のエラーが生成したことがスクリーンショットに示されます。

**コンピューターが予期せず再起動したか、予期しないエラーが発生しました。Windows のインストールを続行できません。Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。**

![Windows のインストール中に発生したエラー:コンピューターが予期せず再起動したか、予期しないエラーが発生しました。 Windows のインストールを続行できません。 Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows インストール セットアップがサービスを開始するときに発生したエラー:コンピューターが予期せず再起動したか、予期しないエラーが発生しました。 Windows のインストールを続行できません。 Windows をインストールするには、[OK] をクリックしてコンピューターを再起動してから、インストールを再実行してください。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

コンピューターは[一般化されたイメージ](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)の初期ブートを実行しようとしていますが、処理中のカスタム応答ファイル (Unattend.xml) が原因で問題が発生しました。 **Azure では、カスタム応答ファイルはサポートされていません**。 

応答ファイルは、Windows Server オペレーティング システムのインストール中に自動化する構成設定の定義と値を含む特殊な XML ファイルです。 構成オプションには、ディスクのパーティション分割方法、インストールする Windows イメージがある場所、適用するプロダクト キー、実行するその他のコマンドなどが含まれます。

ここでも、カスタム応答ファイルは Azure ではサポートされていません。 したがって、この状況が発生するのは、Azure で使用されるイメージが用意されているが、**SYSPREP** を使用して次のコマンドのようなフラグでカスタムの Unattend.xml ファイルを指定した場合です。

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

Azure では、Unattend.xml ファイルではなく、**システム準備ツール GUI** の **[システムの OOBE (Out-of-Box Experience) に入る]** オプションを使用するか、`sysprep /oobe` を使用します。

ほとんどの場合、この問題は、sysprep とオンプレミスの VM を使用して一般化された VM を Azure にアップロードしているときに発生します。 このような状況のため、一般化された VM を適切にアップロードする方法を確認することをお勧めします。

## <a name="solution"></a>解決策

### <a name="do-not-use-unattendxml"></a>Unattend.xml を使用しない

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

この問題を解決するには、[イメージの準備やキャプチャに関する Azure のガイダンス](../windows/upload-generalized-managed.md)の記事に従い、一般化された新しいイメージを準備します。 sysprep の際に **`/unattend:<your file’s name>` フラグを使用しないでください**。 代わりに、以下のフラグのみを使用してください。

`sysprep /oobe /generalize /shutdown`

- Out-of-box-experience (OOBE) は Azure VM でサポートされている設定です。

**システム準備ツール GUI** を使用し、以下のオプションを選択して、上記のコマンドと同じタスクを実行することもできます。

- Out-of-Box-Experience に入る
- Generalize
- Shutdown
 
![OOBE、一般化、およびシャットダウンのオプションが選択されたシステム準備ツールのウィンドウ。](./media/unexpected-restart-error-during-vm-boot/3.png)
