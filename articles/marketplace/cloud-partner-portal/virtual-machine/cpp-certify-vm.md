---
title: Azure Marketplace 向けに VM イメージを認定する | Microsoft Docs
description: VM イメージをテストし送信して､Azure Marketplace の認定を受ける方法を説明しています。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190392"
---
# <a name="certify-your-vm-image"></a>VM イメージを認定する

仮想マシン (VM) を作成してデプロイしたら、VM イメージをテストし、送信して、Azure Marketplace の認定を受ける必要があります。 この記事では、"*Azure 認定用の認定テスト ツール*" の入手場所を紹介すると共に、このツールを使用して VM イメージを認定する方法や、VHD が置かれている Azure コンテナーに検証結果をアップロードする方法について説明します。 


## <a name="download-and-run-the-certification-test-tool"></a>認定テスト ツールをダウンロードして実行する

Azure 認定用の認定テスト ツールはローカルの Windows マシン上で動作しますが、そのテスト対象は Azure ベースの Windows と Linux VM です。  ユーザーの VM イメージが Microsoft Azure に適合していること、つまり VHD を準備するにあたってのガイダンスと要件が満たされていることを確認するツールとなります。 VM 認定の申請は、このツールの出力として得られる互換性チェックを [Cloud パートナー ポータル](https://cloudpartner.azure.com)にアップロードすることで行います。

1. 次のリンクから、[Azure 認定用の認定テスト ツール](https://www.microsoft.com/download/details.aspx?id=44299)の最新版をダウンロードしてインストールします。 
2. 認定ツールを開き、**[新規テストの開始]** をクリックします。
3. **[テスト情報]** 画面で、実行する**テストの名前**を入力します。
4. VM の**プラットフォーム**として、`Windows Server` または `Linux` を選択します。 ここでのプラットフォームの選択は、以降のオプションに影響します。
5. 該当するデータベース サービスを VM で使用している場合、**[Test for Azure SQL Database]\(Azure SQL Database のテスト\)** チェック ボックスをオンにします。

   ![認定テスト ツールの初期ページ](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>VM イメージに認定ツールを接続する

  Windows ベースの VM には [PowerShell](https://docs.microsoft.com/powershell/) で接続し、Linux VM には [SSH.Net](https://www.ssh.com/ssh/protocol/) で接続します。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Linux VM イメージに認定ツールを接続する

1. **[SSH 認証]** モード (`Password Authentication` または `key File Authentication`) を選択します。
2. パスワード ベースの認証を使用する場合は、**[VM DNS Name]\(VM の DNS 名\)**、**[ユーザー名]**、**[パスワード]** の値を入力します。  既定の **SSH ポート**番号は、必要に応じて変更できます。

     ![Linux VM イメージのパスワード認証](./media/publishvm_026.png)

3. キー ファイル ベースの認証を使用する場合は、**VM の DNS 名**、**ユーザー名**、**秘密キー**の場所の値を入力します。  必要に応じて、**パスフレーズ**を入力したり、既定の **SSH ポート**番号を変更したりすることができます。

     ![Linux VM イメージのファイル認証](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Windows ベースの VM イメージへの認定ツールの接続**
1. 完全修飾 **VM DNS 名** (`MyVMName.Cloudapp.net` など) を入力します。
2. **ユーザー名**と**パスワード**の値を入力します。

   ![Windows VM イメージのパスワード認証](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>認定テストを実行する

認定ツールで VM イメージのパラメーター値を入力したら、**[接続のテスト]** を選択して、VM への接続が有効であることを確認します。 接続の確認後、**[次へ]** をクリックしてテストを開始します。  テストが完了すると、テスト結果 (合格/不合格/警告) のテーブルが表示されます。  Linux VM のテスト結果の例を次に示します。 

![Linux VM イメージの認定テスト結果](./media/publishvm_029.png)

いずれかのテストが失敗した場合、イメージは認定 "*されません*"。 その場合は、要件と失敗のメッセージを確認し、指示された変更を加えたうえで、テストを再実行してください。 

自動テストが済むと、**アンケート**画面で VM イメージに関する追加情報の入力を求められます。  この画面は、必要事項を入力するための 2 つのタブで構成されます。  **[General Assessment]\(一般的な評価\)** タブには、**True/False** 形式の質問が、**[Kernel Customization]\(カーネルのカスタマイズ\)** には、複数選択形式の質問と自由形式の質問が表示されます。  両方のタブの質問に回答して、**[次へ]** を選択します。

![認定ツールのアンケート](./media/publishvm_030.png)

最後の画面では、Linux VM イメージの SSH アクセス情報など、補足的な情報を入力できるほか、不合格となった評価についての例外認定を希望する場合は、その説明を入力できます。 

最後に **[レポートの生成]** をクリックすると、実行されたテスト ケースのテスト結果とログ ファイルおよびアンケートへの回答をダウンロードできます。 VHD と同じコンテナーに結果を保存します。

![認定テスト結果の保存](./media/publishvm_031.png)


## <a name="next-steps"></a>次の手順

次に、マーケットプレースに提出する [各 VHD の URI (Uniform Resource Identifier) を作成](./cpp-get-sas-uri.md)します。 
