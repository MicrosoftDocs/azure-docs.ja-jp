---
title: "Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する"
description: "Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する方法"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8cd52e9561f2ad92664c01959df8919aa56e8738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Microsoft Azure Storage Explorer (プレビュー) は、Windows、macOS、Linux で Azure Stack Storage のデータを簡単に操作できるスタンドアロン アプリです。 Azure Stack Storage からデータを移動するために利用できるツールは、いくつかあります。 詳細については、「[Data transfer tools for Azure Stack storage](azure-stack-storage-transfer.md)」 (Azure Stack Storage のデータ転送ツール) を参照してください。

この記事では、Microsoft Azure Storage Explorer を使用して Azure Stack Storage アカウントに接続する方法を学習します。 

Microsoft Azure Storage Explorer をまだインストールしていない場合は、[ダウンロード](http://www.storageexplorer.com/)し、インストールしてください。

Azure Stack サブスクリプションに接続したら、[Microsoft Azure Storage Explorer の記事](../../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用し、Azure Stack データを操作できます。 

## <a name="prepare-an-azure-stack-subscription"></a>Azure Stack サブスクリプションを準備する

Microsoft Azure Storage Explorer から Azure Stack サブスクリプションにアクセスするには、Azure Stack ホスト コンピューターのデスクトップへのアクセスまたは VPN 接続が必要です。 Azure Stack への VPN 接続を設定する方法の詳細については、[VPN を使用した Azure Stack への接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)に関するページを参照してください。

Azure Stack Development Kit 用に、Azure Stack の証明機関のルート証明書をエクスポートする必要があります。

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Azure Stack の証明書をエクスポートしインポートするには

1. Azure Stack に VPN 接続された、Azure Stack のホスト コンピューターまたはローカル コンピューターで `mmc.exe` を開きます。 

2. **[ファイル]** で **[スナップインの追加と削除]** を選択し、**[証明書]** を追加して、**ローカル コンピューター**の**コンピューター アカウント**を管理します。



3. **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** の直下で **AzureStackSelfSignedRootCert** を探します。

    ![Azure Stack のルート証明書を mmc.exe で読み込む][25]

4. 証明書を右クリックし、**[すべてのタスク]** > **[エクスポート]** の順に選択したら、表示される手順に従って **Base-64 encoded X.509 (.CER)** で証明書をエクスポートします。  

    エクスポートした証明書は、次の手順で使用します。
5. Microsoft Azure Storage Explorer (プレビュー) を開始し、**[Connect to Azure Storage]\(Azure Storage に接続\)** ダイアログ ボックスが表示されたらそれをキャンセルします。

6. **[編集]** メニューで、**[SSL 証明書]**、**[証明書のインポート]** の順にクリックします。 ファイル ピッカー ダイアログ ボックスを使用して、前の手順でエクスポートした証明書を検索し、開きます。

    インポート後は、ストレージ エクスプローラーの再起動を求めるメッセージが表示されます。

    ![ストレージ エクスプローラー (プレビュー) に証明書をインポートする][27]

これで Microsoft Azure Storage Explorer を Azure Stack サブスクリプションに接続する準備が完了です。

### <a name="to-connect-an-azure-stack-subscription"></a>Azure Stack サブスクリプションに接続するには


1. ストレージ エクスプローラー (プレビュー) が再起動したら、**[編集]** メニューを選択し、**[Target Azure Stack (Azure Stack を対象にする)]** が選択されていることを確認します。 選択されていない場合は、選択してからストレージ エクスプローラーを再起動し、この変更を反映させます。 この構成は、Azure Stack 環境との互換性を確保するために必要です。

    ![[Target Azure Stack (Azure Stack を対象にする)] が選択されていることを確認する][28]

7. 左側のウィンドウで、**[アカウントの管理]** を選択します。  
    サインインしている Microsoft アカウントがすべて表示されます。

8. Azure Stack アカウントに接続するには、**[アカウントの追加]** を選択します。

    ![Azure Stack アカウントを追加する][29]

9. **[Connect to Azure Storage]\(Azure Storage に接続\)** ダイアログ ボックスの **[Azure environment]\(Azure 環境\)** で **[Use Azure Stack Environment]\(Azure Stack 環境を使用\)** を選択して、**[次へ]** をクリックします。

10. 少なくとも 1 つのアクティブな Azure Stack サブスクリプションと関連付けられた Azure Stack アカウントでサインインするために、**[Sign in to Azure Stack Environment]\(Azure Stack 環境にサインイン\)** ダイアログ ボックスに情報を入力します。  

    各フィールドの詳細は次のとおりです。

    * **[環境名]**: このフィールドはユーザーがカスタマイズできます。
    * **[ARM resource endpoint (ARM リソース エンドポイント)]**: Azure Resource Manager リソース エンドポイントの例は次のとおりです。

        * クラウド管理者の場合:<br> https://adminmanagement.local.azurestack.external   
        * テナントの場合:<br> https://management.local.azurestack.external
 
    * **[テナント ID]**: 省略可能です。 ディレクトリを指定する必要がある場合にのみ値を入力します。

12. Azure Stack アカウントでのサインインに成功すると、左側のウィンドウに、そのアカウントに関連付けられている Azure Stack サブスクリプションが表示されます。 操作する Azure Stack サブスクリプションを選択してから、**[適用]** を選択します  (**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure Stack サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。

    ![カスタム クラウド環境のダイアログ ボックスに情報を入力した後、Azure Stack サブスクリプションを選択する][30]  
    左側のウィンドウに、選択した Azure Stack サブスクリプションに関連付けられているストレージ アカウントが表示されます。

    ![Azure Stack サブスクリプション アカウントを含むストレージ アカウントの一覧][31]

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Storage Explorer (プレビュー) の概要](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack Storage: 違いと考慮事項](azure-stack-acs-differences.md)


* Azure Storage の詳細については、「[Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)」を参照してください。

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
