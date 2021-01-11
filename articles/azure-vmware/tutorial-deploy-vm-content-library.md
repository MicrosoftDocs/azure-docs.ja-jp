---
title: チュートリアル:Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する
description: この Azure VMware Solution のチュートリアルでは、コンテンツ ライブラリを作成して、Azure VMware Solution プライベート クラウドに VM をデプロイします。
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 3abaafac0dbd6f3537d2ca30a093627230780eb5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750546"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>チュートリアル:Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する

コンテンツ ライブラリは、ライブラリ項目の形式でコンテンツを格納および管理します。 1 つのライブラリ項目は、仮想マシン (VM) をデプロイするために使用する 1 つ以上のファイルで構成されます。 
 
このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * コンテンツ ライブラリを作成する
> * ISO イメージをコンテンツ ライブラリにアップロードする
> * コンテンツ ライブラリで ISO を使用して VM をデプロイする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、NSX-T 論理スイッチ セグメントと管理された DHCP サービスが必要です。  詳細については、[Azure VMware Solution プレビューで DHCP を管理する方法](manage-dhcp.md)に関する記事を参照してください。

## <a name="create-a-content-library"></a>コンテンツ ライブラリを作成する

1. オンプレミスの vSphere クライアントから、 **[メニュー] > [コンテンツ ライブラリ]** を選択します。

   ![[メニュー] -> [コンテンツ ライブラリ] の選択](./media/content-library/vsphere-menu-content-libraries.png)

1. 新しいコンテンツ ライブラリを作成するには、 **[追加]** ボタンをクリックします。

   ![新しいコンテンツ ライブラリを作成するには、[追加] ボタンをクリックします。](./media/content-library/create-new-content-library.png)

1. vCenter サーバーの名前を指定して IP アドレスを確認し、 **[次へ]** を選択します。

   ![任意の名前とメモを指定し、[次へ] を選択します。](./media/content-library/new-content-library-step1.png)

1. **[ローカル コンテンツ ライブラリ]** を選択し、 **[次へ]** を選択します。

   ![この例では、ローカル コンテンツ ライブラリを作成し、[次へ] を選択します。](./media/content-library/new-content-library-step2.png)

1. コンテンツ ライブラリを格納するデータストアを選択し、 **[次へ]** を選択します。

   ![コンテンツ ライブラリをホストするデータストアを選択し、[次へ] を選択します。](./media/content-library/new-content-library-step3.png)

1. コンテンツ ライブラリの設定を確認し、 **[完了]** を選択します。

   ![設定を確認し、[完了] を選択します。](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>ISO イメージをコンテンツ ライブラリにアップロードする

コンテンツ ライブラリが作成されたので、ISO イメージを追加して、プライベート クラウド クラスターに VM をデプロイできます。 

1. vSphere クライアントから、 **[メニュー] > [コンテンツ ライブラリ]** を選択します。

1. 新しい ISO に使用するコンテンツ ライブラリを右クリックし、 **[Import Item] (インポート項目)** を選択します。

1. 次のいずれかを実行してソースのライブラリ項目をインポートし、 **[インポート]** を選択します。
   1. [URL] を選択し、ISO をダウンロードするための URL を指定します。

   1. **[ローカル ファイル]** を選択して、ローカル システムからアップロードします。

   > [!TIP]
   > 必要に応じて、宛先のカスタム項目名とメモを定義できます。

1. ライブラリを開き、 **[Other Types] (他の種類)** タブを選択して、ISO が正常にアップロードされたことを確認します。


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>プライベート クラウド クラスターに VM をデプロイする

1. vSphere クライアントから、 **[メニュー] > [ホストとクラスター]** を選択します。

1. 左側のパネルで、ツリーを展開してクラスターを選択します。

1. **[アクション] > [新しい仮想マシン]** を選択します。

1. ウィザードを実行して、必要な設定を変更します。

1. **[New CD/DVD Drive] (新しい CD または DVD ドライブ) > [クライアント デバイス] > [Content Library ISO File] (コンテンツ ライブラリ ISO ファイル)** を選択します。

1. 前のセクションでアップロードした ISO を選択してから、 **[OK]** を選択します。

1. **[接続]** チェック ボックスをオンにして、ISO が電源オン時にマウントされるようにします。

1. **[新しいネットワーク] > [Select dropdown] (ドロップダウンの選択) > [参照]** を選択します。

1. **[logical switch (segment)] (論理スイッチ (セグメント))** を選択し、 **[OK]** を選択します。

1. その他のハードウェア設定を変更し、 **[次へ]** を選択します。

1. 設定を確認し、 **[完了]** を選択します。


## <a name="next-steps"></a>次のステップ

Hybrid Cloud Extension (HCX) を使用して VM ワークロードをプライベート クラウドに移行する予定がある場合は、「[Azure VMware Solution 用の HCX をインストールする](hybrid-cloud-extension-installation.md)」の手順に従います。

<!-- LINKS - external-->

<!-- LINKS - internal -->
