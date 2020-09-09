---
title: Azure Lab Services ラボ アカウント迅速設定ガイド
description: 管理者はこのガイドに従って、学校内で使用するラボ アカウントをすばやく作成できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c186560b27ebcb543a23785dc5fbc556614f64b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445425"
---
# <a name="lab-account-setup-guide"></a>ラボ アカウント設定ガイド

最初のステップとして、管理者は Azure サブスクリプション内にラボ アカウントを設定する必要があります。 ラボ アカウントはクラスルーム ラボ用のコンテナーであり、設定には数分しかかかりません。

## <a name="understand-your-schools-lab-account-requirements"></a>学校のラボ アカウントの要件を理解する

学校のニーズに基づいてラボ アカウントを構成する方法を理解するには、以下の質問事項を検討する必要があります。

### <a name="do-i-have-access-to-an-azure-subscription"></a>Azure サブスクリプションにアクセスできるか?

ラボ アカウントを作成するには、学校用に構成された Azure サブスクリプションにアクセスする必要があります。 学校は 1 つ以上のサブスクリプションを所有している場合があります。 サブスクリプションを使用して、ラボ アカウントなど、すべてのAzure リソースとサービスの課金とセキュリティを管理します。

### <a name="how-many-lab-accounts-need-to-be-created"></a>ラボ アカウントをいくつ作成する必要があるか?

短時間で始めるには、1 つのラボ アカウントを作成し、後から必要に応じて追加のラボ アカウントを作成します。 たとえば、最終的には部門ごとに 1 つのラボ アカウントを使用するようになるような場合があります。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>ラボアカウントの所有者と共同作成者を誰にするべきか?

通常は、管理者がラボ アカウントの所有者と共同作成者になります。 所有者と共同作成者は、ラボ アカウントに含まれるすべてのラボに適用されるポリシーを管理する責任があります。 ラボ アカウントの作成者が自動的に所有者になります。 所有者と共同作成者を追加できます。通常は、サブスクリプションに関連付けられている Azure Active Directory (Azure AD) テナントから行います。 これは、ラボ アカウントレベルで所有者ロールまたは共同作成者ロールを割り当てることによって、ラボ アカウントを管理するのに役立ちます。

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>誰にラボの作成と管理を許可するか?

管理者と教職員のメンバーに、ラボの作成と管理を任せる場合があります。 これらのユーザーは (通常は、サブスクリプションに関連付けられている Azure AD テナントから)、ラボ アカウント内のラボ作成者ロールに割り当てられます。

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>ラボ間で共有可能なイメージの保存をラボ作成者に許可するか?

共有イメージ ギャラリーは、イメージの保存と共有に使用できるリポジトリです。 同じイメージを必要とするクラスが複数ある場合、ラボ作成者がいったんイメージを作成すれば、ラボ全体でそれを共有できます。 ただし、作業を始めるために、必ずしも共有イメージ ギャラリーが必要というわけではありません。後でいつでも追加できます。

この質問に "はい" と答えた場合は、ラボ アカウントに共有イメージ ギャラリーを作成するかアタッチする必要があります。 "わからない" と答えた場合は、この決定を先送りすることができます。

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Azure Marketplace のどのイメージをクラスルーム ラボで使用するか?

Azure Marketplace には、有効にできる何百ものイメージが用意されており、ラボ作成者はイメージを使用してラボを作成できます。 イメージによっては、ラボに必要なすべてのものが最初から含まれている場合があります。 出発点としてイメージを使用し、後からラボ作成者が追加のアプリケーションやツールなどをインストールしてイメージをカスタマイズできる場合もあります。

どのイメージを使用する必要があるか不明な場合、後からいつでもここに戻ってイメージを有効にすることができます。 また、使用可能なイメージを確認する最善の方法は、最初にラボ アカウントを作成することです。 これにより、アクセスできるようになり、使用可能なイメージとその内容の一覧を確認できるようになります。
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>ラボの仮想マシンで他の Azure リソースまたはオンプレミス リソースにアクセスできる必要があるか?

ラボ アカウントを設定するときに、仮想ネットワークとピアリングするオプションもあります。 これが必要かどうかを判断するには、以下の質問事項を検討します。

- **ライセンス サーバーへのアクセスを提供する必要があるか?**
  
   Azure Marketplace のイメージを使用する予定の場合、オペレーティング システムのライセンスのコストは Lab Services の価格にバンドルされています。 そのため、オペレーティング システム自体のライセンスを提供する必要はありません。 ただし、インストールする追加のソフトウェアやアプリケーションについては、必要に応じてライセンスを提供する必要があります。

- **ラボ VM はファイル共有やデータベースなどの他のオンプレミス リソースにアクセスする必要があるか?**

   オンプレミス リソースへのアクセスを提供するために、通常はサイト間仮想ネットワーク ゲートウェイを使用して、仮想ネットワークを作成します。 仮想ネットワークが構成されていない場合は、追加の時間を費やす必要があります。

- **ラボ VM は仮想ネットワーク内にある他の Azure リソースにアクセスする必要があるか?**

   仮想ネットワーク内のセキュリティ保護 "*されていない*" Azure リソースにアクセスする必要がある場合、ピアリングを行わずにパブリック インターネット経由でこれらのリソースにアクセスできます。

1 つ以上の質問に "はい" と答えた場合、ラボ アカウントを仮想ネットワークにピアリングする必要があります。 "わからない" と答えた場合は、この決定を先送りすることができます。 ラボ アカウントを作成した後で、いつでも仮想ネットワークのピアリングを選択できます。

## <a name="set-up-your-lab-account"></a>ラボ アカウントの設定

ラボ アカウントの要件を理解したら、設定を開始できます。

1. **ラボ アカウントを作成します。** 手順については、[ラボ アカウントの作成](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account)に関するチュートリアルを参照してください。

   ラボ アカウントを作成するときは、関連する Azure リソースについて理解しておくと役に立つ場合があります。 詳細については、次の記事を参照してください。

   - [サブスクリプション](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [リソース グループ](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [ラボ アカウント](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [クラスルーム ラボ](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [リージョンと場所の選択](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [リソースの名前付けのガイダンス](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **ユーザーをラボの作成者ロールに追加します。** 手順については、[ラボの作成者ロールへのユーザーの追加](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)に関する記事を参照してください。

   また、ラボ アカウントとラボを管理するユーザーに割り当てることができる各種ロールの詳細については、[ID の管理に関するガイド](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)を参照してください。

3. **ピア仮想ネットワークに接続します。** 手順については、[ラボのネットワークとピア仮想ネットワークの接続](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)に関する記事をご覧ください。

   [ラボ VM のアドレス範囲を構成する](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)手順も参照することが必要な場合があります。

4. **イメージを有効にして確認します。** 手順については、[ラボ作成者向けの Azure Marketplace イメージの有効化](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)に関する記事をご覧ください。

   Azure Marketplace の各イメージの内容を確認するには、イメージ名を選択します。 たとえば、次のスクリーンショットは Ubuntu Data Science VM イメージの詳細を示したものです。

   ![Azure Marketplace イメージの確認のスクリーンショット](./media/setup-guide/review-marketplace-images.png)

   ラボ アカウントに共有イメージ ギャラリーをアタッチしていて、カスタム イメージをラボ クリエイターが共有できるようにする場合は、次のスクリーンショットに示すような手順を完了します。

   ![共有イメージ ギャラリーでのカスタム イメージの有効化のスクリーンショット](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>次のステップ

- [ラボ アカウントの管理](how-to-manage-lab-accounts.md)

- [クラスルーム ラボ設定ガイド](setup-guide.md)
