---
title: Microsoft Azure ベースの VM イメージの Shared Access Signature URI を取得する | Microsoft Docs
description: VM イメージの Shared Access Signature (SAS) URI を取得する方法について説明します。
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
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c21fa3cf819f48dcda46f2d444ed52bc2eb9ae3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113522"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM イメージの Shared Access Signature URI の取得

発行プロセスでは、ご利用の SKU に関連付けられた仮想ハード ディスク (VHD) ごとに URI (Uniform Resource Identifier) を指定する必要があります。 マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。 この記事では、VHD ごとに Shared Access Signature (SAS) URI を生成する方法について説明します。 この URI は、Cloud パートナー ポータルの **[SKU]** タブに入力することになります。 

VHD の SAS URI を生成するときは、次の要件を遵守してください。

- サポートされるのはアンマネージド VHD だけです。
- アクセス許可は `List` と `Read` があれば十分です。 `Write` や `Delete` のアクセス権は付与 "*しないでください*"。
- アクセスするための期間 ("*有効期限*") は、SAS URI の作成時から最低 3 週間必要です。
- UTC 時刻の差を考慮して、開始日は現在の日付の 1 日前に設定します。 たとえば、今日が 2014 年 10 月 6 日の場合は、2014 年 10 月 5 を選択します。

## <a name="generate-the-sas-url"></a>SAS URL の生成

一般に SAS URL は、次のツールを使用した 2 とおりの方法で生成できます。

-   Microsoft Storage Explorer - Windows、macOS、Linux 向けに提供されているグラフィカル ツール
-   Microsoft Azure CLI - Windows 以外の OS や自動化された環境、継続的インテグレーション環境に推奨


### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して SAS URI を生成するには次の手順に従います。

1. [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/) をダウンロードしてインストールします。  Windows と macOS のほか、各種 Linux ディストリビューション向けのバージョンが用意されています。 
2. PowerShell ファイル (`.ps1` ファイル拡張子) を作成し、次のコードをコピーして、ローカルに保存します。

   ``` powershell
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```
    
3. ファイルを編集して次のパラメーター値を指定します。  日付は、UTC 日時形式で指定する必要があります (例: `10-25-2016T00:00:00Z`)。
   - `<account-name>` - 実際の Azure ストレージ アカウント名
   - `<account-key>` - 実際の Azure ストレージ アカウント キー
   - `<vhd-name>` - 実際の VHD 名
   - `<start-date>` - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。 
   - `<expiry-date>` - VHD のアクセス許可の有効期限。  現在の日付から 3 週間以上先の日付を指定します。 
 
   次の例では、(本記事の執筆時点における) 適切なパラメーター値を示します。

   ``` powershell
       az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```
 
4. この PowerShell スクリプトに対する変更を保存します。
5. 管理特権を使用してこのスクリプトを実行し、コンテナー レベル アクセスに使用する "*SAS 接続文字列*" を生成します。  基本的な方法として、次の 2 つを使用できます。
   - コンソールからスクリプトを実行します。  たとえば、Windows でスクリプトを右クリックし、**[管理者として実行]** を選択します。
   - 管理特権を使用して、PowerShell スクリプト エディター ([Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise) など) からスクリプトを実行します。 
     次の図は、このエディター内で SAS 接続文字列が生成されるようすを示しています。 

     ![PowerShell ISE における SAS URI の生成](./media/publishvm_032.png)

6. 結果の SAS 接続文字列をコピーし、テキスト ファイルとして安全な場所に保存します。  この文字列を編集して関連する VHD の場所情報を追加し、最終的な SAS URI を作成します。 
7. Azure portal で、新しく生成された URI に関連付けられている VHD を含んだ Blob Storage に移動します。
8. 次に示すように、**[Blob service エンドポイント]** の URL 値をコピーします。

    ![Azure portal の [Blob service エンドポイント]](./media/publishvm_033.png)

9. 手順 6. で得た SAS 接続文字列でテキスト ファイルを編集します。  次の形式を使用して、完全な SAS URI を作成します。

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    たとえば、VDH の名前が `TestRGVM2.vhd` である場合、最終的な SAS URI は次のようになります。

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

発行する予定の SKU に含まれる各 VHD について、これらの手順を繰り返します。


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Microsoft Azure Storage Explorer で、次の手順を使用して SAS URI を生成します。

1. [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールする](https://azure.microsoft.com/features/storage-explorer/)。
2. エクスプローラーを開き、左側のメニュー バーの **[アカウントの追加]** アイコンをクリックします。  **[Azure Storage へ接続]** ダイアログ ボックスが表示されます。
3. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** をクリックします。  必要な手順を続行して、Azure アカウントにサインインします。
4. 左側の **[エクスプローラー]** ウィンドウで、**[ストレージ アカウント]** に移動して、このノードを展開します。
5. 対象の VHD を右クリックし、コンテキスト メニューから **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。 

    ![Azure Explorer で SAS 項目を取得する](./media/publishvm_034.png)

6. **[Shared Access Signature]** ダイアログが表示されます。 次のフィールドの値を入力します。
   - **[開始時刻]** - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
   - **[有効期限]** - VHD のアクセス許可の有効期限。  現在の日付から 3 週間以上先の日付を指定します。
   - **[アクセス許可]** - `Read` と `List` のアクセス許可を選択します。 

     ![Azure Explorer の SAS ダイアログ](./media/publishvm_035.png)

7. **[作成]** をクリックして、この VHD に関連付けられた SAS URI を作成します。  ダイアログには、この操作の詳細が表示されます。 
8. **[URL]** の値をコピーし、テキスト ファイルとして安全な場所に保存します。 

    ![Azure Explorer での SAS URI の作成](./media/publishvm_036.png)

    ここで生成された SAS URL のアクセス範囲は、コンテナー レベルとなります。  範囲を限定するには、関連付けられている VHD 名を追加する必要があります。

9. テキスト ファイルを編集します。 SAS URL 内の `vhds` 文字列の後に VHD 名を挿入します (先頭のスラッシュを含む)。  最終的な SAS URI は次の形式になります。

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    たとえば、VDH の名前が `TestRGVM2.vhd` である場合、最終的な SAS URI は次のようになります。

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

発行する予定の SKU に含まれる各 VHD について、これらの手順を繰り返します。


## <a name="verify-the-sas-uri"></a>SAS URI の検証

生成された各 SAS URI を確認するには、次のチェックリストを使用してください。  次のことを確認します。
- URI が `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>` 形式になっている。
- URI に実際の VHD イメージのファイル名 (拡張子 ".vhd" を含む) が含まれている。
- URI の中間付近に `sp=rl` が指定されている。 この文字列は、`Read` アクセスと `List` アクセスが指定されていることを示します。
- その後ろに、`sr=c` も表示されている。 この文字列は、コンテナー レベルのアクセスが指定されていることを示します。
- この URI をコピーしてブラウザーに貼り付けて、関連する BLOB をダウンロードします   (この操作は、ダウンロードの完了前に取り消すことができます)。


## <a name="next-steps"></a>次の手順

SAS URI を生成できない場合は、[SAS URL の一般的な問題](./cpp-common-sas-url-issues.md)に関するページを参照してください。  それ以外の場合は、SAS URI を後で使用できるよう安全な場所に保存します。 これは、Cloud パートナー ポータルで [VM プランを発行](./cpp-publish-offer.md)する際に必要となります。
