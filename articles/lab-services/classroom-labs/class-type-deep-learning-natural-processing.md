---
title: Azure Lab Services を使用したディープ ラーニングに重点を置いたラボを設定する | Microsoft Docs
description: Linux でのシェル スクリプト作成を教えるラボを設定する方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0aa30c114153521258842fc5f1e9150053b766f4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332295"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Azure Lab Services を使用した自然言語処理でのディープ ラーニングに重点を置いたラボを設定する
この記事では、Azure Lab Services を使用した自然言語処理 (NLP) でのディープ ラーニングに重点を置いたラボを設定する方法について説明します。 自然言語処理 (NLP) は、コンピューターで翻訳、音声認識、その他の言語認識機能を実行できるようにする、人工知能 (AI) の一種です。  

NLP の授業を受けている学生は、Linux 仮想マシン (VM) を使用し、ニューラル ネットワーク アルゴリズムを適用して、書かれた人間の言語を分析するために使用されるディープ ラーニング モデルを開発する方法を学習します。 

## <a name="lab-configuration"></a>ラボの構成
このラボを設定するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを作成したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のラボ アカウントを使用することができます。 新しいラボ アカウントを作成するには、次のチュートリアルを参照してください: 「[チュートリアル: Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」。
 
ラボ アカウントを作成した後は、ラボ アカウントで次のように設定します。 

| ラボ アカウントの設定 | Instructions |
| ----------- | ------------ |  
| マーケットプレースの画像 | ラボ アカウント内で Data Science Virtual Machine for Linux (Ubuntu) イメージを使用できるようにします。  その手順については、次の記事を参照してください。「[ラボ作成者が利用できる Marketplace イメージを指定する](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators)」。 | 

[このチュートリアル](tutorial-setup-classroom-lab.md)に従って、新しいラボを作成し、次の設定を適用します。

| ラボの設定 | 値/説明 | 
| ------------ | ------------------ |
| 仮想マシン (VM) サイズ | Small GPU (Compute)。 このサイズは、人工知能やディープ ラーニングのような、コンピューティング集中型およびネットワーク集中型のアプリケーションに最適です。 |
| VM イメージ | [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 このイメージでは、機械学習とデータ サイエンス向けのディープ ラーニング フレームワークとツールが提供されます。 このイメージでインストールされるツールの完全な一覧を見るには、次の記事を参照してください: 「[DSVM に含まれているもの](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)」。 |
| リモート デスクトップ接続を有効にする | [Enable]\(有効にする\)。 <p>この設定を有効にすると、教師と学生はリモート デスクトップ (RDP) を使用して仮想マシン (VM) に接続できます。</p><p>**重要**:Data Science Virtual Machine for Linux イメージには、RDP が既にインストールおよび構成されています。 そのため、教師と学生は、何もしなくても RDP 経由で VM に接続できます。 また、グラフィカル デスクトップに接続する必要がある場合、このイメージでは [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) が仮想マシンに既にインストールされています。 学生は、ローカル コンピューターに X2Go クライアントをインストールし、そのクライアントを使用して接続する必要があります。 詳細については、次のガイドを参照してください。 <ul><li>[Data Science Virtual Machine for Linux にアクセスする方法](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[テンプレート VM に接続して RDP および GUI パッケージをインストールする](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Data Science Virtual Machine for Linux イメージには、この種類のクラスに必要なディープ ラーニング フレームワークとツールが用意されています。 そのため、テンプレート マシンを作成した後で、さらにカスタマイズする必要はありません。 学生が使用できるように発行できます。 テンプレート ページの **[発行]** ボタンを選択して、テンプレートをラボに発行します。  

## <a name="cost"></a>コスト
このラボのコストを見積もるには、次の例を使用できます。 

20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります - 学生 25 人 * (20 + 10) 時間 * 139 ラボ ユニット * 0.01 USD/時間 = 1042.5 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ
この記事では、自然言語処理クラスのラボを作成する手順について説明しました。 他のディープ ラーニング クラスにも同様のセットアップを使用できます。

## <a name="next-steps"></a>次の手順
次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。 

