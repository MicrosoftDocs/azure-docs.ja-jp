---
title: Azure Lab Services を使用して MATLAB を教えるためのラボを設定する | Microsoft Docs
description: Azure Lab Services を使用して MATLAB を教えるためのラボを設定する方法について説明します。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445000"
---
# <a name="setup-a-lab-to-teach-matlab"></a>MATLAB を教えるためのラボを設定する

[MATLAB](https://www.mathworks.com/) は、Matrix laboratory の略で、[MathWorks](https://www.mathworks.com/products/matlab.html) のプログラミング プラットフォームです。  計算能力と視覚化を組み合わせたツールで、数学、エンジニアリング、物理、および化学の分野でよく利用されています。

[Campus-Wide License](https://www.mathworks.com/academia/tah-support-program/administrators.html) を使用する場合は、[MATLAB インストーラー ファイルのダウンロード](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine)に関するページの手順を参考に、テンプレート マシンに MATLAB インストール ファイルをダウンロードしてください。  

この記事では、MATLAB クライアント ソフトウェアとライセンス サーバーを使用するクラスを設定する方法について説明します。

## <a name="license-server"></a>ライセンス サーバー

実際のラボ用にテンプレート マシンを変更する前に、[ネットワーク ライセンス マネージャー](https://www.mathworks.com/help/install/administer-network-licenses.html) ソフトウェアを実行するようにサーバーを設定する必要があります。  これらの手順は、ユーザーはライセンス キーのプールを共有できるよう MATLAB のネットワーク ライセンス オプションを選択する機関にのみ適用されます。  また、後のために、ライセンス ファイルとファイル インストール キーも保存しておく必要があります。  ライセンス ファイルをダウンロードする方法の詳細については、「[インターネット接続を使用したネットワーク ライセンス マネージャーのインストール](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)」に記載されている最初の手順を参照してください。

ライセンス サーバーのインストール方法に関する詳細な手順については、「[インターネット接続を使用したネットワーク ライセンス マネージャーのインストール](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)」を参照してください。  借入を有効にするには、「[ライセンスの借入](https://www.mathworks.com/help/install/license/borrow-licenses.html)」の記事を参照してください。

ライセンス サーバーがオンプレミス ネットワークまたは Azure 内のプライベート ネットワークに配置されている場合は、ご自分の[ラボ アカウント](tutorial-setup-lab-account.md)に[仮想ネットワークをピアリングする](how-to-connect-peer-virtual-network.md)ことを忘れないでください。  ネットワーク ピアリングは、ラボを作成して、ラボ仮想マシンがライセンス サーバーにアクセスできるようになる前に行う必要があります。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションが必要です。  Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のアカウントを使用することができます。  新しいラボ アカウントを作成するには、[ラボ アカウントの設定に関するチュートリアル](tutorial-setup-lab-account.md)を参照してください。

新しいラボを作成するには、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)に従ってください。  次の設定を適用します。

| 仮想マシンのサイズ | Image |
| -------------------- | ----- |
| Medium | Windows 10 |

MATLAB は他のオペレーティング システムでもサポートされています。  詳細については、[MATLAB のシステム要件](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)に関するページをご覧ください。

> [!WARNING]
> ラボを作成する前に、ライセンス サーバーの仮想ネットワークにラボ アカウントの[仮想ネットワークをピアリングする](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)ことを忘れないでください。

## <a name="template-machine"></a>テンプレート マシン

テンプレート マシンを作成したら、マシンを起動して接続し、次の主要なタスクを実行します。

1. MATLAB クライアント ソフトウェアのインストール ファイルをダウンロードします。
2. ファイル インストール キーを使用して MATLAB をインストールします。

MATLAB のインストールは、複数パート プロセスです。  最初のパートでは、インストールする MATLAB などの製品のファイルをダウンロードします。  ファイル インストール キーを使用するには、インストールする製品のインストール ファイルがすべて事前にダウンロードされている必要があります。  2 番目のパートでは、テンプレート VM 上に MATLAB ソフトウェアをインストールし、ソフトウェアをアクティブ化します。  テンプレート VM がライセンス サーバーを使用してアクティブ化するように構成されている場合、学生の VM も同じようになります。

### <a name="download-installation-files"></a>インストール ファイルをダウンロードする

インストール ファイルをダウンロードし、ライセンス ファイルとファイル インストール キーを取得するには、ライセンス管理者である必要があります。  以下は、インストール ファイルのダウンロード手順です。

1. [https://www.mathworks.com](https://www.mathworks.com) のご自分のアカウントにログインします。
2. **[My Account]\(アカウント\)** を選択します。
3. アカウント ページの **[My Software]\(ソフトウェア\)** セクションで、ラボ用に設定されているネットワーク ライセンス マネージャーにアタッチされているライセンスをクリックします。
4. ライセンスの詳細ページで、 **[Download Products]\(製品のダウンロード\)** をクリックします。
5. インストーラーが自己抽出するのを待ちます。
6. インストーラーを起動します。  
7. **[Sign in to your MathWorks Account]\(MathWorks アカウントでログインする\)** ページでご自分の MathWorks アカウントを入力します。
8. **[MathWorks License Agreement]\(MathWorks のライセンス条項\)** ページで、使用条件に同意し、 **[Next]\(次へ\)** をクリックします。
9. **[Advanced Options]\(詳細オプション\)** ドロップダウンをクリックし、 **[I want to download without installing]\(インストールせずにダウンロードする\)** を選択します。
10. **[Select destination folder]\(保存先フォルダーの選択\)** で **[Next]\(次へ\)** をクリックします。
11. MATLAB をインストールするコンピューターのプラットフォームとして **[Windows]** を選択します。
12. **[Select product]\(製品の選択\)** ページで、インストールする他のすべての MathWorks 製品と共に MATLAB が選択されていることを確認します。
13. **[Confirm Selections and Download]\(選択内容の確認とダウンロード\)** ページで、 **[Begin Download]\(ダウンロードの開始\)** をクリックします。  
14. 選択した製品がダウンロードされるまで待ちます。  **[完了]** をクリックします。

MathWorks の Web サイトから ISO イメージをダウンロードすることもできます。

1. [https://www.mathworks.com](https://www.mathworks.com) のご自分のアカウントにログインします。
2. [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads) に移動します。
3. インストールする MATLAB のリリースを選択します。
4. [Related links]\(関連リンク\) の下にある [Get {version}.iso image]\({version}.iso イメージを入手する\) リンクをクリックします。{version} は R2020a などです。
5. Windows 用の青い **リリースのダウンロード** リンクをクリックします。

### <a name="run-installer"></a>インストーラーを実行する

ファイルがダウンロードされたら、2 番目の手順としてインストーラーを実行します。 ここでも、この手順を実行するにはライセンス管理者である必要があります。  ファイル インストール キーを使用して MATLAB をインストールできるのはライセンス管理者だけです。

1. ダウンロードしたライセンス ファイルをチェックし、SERVER 行にライセンス サーバーが正しくリストされていることを確認します。  ライセンス ファイルの書式設定方法の詳細については、「[ネットワーク ライセンスの更新](https://www.mathworks.com/help/install/ug/network-license-files.html)」、「[ライセンスの借入](https://www.mathworks.com/help/install/license/borrow-licenses.html)」、および[ホスト ID の検索](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license)に関する記事を参照してください。
2. MATLAB のインストーラーを起動します。
3. **[Sign in to your MathWorks Account]\(MathWorks アカウントでログインする\)** ページでご自分の MathWorks アカウントを入力します。
4. **[MathWorks License Agreement]\(MathWorks のライセンス条項\)** ページで、使用条件に同意し、 **[Next]\(次へ\)** をクリックします。
5. **[Advanced Options]\(詳細オプション\)** ドロップダウンをクリックし、 **[I have a File Installation Key]\(ファイル インストール キーを持っている\)** を選択します。
6. **[Install using File Installation Key]\(ファイル インストール キーを使用したインストール\)** ページで、ライセンス サーバーのファイル インストール キーを入力します。   **[次へ]** をクリックします。
7. **[Select License File]\(ライセンス ファイルの選択\)** ページで、インストール ファイルをダウンロードするときに保存したライセンス ファイルに移動します。
8. **[Select Destination Folder]\(保存先フォルダーの選択\)** ページで **[Next]\(次へ\)** をクリックします。
9. **[Select product]\(製品の選択\)** ページで **[Next]\(次へ\)** をクリックします。
10. **[Select Options]\(オプションの選択\)** ページで、 **[Next]\(次へ\)** をクリックします。
11. **[Confirm Selections and Install]\(選択内容の確認とインストール\)** ページで、 **[Begin Install]\(インストールの開始\)** をクリックします。
12. **[Installation Complete]\(インストールの完了\)** ページで、 **[Activate MATLAB]\(MATLAB のアクティベーション\)** チェック ボックスがオンになっていることを確認します。  **[完了]** をクリックします。

## <a name="cost-estimate"></a>コストの見積もり

このクラスの考えられるコスト見積もりを検討しましょう。  この見積もりには、ライセンス サーバーの実行に伴うコストは含まれません。  クラスの生徒数は 25 人とします。  予定された授業時間は 20 時間です。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。  ここで選択した仮想マシンのサイズは、55 ラボ ユニットの "中" です。

このクラスで考えられるコスト見積もりの例を次に示します。

25 人の学生 \* (予定された 20 時間 + 割り当てられた 10 時間) \* 55 ラボ ユニット \* 0.01 USD/時間 = 412.50 USD

>[!IMPORTANT]
> コストの見積もりはあくまでも例です。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。  

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートを作成、管理、公開する](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
