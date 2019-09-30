---
title: Azure シリアル コンソールのエラー | Microsoft Docs
description: Azure シリアル コンソール内での一般的なエラー
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129471"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure シリアル コンソール内での一般的なエラー
Azure シリアル コンソール内には、一連の既知のエラーがあります。 これらのエラーとその対応策の一覧を次に示します。

## <a name="common-errors"></a>一般的なエラー

Error                            |   対応策
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;* . (VMNAME のブート診断設定を取得できません。) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) ![ブート診断エラー](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | VM または仮想マシン スケール セットで[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 仮想マシン スケール セットでシリアル コンソールを使用している場合は、インスタンスが最新モデルであることを確認してください。
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) ![割り当て解除済みエラー](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | シリアル コンソールにアクセスするには、VM または仮想マシン スケール セット インスタンスが起動済み状態である必要があります。 VM または仮想マシン スケール セット インスタンスを起動して、もう一度やり直してください。
この VM のブート診断ストレージ アカウントにアクセスする際に、"許可されていません" という応答が発生しました。 ![ストレージ アカウント ファイアウォールのエラー](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。
You do not have the required permissions to use this VM with the serial console. (シリアル コンソールでこの VM を使用するために必要なアクセス許可がありません。) Ensure you have at least Virtual Machine Contributor role permissions. (少なくとも仮想マシン共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、VM または仮想マシン スケール セットに対する共同作成者レベル以上のアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
The storage account '' used for boot diagnostics on this VM could not be found. (この VM のブート診断に使用したストレージ アカウント '' が見つかりませんでした。) Verify that boot diagnostics is enabled for this VM, this storage account has not been deleted, and you have access to this storage account. (この VM に対してブート診断が有効になっていること、このストレージ アカウントが削除されていないこと、このストレージ アカウントへのアクセス権があることをご確認ください。) | VM または仮想マシン スケール セットのブート診断ストレージ アカウントが削除されていないことを再度確認します。
Provisioning for this VM has not yet succeeded. (この VM のプロビジョニングはまだ成功していません。) Please ensure the VM is fully deployed and retry the serial console connection. (VM が完全にデプロイされていることを確認して、シリアル コンソール接続をもう一度お試しください。) | VM または仮想マシン スケール セットがまだプロビジョニング中である可能性があります。 しばらく待って、もう一度お試しください。
You do not have the required permissions to write to the boot diagnostics storage account for this VM. (この VM のブート診断ストレージ アカウントに書き込むために必要なアクセス許可がありません。) Please ensure you have at least VM Contributor permissions on ''. ('' に対して少なくとも VM 共同作成者のアクセス許可があることをご確認ください。) | シリアル コンソールにアクセスするには、ブート診断ストレージ アカウントに対する共同作成者レベルのアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;* . (ブート診断ストレージ アカウント STORAGEACCOUNTNAME のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account. (この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。) | シリアル コンソールにアクセスするには、ブート診断ストレージ アカウントに対する共同作成者レベルのアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
Web ソケットが閉じているか、開けませんでした。 | `*.console.azure.com` にファイアウォール アクセスを追加することが必要になる場合があります。 より詳細であるが時間がかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのファイアウォール アクセスを許可するというものがありますが、この IP 範囲はかなり定期的に変化します。


## <a name="next-steps"></a>次の手順
* [Linux VM 用 Azure シリアル コンソール](./serial-console-linux.md)についての詳細を参照する
* [Windows VM 用 Azure シリアル コンソール](./serial-console-windows.md)についての詳細を参照する