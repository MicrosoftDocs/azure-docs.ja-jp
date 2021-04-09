---
title: Azure Lab Services で Linux シェル スクリプト作成ラボを設定する | Microsoft Docs
description: Linux でのシェル スクリプト作成を教えるラボを設定する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444966"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Linux でのシェル スクリプト作成を教えるためのラボを設定する
この記事では、Linux でのシェル スクリプトの作成を教えるためのラボを設定する方法について説明します。 スクリプトの作成は、管理者がタスクを繰り返さなくて済むようにできる、システム管理の役に立つ部分です。 このサンプル シナリオのクラスでは、従来の bash スクリプトと拡張スクリプトについて説明します。 拡張スクリプトは、bash コマンドと Ruby を組み合わせたスクリプトです。 このアプローチにより、Ruby でデータを渡すことができ、bash コマンドでシェルと対話することができます。 

これらのスクリプト作成のクラスを受講する学生は、Linux 仮想マシンを使用して、Linux の基本を学習し、bash シェル スクリプトの作成にも慣れることができます。 Linux 仮想マシンには、有効になったリモート デスクトップ アクセスが付属しており、[gedit](https://help.gnome.org/users/gedit/stable/) と [Visual Studio Code](https://code.visualstudio.com/) テキスト エディターがインストールされています。

## <a name="lab-configuration"></a>ラボの構成
このラボを設定するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを作成したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のラボ アカウントを使用することができます。 新しいラボ アカウントを作成するには、次のチュートリアルを参照してください: 「[チュートリアル: Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」。

ラボ アカウントを作成した後は、ラボ アカウントで次のように設定します。 

| ラボ アカウントの設定 | Instructions |
| ----------- | ------------ |  
| マーケットプレースの画像 | ラボ アカウント内で使用する Ubuntu Server 18.04 LTS イメージを有効にします。 詳細については、「[ラボ作成者が利用できる Marketplace イメージを指定する](specify-marketplace-images.md)」を参照してください | 

[このチュートリアル](tutorial-setup-classroom-lab.md)に従って、新しいラボを作成し、次の設定を適用します。

| ラボの設定 | 値/説明 | 
| ------------ | ------------------ |
| 仮想マシン (VM) サイズ | Small  |
| VM イメージ | Ubuntu Server 18.04 LTS|
| リモート デスクトップ接続を有効にする | [Enable]\(有効にする\)。 <p>この設定を有効にすると、教師と学生はリモート デスクトップ (RDP) を使用して VM に接続できます。 詳細については、「[Azure Lab Services のラボの Linux 仮想マシンでリモート デスクトップを有効にする](how-to-enable-remote-desktop-linux.md)」を参照してください。 </p>|

## <a name="install-desktop-and-rdp"></a>デスクトップと RDP をインストールする
Ubuntu Server 18.04 LTS イメージでは、RDP リモート デスクトップ サーバーは既定ではインストールされません。 「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../virtual-machines/linux/use-remote-desktop.md)」記事の手順に従って、リモート デスクトップ プロトコル(RDP) を使用して接続するためにテンプレート マシンで必要なパッケージをインストールします。

## <a name="install-ruby"></a>Ruby のインストール
Ruby は、bash スクリプトと組み合わせることができるオープンソースの動的言語です。 このセクションでは、`apt-get` を使用して最新バージョンの [Ruby](https://www.ruby-lang.org/) をインストールする方法について説明します。

1. 次のコマンドを実行して更新プログラムをインストールします。

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  [Ruby](https://www.ruby-lang.org/) をインストールします。  Ruby は、bash スクリプトと組み合わせることができるオープンソースの動的言語です。 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>開発ツールのインストール
ここでは、2 つのテキスト エディターをインストールする方法について説明します。 gedit は、gnome デスクトップ環境用の既定のテキスト エディターです。 汎用テキスト エディターとして設計されています。 Visual Studio Code は、デバッグとソース管理の統合のサポートが組み込まれているテキスト エディターです。

> [!NOTE]
> さまざまなテキスト エディターを使用できます。 Visual Studio Code と gedit は、2 つの例にすぎません。

1. [gedit](https://help.gnome.org/users/gedit/stable/) をインストールします。

    ```bash
    sudo apt-get install gedit
    ```
1. [Visual Studio Code](https://code.visualstudio.com/) をインストールします。  Visual Studio Code は、Snap Store を使用してインストールできます。  別のインストール オプションについては、[Visual Studio Code の代替ダウンロード](https://code.visualstudio.com/#alt-downloads)のページを参照してください。

    ```bash
    sudo snap install vscode --classic 
    ```

    テンプレートが更新され、ラボを完了するために必要なプログラミング言語と開発ツールの両方が追加されます。 テンプレート イメージをラボに発行できるようになります。 テンプレート ページの **[発行]** ボタンを選択して、テンプレートをラボに発行します。  

## <a name="cost"></a>コスト 
このラボのコストを見積もるには、次の例を使用できます。
 
20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります: 

学生 25 人 * (20 + 10) 時間 * 20 ラボ ユニット * 0.01 USD/時間 = 150 USD

価格の詳細については、次のドキュメントを参照してください: 「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」。

## <a name="conclusion"></a>まとめ
この記事では、スクリプト作成クラスのラボを作成する手順について説明しました。 この記事では、Linux マシンでの Ruby スクリプト ツールの設定に注目しましたが、Linux 上の Python などの他のスクリプト クラスにも同じ設定を使用できます。

## <a name="next-steps"></a>次のステップ
次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。 





