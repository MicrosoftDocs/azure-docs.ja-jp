---
title: Red Hat Enterprise Linux 延長ライフサイクル サポート
description: Red Hat Enterprise 延長ライフサイクル サポート アドオンを追加する方法について説明します
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677192"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) Extended Lifecycle Support
この記事では、次の Red Hat Enterprise イメージに対する延長ライフサイクル サポートについて説明します。
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 のライフサイクル
2020 年 11 月 30 日をもって、Red Hat Enterprise Linux 6 のメンテナンス フェーズは終了します。 メンテナンス フェーズの後に、延長ライフサイクル フェーズが続きます。 Red Hat Enterprise Linux 6 はフルまたはメンテナンスのフェーズから移行するため、Red Hat Enterprise Linux 7 または 8 にアップグレードすることを強くお勧めします。 顧客が Red Hat Enterprise Linux 6 を使い続ける必要がある場合は、Red Hat Red Hat Enterprise Linux Extended Life Cycle Support (ELS) アドオンを追加することをお勧めします。

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Marketplace の従量課金制 VM に拡張ライフサイクル サポートを追加する手順
1. [こちらからアクセスできる ELS フォーム](https://aka.ms/els-form)に、連絡先の詳細と、ELS サポートを追加する VM のサブスクリプションの情報を入力します。 アドオンの価格の詳細は、フォームでも確認できます。
1. Azure Red Hat Enterprise Linux チームが、1-2 営業日以内に連絡して、ELS サポート アドオンを適用した VM の一覧をご案内します。 一覧を確認し、アドオンの価格に同意してください。
1. Azure Red Hat Enterprise Linux チームが、ELS クライアント パッケージを VM に追加する手順を共有します。 電子メールに記載されている手順に従って、引き続きソフトウェア メンテナンス (バグとセキュリティの修正) を受け取り、Red Hat Enterprise Linux 6 のサポートを受けます。

> [!Note]
> RHEL ELS アドオンを使用する手順は、組織外のユーザーと共有しないでください。 サポートの依頼、またはその他の質問については、AzureRedHatELS@microsoft.com にご連絡ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6 を実行しています。現時点では、新しいバージョンに移行することはできません。 どのようなオプションがありますか
* 引き続き Red Hat Enterprise Linux 6 を実行し、Extended Life Cycle Support (ELS) アドオン リポジトリを購入すると、制限されたソフトウェア メンテナンスとテクニカル サポートを引き続き受けることができます (アップグレードのプロセスと価格の詳細については、以下を参照してください)。
* できるだけ早く Red Hat Enterprise Linux 7 または 8 に移行してください。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Red Hat Enterprise Linux Extended Life Cycle Support (ELS) アドオンを使用する場合、追加料金はいくらですか?
拡張ライフサイクル サポートに関連するコストについては、[ELS フォーム](https://aka.ms/els-form)を参照してください

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>カスタム イメージを使用して VM をデプロイしました。 この VM に延長ライフサイクル サポートを追加するにはどうすればよいですか?
Red Hat に直接問い合わせて、直接サポートを受ける必要があります。

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>カスタム イメージを使用して VM をデプロイしました。 この VM を PAYG VM に変換することはできますか?
いいえ、できません。 現在、この変換は、Azure ではサポートされていません。


## <a name="next-steps"></a>次のステップ

* Azure の RHEL イメージの詳細な一覧については、「[Azure で利用可能な Red Hat Enterprise Linux (RHEL) イメージ](./redhat-imagelist.md)」を参照してください。
* Azure Red Hat Update Infrastructure の詳細については、「[Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure](./redhat-rhui.md)」を参照してください。
* RHEL BYOS オファーの詳細については、「[Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image](./byos.md)」を参照してください。
* すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。