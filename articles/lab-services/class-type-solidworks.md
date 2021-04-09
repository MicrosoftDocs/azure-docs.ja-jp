---
title: Azure Lab Services でエンジニアリング用に SOLIDWORKS ラボを設定する | Microsoft Docs
description: SOLIDWORKS を使用して、エンジニアリング コース向けのラボを設定する方法について説明します。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626106"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>SOLIDWORKS を使用するエンジニアリング クラス用にラボを設定する

[SOLIDWORKS](https://www.solidworks.com/) には、ソリッド オブジェクトをモデル化するための 3D コンピューター支援設計 (CAD) 環境が用意されており、さまざまなエンジニアリング分野で使用されます。  SOLIDWORKS を使用すると、エンジニアは設計の作成、視覚化、シミュレーション、ドキュメント化を簡単に行うことができます。

大学で一般的に使用されるライセンス オプションは、SOLIDWORKS のネットワーク ライセンスです。   このオプションでは、ユーザーはライセンス サーバーによって管理されるライセンスのプールを共有します。  この種類のライセンスは、"フローティング" ライセンスと呼ばれることもあります。これは、同時実行ユーザー数に対して十分なライセンスだけを用意する必要があるためです。  ユーザーが SOLIDWORKS を使い終わると、そのライセンスは一元管理されているライセンス プールに戻され、他のユーザーが再利用できるようになります。

この記事では、SOLIDWORKS 2019 とネットワーク ライセンスを使用するクラスを設定する方法について説明します。

## <a name="license-server"></a>ライセンス サーバー

SOLIDWORKS のネットワーク ライセンスを使用するには、ライセンス サーバーに SolidNetWork License Manager をインストールして、アクティブ化する必要があります。  このライセンス サーバーは、通常、オンプレミスのネットワークまたは Azure 内のプライベート ネットワークのいずれかに配置されます。  サーバーで SolidNetWork License Manager を設定する方法の詳細については、SOLIDWORKS インストール ガイドの「[Installing and Activating a License Manager](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm)」 (License Manager のインストールとアクティベーション) を参照してください。  これを設定するときは、後の手順で必要になるため、**ポート番号** と [**シリアル番号**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm)を記録しておいてください。

ライセンス サーバーを設定した後は、[仮想ネットワーク (VNet)](./how-to-connect-peer-virtual-network.md) を[ラボ アカウント](./tutorial-setup-lab-account.md)にピアリングする必要があります。  ネットワーク ピアリングは、ラボの仮想マシンがライセンス サーバーに、またその逆にアクセスできるように、ラボを作成する前に行う必要があります。

> [!NOTE]
> ファイアウォールで適切なポートが開かれていて、ラボの仮想マシンとライセンス サーバーの間の通信が許可されることを確認する必要があります。  たとえば、ライセンス サーバーのファイアウォールに受信と送信の規則を追加する方法が示されている「[Modifying License Manager Computer Ports for Windows Firewall (Windows ファイアウォールのためのライセンス マネージャー コンピューターの変更)](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm)」の手順を参照してください。  また、ラボの仮想マシンに対するポートを開くことが必要な場合もあります。  ラボのパブリック IP アドレスを取得する方法など、これの詳細については、[ラボ用のファイアウォールの設定](./how-to-configure-firewall-settings.md)に関する記事の手順に従ってください。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 マーケットプレース イメージを有効にする方法の詳細については、[ラボ作成者が利用できる Marketplace イメージを指定する方法](./specify-marketplace-images.md)に関する記事を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
|Marketplace イメージ| ラボ アカウント内で使用する Windows 10 Pro イメージを有効にします。|

> [!NOTE]
> SOLIDWORKS では、Windows 10 だけでなく、他のバージョンの Windows もサポートされています。  詳細については、[SOLIDWORKS のシステム要件](https://www.solidworks.com/sw/support/SystemRequirements.html)に関するページをご覧ください。

### <a name="lab-settings"></a>ラボの設定

クラスルーム ラボを設定するときは、次の表の設定を使用します。 クラスルーム ラボの作成方法の詳細については、クラスルーム ラボの設定に関するチュートリアルを参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| **小規模 GPU (視覚化)** 。  この VM は、OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。|  
|仮想マシン イメージ| Windows 10 Pro|

> [!NOTE]
> **小規模 GPU (視覚化)** 仮想マシン サイズは、高パフォーマンスのグラフィックス エクスペリエンスを実現するように構成されています。  この仮想マシン サイズの詳細については、[GPU を使用したラボの設定方法](./how-to-setup-lab-gpu.md)に関する記事を参照してください。

> [!WARNING]
> ラボを作成する **前** に、ライセンス サーバーの仮想ネットワークにラボ アカウントの [仮想ネットワークをピアリングする](./how-to-connect-peer-virtual-network.md)ことを忘れないでください。

## <a name="template-virtual-machine-configuration"></a>テンプレート仮想マシンの構成

このセクションの手順では、SOLIDWORKS インストール ファイルをダウンロードしてクライアント ソフトウェアをインストールすることにより、テンプレート仮想マシンを設定する方法について説明します。

1. テンプレート仮想マシンを起動し、RDP を使用してマシンに接続します。

1. SOLIDWORKS クライアント ソフトウェアのインストール ファイルをダウンロードします。 ダウンロードには 2 つのオプションがあります。
   - [SOLIDWORKS カスタマー ポータル](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)からダウンロードします。
   - サーバー上のディレクトリからダウンロードします。  このオプションを使用した場合は、テンプレート仮想マシンからサーバーにアクセスできることを確認する必要があります。  たとえば、このサーバーは、ラボ アカウントとピアリングされている同じ仮想ネットワークに配置されている場合があります。
  
    詳細については、SOLIDWORKS インストール ガイドの [SOLIDWORKS での個人コンピューターへのインストール](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0)に関するページを参照してください。

1. インストール ファイルをダウンロードしたら、SOLIDWORKS Installation Manager を使用してクライアント ソフトウェアをインストールします。 詳細については、SOLIDWORKS インストール ガイドの「[Installing a License Client](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm)」 (ライセンス クライアントのインストール) を参照してください。

    > [!NOTE]
    > **[Add Server]\(サーバーの追加\)** ダイアログ ボックスで、ライセンス サーバーに使用する **ポート番号** と、ライセンス サーバーの名前または IP アドレスを入力するように求められます。

## <a name="cost"></a>コスト

このクラスの考えられるコスト見積もりを検討しましょう。 この見積もりには、ライセンス サーバーの実行に伴うコストは含まれません。 クラスの生徒数は 25 人とします。 予定された授業時間は 20 時間です。 また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。 ここで選択した仮想マシンのサイズは **小規模 GPU (視覚化)** であり、160 ラボ ユニットです。

25 人の学生 \* (予定された 20 時間 + 割り当てられた 10 時間) \* 160 ラボ ユニット * 0.01 USD/時間 = 1200.00 USD

>[!IMPORTANT]
> コストの見積もりはあくまでも例です。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。  

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)