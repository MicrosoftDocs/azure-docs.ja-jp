---
title: Azure DevTest Labs でのネットワークの分離
description: Azure DevTest Labs でのネットワークの分離について説明します。
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352934"
---
# <a name="network-isolation-in-devtest-labs"></a>DevTest Labs でのネットワークの分離

[Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)は、パブリック インターネットから Azure リソースを分離するセキュリティ境界として機能します。 オンプレミスのリソースに安全に接続できるように、Azure 仮想ネットワークをオンプレミス ネットワークに参加させることもできます。 DevTest Labs では、ラボ リソースが組織のネットワーク ポリシーに従うようにするために、[すべてのラボ仮想マシン](devtest-lab-configure-vnet.md)と[環境をネットワーク](connect-environment-lab-virtual-network.md)に分離することを選択できます。 

ラボの所有者は、ラボを完全に分離することもできます。つまり、選択したネットワークに仮想マシンと環境を分離することに加え、サブスクリプションで作成されたラボ ストレージ アカウントとキー コンテナーを分離することもできます。 この記事では、ネットワーク分離ラボを作成する手順について説明します。 

以下の記事も確認してください。

- [DevTest Labs がラボ ストレージ アカウントを使用する方法](encrypt-storage.md)
- [DevTest Labs がキー コンテナーを使用する方法](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> ネットワークの分離は、現在新しいラボの作成でのみサポートされています。

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>ラボの作成時にネットワークの分離を有効にする手順

1. ラボの作成時に、 **[ネットワーク]** タブにアクセスします。
1. ラボによって作成される **既定** のネットワークを選択するか、ドロップダウンから既存のネットワークを選択することができます。 ラボと同じリージョンおよびサブスクリプション内にあるネットワークのみを選択できます。 

    > [!div class="mx-imgBorder"]
    > ![ラボの作成](./media/network-isolation/create-lab.png)
1. サブネットを選択します。

    > [!div class="mx-imgBorder"]
    > ![サブネットを選択する](./media/network-isolation/create-lab-subnet.png)
1. ラボ リソース (ラボ ストレージ アカウントとキー コンテナー) を既定のネットワークに分離する場合、それ以上の操作は必要なく、ラボは今後のリソースの分離を処理します。
 
    > [!div class="mx-imgBorder"]
    > ![ネットワークの分離](./media/network-isolation/isolate-lab-resources.png)
1. ラボ リソース (ラボ ストレージ アカウントとキー コンテナー) を、選択した既存のネットワークに分離する場合、ラボを分離モードで引き続き機能させるために、ラボの作成後に次の手順を完了する必要があります。 
 
    > [!div class="mx-imgBorder"]
    > ![リソースを分離する](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > ラボの所有者は、ラボでリソースを構成または作成する前に、これらの手順を完了する必要があります。

### <a name="steps-to-follow-post-lab-creation"></a>ラボの作成後に従う手順

1. ラボのホーム ページで、 **[概要]** ページの **リソース グループ** を選択します。 そのラボが含まれているリソース グループの **[リソースグループ]** ページが表示されます。 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso ラボ](./media/network-isolation/contoso-lab.png)
1. ラボの Azure ストレージ アカウントを選択します。 ラボ ストレージ アカウントの名前付け規則は、a<*labNameWithoutInvalidCharacters*> *< 4 桁の数字*> です。 たとえば、ラボ名が contosolab である場合、ストレージ アカウント名は acontosolab1234 になります。
 
   > [!div class="mx-imgBorder"]
   > ![Contoso テスト](./media/network-isolation/contoso-test.png)
1. ストレージ アカウントで、[ファイアウォールと仮想ネットワーク] にアクセスし、[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] のチェックボックスをオンにします。 [DevTest Labs は信頼された Microsoft サービスである](../storage/common/storage-network-security.md#trusted-microsoft-services)ため、このオプションを使用すると、ラボをネットワーク分離モードで正常に動作させることができます。 

   > [!div class="mx-imgBorder"]
   > ![Contoso ラボ ファイアウォール](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 次に、 **[既存の仮想ネットワークを追加]** をクリックし、ラボの作成時に選択した仮想ネットワークとサブネットを選択し、 **[有効化]** をクリックします。 

   > [!div class="mx-imgBorder"]
   > ![Contoso my vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  選択した仮想ネットワークのサービス エンドポイントが正常に有効になったら、 **[追加]** クリックします。 

   > [!div class="mx-imgBorder"]
   > ![追加](./media/network-isolation/contoso-firewall-add.png)
 
これにより、Azure Storage は追加された仮想ネットワークからの着信接続を許可し、ラボがネットワーク分離モードで正常に動作できるようにします。 

また、これらの手順を自動化して、複数のラボに対してこの設定を構成することもできます。 

[PowerShell と CLI を使用して Azure Storage の既定のネットワーク アクセス ルールを管理する方法の詳細を確認する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>ネットワーク分離モードでラボを使用するときに覚えておくべきこと

### <a name="accessing-labs-storage-account-outside-the-lab"></a>ラボ外のラボのストレージ アカウントへのアクセス 

ネットワーク分離ラボの内部で、ラボのストレージ アカウントに VHD をアップロードしてカスタム イメージを作成するなどの操作を行うために、ラボ所有者は、許可されているエンドポイントからストレージ アカウントへのアクセスを明示的に有効にする必要があります。 そのためには、仮想マシンを作成し、その仮想マシンからラボのストレージ アカウントに安全にアクセスできるようにします。 

[仮想マシンから非公開でストレージ アカウントにアクセスする方法の詳細を確認する](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>ラボからの使用状況データのエクスポート 

ネットワーク分離ラボの内部で、[ラボの個人の使用状況データをエクスポートする](personal-data-delete-export.md)には、ラボの所有者は明示的にストレージ アカウントを指定し、データを格納するためのアカウント内に BLOB を生成する必要があります。 

ストレージ アカウントが指定されていない場合、この操作はネットワーク分離モードで失敗します。これは、顧客が指定するストレージ アカウントがない場合、ラボが使用するラボのストレージ アカウントにアクセスできないためです。 

[指定されたストレージ アカウントでラボの使用状況データをエクスポートする方法の詳細を確認する](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する](devtest-lab-use-arm-and-powershell-for-lab-resources.md)