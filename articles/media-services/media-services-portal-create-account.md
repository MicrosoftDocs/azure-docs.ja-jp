---
title: "Azure Portal での Azure Media Services アカウントの作成 | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal で Azure Media Services アカウントを作成する手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b190ad54147a4d548806047b2257069cac4475cd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Azure Portal を使用した Azure Media Services アカウントの作成
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

Azure Portal には、Azure Media Services (AMS) アカウントをすばやく作成する方法が用意されています。 アカウントを使用して Media Services にアクセスすると、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、およびストリーミングすることができます。 Media Services アカウントを作成するときは、同時に Media Services アカウントと同じリージョンにストレージ アカウントも作成して関連付けます (または既存のストレージ アカウントを使用します)。

この記事では、いくつかの一般的な概念について説明し、Azure Portal で Media Services アカウントを作成する方法を示します。

## <a name="concepts"></a>概念
Media Services にアクセスするには、関連付けられた次の 2 つのアカウントが必要です。

* Media Services アカウント。 アカウントを使用して、Azure で利用可能なクラウド ベースの Media Services にアクセスできます。 Media Services アカウントには実際のメディア コンテンツは保存されません。 代わりに、メディア コンテンツに関するメタデータおよびメディア処理ジョブがアカウントに保存されます。 アカウントを作成するときに、利用可能な Media Services リージョンを選択します。 選択したリージョンに、アカウントのメタデータ レコードを保存するデータ センターが配置されます。
  
* Azure ストレージ アカウント。 ストレージ アカウントは、Media Services アカウントと同じリージョンに配置する必要があります。 Media Services アカウントを作成するときに、同じリージョンにある既存のストレージ アカウントを選択することも、同じリージョンに新しいストレージ アカウントを作成することもできます。 メディア サービス アカウントを削除しても、関連付けられたストレージ アカウントにある BLOB は削除されません。

> [!NOTE]
> 異なるリージョンにおける Azure Media Services 機能の可用性については、[データ センター全体における AMS 機能の可用性](scenarios-and-availability.md#availability)に関するセクションを参照してください。

## <a name="create-an-ams-account"></a>AMS アカウントの作成
このセクションでは、AMS アカウントを作成する方法について説明します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. **[+新規]** > **[Web + モバイル]** > **[Media Services]** の順にクリックします。
   
    ![Media Services Create](./media/media-services-create-account/media-services-new1.png)
3. **[CREATE MEDIA SERVICES ACCOUNT (Media Services アカウントの作成)]** に必要な値を入力します。
   
    ![Media Services Create](./media/media-services-create-account/media-services-new3.png)
   
   1. **[アカウント名]** に新しい AMS アカウントの名前を入力します。 Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。
   2. [サブスクリプション] ボックスで、アクセス権のある別の Azure サブスクリプションを選択します。
   3. **[リソース グループ]**ボックスで、新規または既存のリソースを選択します。  リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../azure-resource-manager/resource-group-overview.md#resource-groups)を参照してください。
   4. **[場所]** ボックスで、この Media Services アカウントのメディアとメタデータのレコードを保存するリージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。 ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。 
   5. **[ストレージ アカウント]**ボックスで、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、ストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Media Services アカウントと同じです。
      
       ストレージの詳細については、 [こちら](../storage/common/storage-introduction.md)を参照してください。
   6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、アカウントのデプロイの進行状況を確認します。
4. フォームの下部にある **[作成]** をクリックします。
   
    アカウントが正常に作成されると、概要ページが読み込まれます。 ストリーミング エンドポイントのテーブルで、アカウントには既定のストリーミング エンドポイントが**停止**状態で示されます。 

    >[!NOTE]
    >AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 
   
## <a name="to-manage-your-ams-account"></a>AMS アカウントを管理するには

AMS アカウントの管理 (たとえば、プログラムによる AMS API への接続、ビデオのアップロード、資産のエンコード、コンテンツ保護の構成、ジョブの進行状況の監視など) を行うには、ポータルの左側にある **[設定]** を選択します。 **[設定]** から、使用可能ないずれかのブレード (たとえば、**[API アクセス]**、**[資産]**、**[ジョブ]**、**[コンテンツ保護]**) に移動します。


## <a name="next-steps"></a>次のステップ

これで、ファイルを AMS アカウントにアップロードできるようになりました。 詳細については、 [ファイルのアップロード](media-services-portal-upload-files.md)に関するページを参照してください。

プログラムで AMS API にアクセスする場合は、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


