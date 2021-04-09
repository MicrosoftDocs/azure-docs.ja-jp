---
title: Python と Jupyter Notebooks を使用してデータ サイエンスを教えるためのラボを設定する |Microsoft Docs
description: Python と Jupyter Notebooks を使用してデータサイエンスを教えるためのラボを設定する方法について説明します。
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648023"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python と Jupyter Notebook を使用してデータ サイエンスを教えるためのラボを設定する
この記事では、[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/) の使用方法を学生に教えるために必要なツールを使用して Lab Services 内にテンプレート仮想マシン (VM) を設定する方法と、学生が各自の仮想マシン (VM) 上で各自のノートブックに接続する方法について概説します。

Jupyter Notebook は、ノートブックと呼ばれる単一のキャンバス上でリッチ テキストと実行可能な Python ソース コードを簡単に組み合わせることができるオープンソース プロジェクトです。 ノートブックを実行すると、入力と出力の線形レコードが生成されます。 これらの出力には、テキスト、情報テーブル、散布図などを含めることができます。

## <a name="set-up-the-lab"></a>ラボを設定する

### <a name="lab-configuration"></a>ラボの構成
このラボを設定するには、Azure サブスクリプションとラボ アカウントにアクセスする必要があります。 組織の管理者に相談して、既存の Azure サブスクリプションにアクセスできるかどうかを確認してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

Azure サブスクリプションを用意したら、Azure Lab Services で新しいラボ アカウントを作成します (チュートリアル: [ラボ アカウントの設定](tutorial-setup-lab-account.md)の指示に従ってください)。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定
次の表に記載されているラボ アカウントの設定を有効にします。 マーケットプレース イメージの有効化の詳細については、[ラボ作成者が利用できる Marketplace イメージの指定](specify-marketplace-images.md)に関する記事を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
| Marketplace イメージ | ラボ アカウント内で、お使いのオペレーティング システムのニーズに基づいて、いずれかの Azure Marketplace イメージを有効にします。 <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> この記事では、Jupyter Notebook で事前構成済みであるため、Azure マーケットプレースで入手できる Data Science 仮想マシン イメージを使用します。 ただし、これらのイメージには、データ サイエンス用の他の多くの開発ツールとモデリング ツールも含まれています。 これらの追加のツールが不要で、Jupyter Notebook だけを使用する簡易セットアップを行う場合は、カスタム VM イメージを作成します。 例については、[Azure での JupyperHub のインストール](http://tljh.jupyter.org/en/latest/install/azure.html)に関する記事を参照してください。 カスタム イメージを作成したら、Azure Lab Services 内でそのイメージを使用できるように共有イメージ ギャラリーにアップロードします。 詳細については、[Azure Lab Services での共有イメージ ギャラリーの使用](how-to-attach-detach-shared-image-gallery.md)に関する記事を参照してください。 

### <a name="lab-settings"></a>ラボの設定
クラスルーム ラボを設定するときに、次の表に示すように、**仮想マシンのサイズ** と **仮想マシン イメージ** の設定を構成します。 クラスルーム ラボを作成する手順については、「[クラスルーム ラボをセットアップする](tutorial-setup-classroom-lab.md)」を参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ | 
| 仮想マシンのサイズ | <p>ここで選択するサイズは、実行するワークロードによって異なります。</p><ul><li>小規模または大規模 - Jupyter Notebook にアクセスする基本的なセットアップに適しています</li><li>小規模 GPU (コンピューティング) – 人工知能やディープ ラーニングのような、コンピューティング集中型およびネットワーク集中型のアプリケーションに最適です</li></ul> | 
| 仮想マシン イメージ | <p>オペレーティング システムのニーズに応じて、次のいずれかのイメージを選択します。</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>テンプレート仮想マシン
ラボを作成すると、選択した仮想マシンのサイズとイメージに基づいてテンプレート VM が作成されます。 このテンプレート VM に、このクラスの学生に対して提供するすべてのものを構成します。 詳細については、[テンプレート仮想マシンを管理する方法](how-to-create-manage-template.md)に関する記事を参照してください。 

既定では、Data Science VM のイメージには、この種類のクラスに必要な多くのデータ サイエンス フレームワークとツールが付属しています。 たとえば、イメージには、以下が含まれます。

- [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/):データ サイエンティストが生データの取得、計算の実行、および結果の表示をすべて同じ環境で行えるようにする Web アプリです。 テンプレート VM でローカルで実行されます。  
- [Visual Studio Code](https://code.visualstudio.com/):ノートブックを作成およびテストする際に、豊富な対話型エクスペリエンスを提供する統合開発環境 (IDE) です。 詳細については、「[Visual Studio Code での Jupyter Notebooks の使用](https://code.visualstudio.com/docs/python/jupyter-support)」を参照してください。

### <a name="provide-notebooks-for-the-class"></a>クラス用のノートブックを提供する
次のタスクは、使用するノートブックを学生に提供することです。 独自のノートブックを提供するために、テンプレート VM にノートブックをローカルに保存できます。 

Azure Machine Learning のサンプルのノートブックを使用する場合は、[Jupyter Notebook で環境を構成する方法](../machine-learning/how-to-configure-environment.md#jupyter)に関するセクションをご覧ください。 

### <a name="optional-enable-graphical-desktop-for-linux"></a>省略可能: Linux 用にグラフィカル デスクトップを有効にする 
**Data Science 仮想マシン – Ubuntu** イメージには既に X2GO サーバーがプロビジョニングされており、クライアント接続を受け入れる準備ができています。 テンプレート VM を設定するときに、これ以上の手順は必要ありません。 

### <a name="publish-the-template-machine"></a>テンプレート マシンを発行する
テンプレートを発行すると、ラボに登録されている各学生に対して、テンプレート VM とテンプレートに設定したすべてのローカル ツールとノートブックのコピーが提供されます。

## <a name="how-students-connect-to-jupyter-notebooks"></a>学生が Jupyter Notebook に接続する方法
テンプレートが発行された後、各学生は、Jupyter Notebook を含めて、クラス向けに事前に構成されたすべてのものを備えた VM にアクセスできるようになります。 次のセクションで、学生が Jupyter Notebook に接続するためのさまざまな方法を示します。 

### <a name="for-windows-vms"></a>Windows VM の場合
Windows VM を提供している場合、学生は各自の VM に接続し、そこでローカルで使用できる Jupyter Notebook を使用する必要があります。 

Windows VM に接続するために、学生はリモート デスクトップ接続 (RDP) を使用できます。 詳しい手順については、[クラスルーム ラボへのアクセス方法](how-to-use-classroom-lab.md)に関するページを参照してください。 

Mac または Chromebook を使用する学生は、次の記事の手順に従って、Data Science Windows VM に接続できます。 

- [MAC から RDP を使用して VM に接続する](connect-virtual-machine-mac-remote-desktop.md)
- [Chromebook から RDP を使用して VM に接続する](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Linux VM の場合
学生に Linux VM を提供している場合、学生が VM 内の Jupyter Notebook に接続するために使用できるオプションは複数あります。

- VM に接続した後、Jupyter Notebook にローカルでアクセスする。
    - ターミナル セッション用の VM への SSH 接続
     - グラフィカル セッション用の VM への X2Go 接続
- SSH トンネリングを使用して、学生のローカル コンピューターから、VM 上の Jupyter サーバーに直接接続する。 

以下のセクションで、Jupyter Notebook に接続するためのこれらの方法の詳細について説明します。 

#### <a name="ssh-to-virtual-machine"></a>仮想マシンに SSH 接続する
学生は、ターミナル セッションから Linux VM に SSH 経由で接続できます。 詳しい手順については、[クラスルーム ラボへのアクセス方法](how-to-use-classroom-lab.md)に関するページを参照してください。 Windows クライアント コンピューターを使用している場合は、[PuTTY](https://www.putty.org/) をダウンロードして SSH クライアントを有効にするか、コマンド プロンプトから SSH への [Windows での OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse) を有効にする必要があります。 

1.  VM を起動します。
2.  VM が実行されたら、 **[接続]** をクリックします。SSH コマンド文字列を示すダイアログ ボックスが表示されます。これは、次の例のようになります。
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  コマンド プロンプトまたはターミナルにアクセスし、このコマンドを貼り付けてから **Enter** キーを押します。
4.  VM にサインインするためのパスワードを入力します。 

学生は、VM に接続された後、Jupyter Notebook にローカルでアクセスして実行できます。

#### <a name="x2go-to-virtual-machine"></a>仮想マシンに X2Go 接続する
**Data Science 仮想マシン – Ubuntu** イメージには既に X2GO サーバーがプロビジョニングされており、クライアント接続を受け入れる準備ができています。 学生が Linux マシンのグラフィカル デスクトップに接続するには、次の 1 回限りの手順に従って、クライアント コンピューターに X2Go を設定する必要があります。

1.  お使いのクライアント プラットフォーム向けの [X2Go クライアント](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)をダウンロードしてインストールします。
2.  [Azure Lab Services ポータル](https://labs.azure.com)で、接続先の Linux VM が起動されていることを確認します。
3.  VM が実行されたら、 **[接続]** をクリックします。SSH コマンド文字列を示すダイアログ ボックスが表示されます。これは、次の例のようになります。

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. この情報が得られたら、X2Go クライアント アプリを開き、新しいセッションを作成します。 
5.  **[セッションの基本設定]** ペインに次の値を入力します。
    - **セッション名**: どのようなものでもかまいませんが、ラボ VM の名前を使用することをお勧めします。
     - **ホスト**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **ログイン**: student
     - **SSH ポート**: 12345
     - **セッションの種類**: XFCE
6. **[OK]** を選択します。 

    > [!NOTE]
     > 新しい X2Go セッションを作成するときは、RDP ポート **ではなく**、必ず SSH ポートを使用してください。

次に、VM に接続するために、次の手順に従います。    

1.  X2Go クライアントで、接続先の VM をダブルクリックします。 

    ![X2Go クライアント](./media/class-type-jupyter-notebook/x2go-client.png)
2. VM に接続するためのパスワードを入力します (ファイアウォールをバイパスして接続を完了するには、X2Go アクセス許可の付与が必要な場合があります)。
3.  これで、Ubuntu Data Science VM のグラフィカル インターフェイスが表示されます。


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>VM 上の Jupyter サーバーへの SSH トンネル
学生によっては、自分のローカル コンピューターから VM 内の Jupyter サーバーに直接接続することを望む場合があります。 SSH プロトコルでは、ローカル コンピューターとリモート サーバー (ここでは学生のラボ VM) 間でのポート転送を有効にして、サーバー上の特定のポートで実行されているアプリケーションを、ローカル コンピューター上のマッピングポートに **トンネリング** されるようにすることができます。 学生は、次の手順に従って、ラボ VM 上の Jupyter サーバーへの SSH トンネリングを行う必要があります。

1.  [Azure Lab Services ポータル](https://labs.azure.com)で、接続先の Linux VM が起動されていることを確認します。
2.  VM が実行されたら、 **[接続]** をクリックします。SSH コマンド文字列を示すダイアログ ボックスが表示されます。これは、次の文字列のようになります。

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. ローカル コンピューターで、ターミナルまたはコマンド プロンプトを起動し、それに SSH 接続文字列をコピーします。 次に、`-L 8888:localhost:8888` をコマンド文字列に追加します。これにより、ポート間の **トンネル** が作成されます。 最終的な文字列は次のようになります。

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. **Enter** キーを押してコマンドを実行します。 
5.  プロンプトが表示されたら、ラボ VM に接続するためのパスワードを入力します。 
6.  VM に接続したら、次のコマンドを使用して Jupyter サーバーを起動します。 

    ```bash
     jupyter notebook
     ```
7. コマンドを実行すると、ターミナルまたはコマンド プロンプトに URL が表示されます。 この URL は次のようになります。

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. この URL をローカル コンピューターのブラウザーに貼り付けて、Jupyter Notebook に接続して操作します。 

    > [!NOTE]
    > Visual Studio Code でも、優れた [Jupyter Notebook の編集エクスペリエンス](https://code.visualstudio.com/docs/python/jupyter-support)が可能です。 [リモート Jupyter サーバーに接続する方法](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server)の指示に従って、前の手順と同じ URL を使用して、ブラウザーからではなく VS Code から接続できます。 


## <a name="cost-estimate"></a>コストの見積もり
このクラスの考えられるコスト見積もりを検討しましょう。 クラスの生徒数は 25 人とします。 予定された授業時間は 20 時間です。 また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。 ここで選択した VM のサイズは、[小規模 GPU (コンピューティング)] (139 のラボ ユニット) でした。 [小規模] (20 のラボ ユニット) または [中規模] (42 のラボ ユニット) を使用する場合は、次の式のラボ ユニット部分を適切な数値に置き換えることができます。  

このクラスで考えられるコスト見積もりの例を次に示します。25 人の学生 * (予定された 20 時間 + 割り当てられた 10 時間) * 139 のラボ ユニット * 0.01 USD/時間 = 1042.5 USD

価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ
この記事では、Jupyter Notebooks クラス用のラボを作成する手順について説明しました。 他の機械学習クラスにも同様のセットアップを使用できます。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)