---
title: Azure Portal での Azure Media Services アカウントの作成 | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Azure Media Services アカウントを作成する手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: ddc1c7f2dd207cba18a8c080c8b14cc53c149a39
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804177"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Azure portal を使用して Media Services アカウントを作成する

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

Azure Portal には、Azure Media Services (AMS) アカウントをすばやく作成する方法が用意されています。 アカウントを使用して Media Services にアクセスすると、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、およびストリーミングすることができます。 Media Services アカウントを作成するときは、同時に関連付けられたストレージ アカウントも作成します (または既存のストレージ アカウントを使用します)。 メディア サービス アカウントを削除しても、関連付けられたストレージ アカウントにある BLOB は削除されません。

プライマリ ストレージ アカウントとして、汎用 v1 または汎用 v2 を使用できます。 現在、Azure portal では v1 のみを選択できますが、API または PowerShell を使用してアカウントを作成するときに v2 を追加できます。 ストレージの種類の詳細については、「[Azure ストレージ アカウントについて](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)」を参照してください。

Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

この記事では、Azure portal を使用して Media Services アカウントを作成する方法について説明します。

> [!NOTE]
> 異なるリージョンにおける Azure Media Services 機能の可用性については、[データ センター全体における AMS 機能の可用性](scenarios-and-availability.md#availability)に関するセクションを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 

## <a name="create-an-ams-account"></a>AMS アカウントの作成

このセクションでは、AMS アカウントを作成する方法について説明します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[+新規]** > **[Web + モバイル]** > **[Media Services]** の順にクリックします。
   
    ![Media Services Create](./media/media-services-create-account/media-services-new1.png)
3. **[CREATE MEDIA SERVICES ACCOUNT (Media Services アカウントの作成)]** に必要な値を入力します。
   
    ![Media Services Create](./media/media-services-create-account/media-services-new3.png)
   
   1. **[アカウント名]** に新しい AMS アカウントの名前を入力します。 Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。
   2. [サブスクリプション] ボックスで、アクセス権のある別の Azure サブスクリプションを選択します。
   3. **[リソース グループ]** ボックスで、新規または既存のリソースを選択します。  リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../../azure-resource-manager/resource-group-overview.md#resource-groups)を参照してください。
   4. **[場所]** ボックスで、この Media Services アカウントのメディアとメタデータのレコードを保存するリージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。 ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。 
   5. **[ストレージ アカウント]** ボックスで、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、ストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Media Services アカウントと同じです。
      
       ストレージの詳細については、 [こちら](../../storage/common/storage-introduction.md)を参照してください。
   6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、アカウントのデプロイの進行状況を確認します。
4. フォームの下部にある **[作成]** をクリックします。
   
    アカウントが正常に作成されると、概要ページが読み込まれます。 ストリーミング エンドポイントのテーブルで、アカウントには既定のストリーミング エンドポイントが**停止**状態で示されます。 

    >[!NOTE]
    >AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 
   
## <a name="to-manage-your-ams-account"></a>AMS アカウントを管理するには

AMS アカウントの管理 (たとえば、プログラムによる AMS API への接続、ビデオのアップロード、資産のエンコード、コンテンツ保護の構成、ジョブの進行状況の監視など) を行うには、ポータルの左側にある **[設定]** を選択します。 **[設定]** から、使用可能ないずれかのブレード (たとえば、**[API アクセス]**、**[資産]**、**[ジョブ]**、**[コンテンツ保護]**) に移動します。


## <a name="next-steps"></a>次の手順

これで、ファイルを AMS アカウントにアップロードできるようになりました。 詳細については、 [ファイルのアップロード](media-services-portal-upload-files.md)に関するページを参照してください。

プログラムで AMS API にアクセスする場合は、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

