---
title: "Microsoft Azure StorSimple データ マネージャーの概要 | Microsoft Docs"
description: "StorSimple データ マネージャー サービスの概要を示します (プライベート プレビュー)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73

---

# <a name="storsimple-data-manager-overview-private-preview"></a>StorSimple データ マネージャーの概要 (プライベート プレビュー)

## <a name="overview"></a>Overview

Microsoft Azure StorSimple は、ファイル共有でよく使用される複雑な非構造データに対応できるハイブリッド クラウド ストレージ ソリューションです。 StorSimple では、クラウド ストレージがオンプレミス ソリューションの拡張機能として使用され、オンプレミス ストレージとクラウド ストレージをまたがってデータが自動的に階層化されます。 ローカル スナップショットおよびクラウド スナップショットを使用した統合型データ保護により、ストレージ インフラストラクチャを拡大する必要がなくなります。 アーカイブと障害復旧も、オフサイトの場所として機能するクラウドでシームレスに行うことができます。

このドキュメントで紹介するデータ変換サービスにより、クラウド内の StorSimple データにシームレスにアクセスできます。 このサービスの API は、StorSimple からデータを抽出し、そのデータを、すぐに使用できる形式で他の Azure サービスに提供します。 このプレビューでサポートされている形式は、Azure BLOB と Azure Media Services 資産です。 この変換により、Azure Media Services、Azure HDInsight、Azure Machine Learning、Azure Search などのサービスを容易に接続して、StorSimple 8000 シリーズのオンプレミス デバイスでデータを操作できます。

その概要を次のブロック図に示します。

![概要図](./media//storsimple-data-manager-overview/high-level-diagram.png)

このドキュメントでは、このサービスのプライベート プレビューにサインアップする方法について説明します。 また、このサービスを使用して、StorSimple データおよびその他の Azure サービスをクラウドで使用するアプリケーションを作成する方法についても説明します。

## <a name="sign-up-for-data-manager-preview"></a>データ マネージャー プレビューにサインアップする
データ マネージャー サービスにサインアップする前に、次の前提条件を確認します。

### <a name="prerequisites"></a>前提条件

この演習では、以下のことを前提としています
* 有効な Azure サブスクリプション。
* 登録済み StorSimple 8000 シリーズ デバイスへのアクセス
* StorSimple 8000 シリーズ デバイスに関連付けられているすべてのキー。

### <a name="sign-up"></a>サインアップ

StorSimple データ マネージャーはプライベート プレビュー段階にあります。 このサービスのプライベート プレビューにサインアップするには、次の手順を実行します。

1.  StorSimple データ マネージャー拡張機能を備えた Azure Portal ([https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)) にログインします。 ログインには Azure 資格情報を使用してください。

2.  **+** アイコンをクリックして、サービスを作成します。 **[ストレージ]** をクリックし、表示されたブレードで **[すべて表示]** をクリックします。

    ![StorSimple データ マネージャー アイコンを検索](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. StorSimple データ マネージャー アイコンが表示されます。

    ![StorSimple データ マネージャー アイコンを選択](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. StorSimple データ マネージャー アイコンをクリックし、**[作成]** をクリックします。 プライベート プレビューに対して有効にするサブスクリプションを選択し、**[Sign me up (サインアップ)]** をクリックします。

    ![サインアップ](./media/storsimple-data-manager-overview/sign-me-up.png)

5. これにより、利用開始要求が送信されます。 ユーザーができるだけ早く開始できるように要求が処理されます。 サブスクリプションが有効になると、StorSimple データ マネージャー サービスを作成できます。

6. StorSimple データ マネージャー サービスに簡単にアクセスするには、星のアイコンをクリックして、お気に入りにピン留めします。

    ![StorSimple データ マネージャーにアクセス](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。


<!--HONumber=Nov16_HO4-->


