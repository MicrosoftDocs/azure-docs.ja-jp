---
title: Azure で Micro Focus Enterprise Server 5.0 および Enterprise Developer 5.0 をインストールする | Microsoft Docs
description: この記事では、Microsoft Azure で Micro Focus Enterprise Server 5.0 と Enterprise Developer 5.0 をインストールする方法について説明します。
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 5001f5df8ebab3e8366a2b31b1c5eb8ccc69c985
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951353"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Azure で Micro Focus Enterprise Server 5.0 および Enterprise Developer 5.0 をインストールする

この記事では、Microsoft Azure で [Micro Focus Enterprise Server 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) と [Micro Focus Enterprise Developer 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) を設定する方法について説明します。

Azure 上の一般的なワークロードは、開発およびテスト環境です。 このシナリオは、非常にコスト効率が高く、デプロイや解体が容易なため、一般的です。 Enterprise Server により、Micro Focus は、使用可能な最大のメインフレーム リホスト プラットフォームの 1 つを作成しました。 Windows または Linux 仮想マシン (VM) のどちらかを使用して、Azure 上のより安価な x86 プラットフォームで z/OS ワークロードを実行できます。

この設定では、Microsoft SQL Server 2017 が既にインストールされた、Azure Marketplace の Windows Server 2016 のイメージを実行している Azure VM を使用します。 この設定は Azure Stack にも適用されます。

Enterprise Server に対応する開発環境は、Microsoft Visual Studio 2017 以降、Visual Studio Community (無料でダウンロード可能)、Eclipse のいずれかで実行される Enterprise Developer です。 この記事では、Visual Studio 2017 以降がインストールされた Windows Server 2016 仮想マシンを使用してこれをデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件を確認してください。

-   Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

-   Micro Focus ソフトウェアおよび有効なライセンス (または試用版ライセンス)。 Micro Focus の既存の顧客である場合は、Micro Focus の担当者に問い合わせてください。 そうでない場合は、[試用版を要求](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)してください。

-   [Enterprise Server と Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/) のドキュメントを入手します。

    > [!Note]
    > 以下のように、VM へのアクセスを制御するためのオプションはいくつかあります。
    > -   [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) を設定することをお勧めします。
    > -   [サイト間仮想プライベート ネットワーク (VPN)](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) トンネル。
    > -   ジャンプボックス VM。

## <a name="install-enterprise-server"></a>Enterprise Server をインストールする

1.  セキュリティや管理容易性を向上させるために、このプロジェクトだけのための新しいリソース グループ (たとえば、**RGMicroFocusEntServer**) を作成することを考慮してください。 Azure での名前の最初の部分を使用してリソースの種類を選択することにより、一覧内で見つけやすくします。

2.  仮想マシンを作成します。 Azure Marketplace から、必要な仮想マシンとオペレーティング システムを選択します。 推奨される設定を次に示します。

    -   **Enterprise Server:** Windows Server 2016 と SQL Server 2017 がインストールされた **ES2 v3 VM** (2 つの vCPU と 16 GB メモリを装備) を選択します。 このイメージは、Azure Marketplace から入手できます。 Enterprise Server は、Azure SQL Database も使用できます。

    -   **Enterprise Developer:** Windows 10 と Visual Studio がインストールされた **B2ms VM** (2 つの vCPU と 8 GB メモリを装備) を選択します。 このイメージは、Azure Marketplace から入手できます。

3.  **[基本]** セクションで、ユーザー名とパスワードを入力します。 VM に使用する **[サブスクリプション]** と **[場所]/[リージョン]** を選択します。 リソース グループとして **RGMicroFocusEntServer** を選択します。

4.  互いに通信できるように、両方の VM を同じ仮想ネットワークに配置します。

5.  残りの設定については既定値を受け入れます。 これらの VM の管理者のために作成したユーザー名とパスワードを覚えておいてください。

6.  仮想マシンが作成されたら、Enterprise Server コンピューターでは **9003、86**、HTTP の場合は **80**、リモート デスクトップ プロトコル (RDP) の場合は **3389**、および Developer コンピューターでは **3389** の受信ポートを開きます。

7.  Enterprise Server 仮想マシンにサインインするには、Azure portal で [ES2 v3 VM] を選択します。 **[概要]** セクションに移動し、 **[接続]** を選択して RDP セッションを起動します。 VM のために作成した資格情報を使用してサインインします。

8.  RDP セッションから、次の 2 つのファイルを読み込みます。 Windows を使用しているため、これらのファイルを RDP セッションにドラッグ アンド ドロップできます。

    -   `es\_50.exe`、Enterprise Server のインストール ファイル。

    -   `mflic`、対応するライセンス ファイル (これがないと Enterprise Server が読み込まれません)。

9.  ファイルをダブルクリックしてインストールを開始します。 最初のウィンドウで、インストールの場所を選択し、使用許諾契約書に同意します。

    ![スクリーンショットに、インストールを開始できる、Micro Focus Enterprise Server のダイアログ ボックスが示されています。](media/install-image-1.png)

    設定が完了すると、次のメッセージが表示されます。

    ![スクリーンショットに、Micro Focus Enterprise Server のダイアログ ボックスに成功メッセージが示されています。](media/install-image-2.png)

 ### <a name="check-for-updates"></a>更新プログラムをチェックする

インストール後、Enterprise Server と共に Microsoft C++ 再頒布可能パッケージおよび .NET Framework がインストールされるなどのいくつかの前提条件があるため、追加の更新プログラムがないかどうかチェックするようにしてください。

### <a name="upload-the-license"></a>ライセンスをアップロードする

1.  Micro Focus のライセンス管理を起動します。

2.  **[スタート]** \> **[Micro Focus License Manager]\(Micro Focus ライセンス マネージャー\)** \> **[License Administration]\(ライセンス管理\)** の順に選択し、 **[インストール]** タブをクリックします。アップロードするライセンス形式の種類 (ライセンス ファイルまたは 16 文字のライセンス コード) を選択します。 たとえば、ファイルの場合は、 **[License file]\(ライセンス ファイル\)** で、前に VM にアップロードされた `mflic` ファイルを参照し、 **[Install Licenses]\(ライセンスのインストール\)** を選択します。

    ![スクリーンショットに、[Install Licenses]\(ライセンスのインストール\) を選択できる Micro Focus の [License Administration]\(ライセンス管理\) ダイアログ ボックスが示されています。](media/install-image-3.png)

3.  Enterprise Server が読み込まれることを確認します。 URL: `http://localhost:86/` を使用して、ブラウザーから [Enterprise Server Administration]\(Enterprise Server の管理\) サイトを起動してみてください。 [Enterprise Server Administration]\(Enterprise Server の管理\) ページが次のように表示されます。

    ![[Enterprise Server Administration]\(Enterprise Server の管理\) ページ](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>開発者のコンピューターに Enterprise Developer をインストールする

1.  前に作成したリソース グループ (たとえば、**RGMicroFocusEntServer**) を選択した後、開発者イメージを選択します。

2.  仮想マシンにサインインするには、 **[概要]** セクションに移動し、 **[接続]** を選択します。 このサインインにより、RDP セッションが起動されます。 VM のために作成した資格情報を使用してサインインします。

3.  RDP セッションから、次の 2 つのファイルを読み込みます (必要に応じて、ドラッグ アンド ドロップします)。

    -   `edvs2017.exe`、Enterprise Server のインストール ファイル。

    -   `mflic`、対応するライセンス ファイル (これがないと Enterprise Developer が読み込まれません)。

4.  **edvs2017.exe** ファイルをダブルクリックしてインストールを開始します。 最初のウィンドウで、インストールの場所を選択し、使用許諾契約書に同意します。 必要な場合は、 **[Install Rumba 9.5]\(Rumba 9.5 のインストール\)** を選択して、このターミナル エミュレーターをインストールします (これは、おそらく必要ありません)。

    ![[Micro Focus Enterprise Developer for Visual Studio 2017 Setup]\(Micro Focus Enterprise Developer for Visual Studio 2017 の設定\) ダイアログ ボックス](media/install-image-5.png)

5.  設定が完了すると、次のメッセージが表示されます。

    ![設定成功のメッセージ](media/install-image-6.png)

6.  Enterprise Server の場合と同様に Micro Focus ライセンス マネージャーを起動します。 **[スタート]** \> **[Micro Focus License Manager]\(Micro Focus ライセンス マネージャー\)** \> **[License Administration]\(ライセンス管理\)** を選択し、 **[インストール]** タブをクリックします。

7.  アップロードするライセンス形式の種類 (ライセンス ファイルまたは 16 文字のライセンス コード) を選択します。 たとえば、ファイルの場合は、 **[License file]\(ライセンス ファイル\)** で、前に VM にアップロードされた `mflic` ファイルを参照し、 **[Install Licenses]\(ライセンスのインストール\)** を選択します。

    ![Micro Focus の [License Administration]\(ライセンス管理\) ダイアログ ボックス](media/install-image-7.png)

Enterprise Developer が読み込まれたら、Azure への Micro Focus 開発およびテスト環境のデプロイは完了しました。

**次の手順**

-   [銀行デモ アプリケーションを設定する](./demo.md)

-   [Docker コンテナーで Enterprise Server を実行する](./run-enterprise-server-container.md)

-   [メインフレーム アプリケーションの移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)