---
title: Azure Lab Services のテンプレート VM で入れ子になった仮想化を有効にする | Microsoft Docs
description: この記事では、Azure Lab Services のテンプレート マシンで入れ子になった仮想化を設定する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251476"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする

現在、Azure Lab Services では、ラボ内に 1 つのテンプレート仮想マシンを設定し、各ユーザーが 1 つのコピーを使用できるようにすることが可能です。 ネットワーク、セキュリティ、または IT のクラスを教える教師であれば、必要に応じて、複数の仮想マシンがネットワークを介して相互に通信できる環境を各学生に用意します。

入れ子になった仮想化を使用すると、ラボのテンプレート仮想マシン内にマルチ VM 環境を作成できます。 テンプレートを公開すると、ラボ内の各ユーザーに、内部に複数の VM が設定された仮想マシンが提供されます。  この記事では、Azure Lab Services のテンプレート マシンで入れ子になった仮想化を設定する方法について説明します。

## <a name="what-is-nested-virtualization"></a>入れ子になった仮想化の概要

入れ子になった仮想化を使用すると、1 つの仮想マシン内に複数の仮想マシンを作成できます。 入れ子になった仮想化は Hyper-V を介して行われ、Windows VM でのみ使用できます。

入れ子になった仮想化の詳細については、以下の記事を参照してください。

- [Azure の入れ子になった仮想化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM で入れ子になった仮想化を有効にする方法](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>考慮事項

入れ子になった仮想化を使用してラボを設定する前に、次の点を考慮する必要があります。

- 新しいラボを作成するときは、仮想マシン サイズとして **[Medium (Nested Virtualization)]\(中 (入れ子になった仮想化)\)** または **[Large (Nested virtualization)]\(大 (入れ子になった仮想化)\)** のサイズを選択します。 これらの仮想マシンのサイズでは、入れ子になった仮想化がサポートされます。
- ホストとクライアントの両方の仮想マシンに優れたパフォーマンスを提供するサイズを選択します。  仮想化を使用する場合、選択するサイズは、1 台のコンピューターだけでなく、ホストおよび同時に実行する Hyper-V コンピューターにも適したものでなければならないことにご注意ください。
- クライアント仮想マシンは、Azure 仮想ネットワーク上の DNS サーバーなどの Azure リソースにアクセスすることはできません。
- ホスト仮想マシンには、クライアント コンピューターがインターネットに接続できるようにするためのセットアップが必要です。
- クライアント仮想マシンは、独立したコンピューターとしてライセンスされます。 Microsoft のオペレーティング システムおよび製品のライセンスの詳細については、[Microsoft のライセンス](https://www.microsoft.com/licensing/default)に関するページを参照してください。 テンプレート マシンを設定する前に、使用している他のソフトウェアのライセンス契約を確認してください。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>テンプレート VM で入れ子になった仮想化を有効にする

この記事では、既にラボ アカウントとラボが作成されていることを前提としています。  新しいラボ アカウントの作成の詳細については、[ラボ アカウントを設定するためのチュートリアル](tutorial-setup-lab-account.md)をご覧ください。 ラボの作成方法の詳細については、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)をご覧ください。

>[!IMPORTANT]
>ラボの作成時、仮想マシン サイズとして **[Large (Nested Virtualization)]\(大 (入れ子になった仮想化)\)** または **[Medium (Nested Virtualization)]\(中 (入れ子になった仮想化)\)** のサイズを選択します。  それ以外の場合、入れ子になった仮想化は機能しません。  

テンプレート マシンに接続するには、[クラスルーム テンプレートの作成と管理](how-to-create-manage-template.md)に関する記事をご覧ください。

入れ子になった仮想化を有効にするには、いくつかのタスクを実行する必要があります。  

- **Hyper-V ロールを有効にします**。 ラボ サービス仮想マシンで Hyper-V 仮想マシンを作成および実行するには、Hyper-V ロールを有効にする必要があります。
- **DHCP を有効にします**。  ラボ サービスの仮想マシンで DHCP ロールを有効にすると、Hyper-V 仮想マシンに自動的に IP アドレスが割り当てられます。
- **Hyper-V VM 用の NAT ネットワークを作成します**。  Hyper-V 仮想マシンからインターネットにアクセスできるように NAT ネットワークを設定します。  Hyper-V 仮想マシンは相互に通信できます。

>[!NOTE]
>NAT ネットワークをラボ サービス VM に作成すると、Hyper-V VM は、インターネットおよび、同じラボ サービス VM 上の他の Hyper-V VM にアクセスできるようになります。  Hyper-V VM は、Azure 仮想ネットワーク上の DNS サーバーなどの Azure リソースにアクセスすることはできません。

上記のタスクを実行するには、スクリプトまたは Windows ツールを使用します。  詳細については、以下のセクションを参照してください。

### <a name="using-script-to-enable-nested-virtualization"></a>入れ子になった仮想化をスクリプトを使用して有効にする

Windows Server 2016 または Windows Server 2019 で、入れ子になった仮想化のセットアップを自動化する方法については、「[スクリプトを使用して Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする](how-to-enable-nested-virtualization-template-vm-using-script.md)」を参照してください。 [Lab Services Hyper-V スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)のページにあるスクリプトを使用して、Hyper-V ロールをインストールします。  このスクリプトでは、Hyper-V 仮想マシンからインターネットにアクセスできるよう、ネットワークのセットアップも行われます。

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>入れ子になった仮想化を Windows のツールを使用して有効にする

Windows Server 2016 または Windows Server 2019 で、Windows のロールと管理ツールを使用して、入れ子になった仮想化をセットアップする方法については、「[Azure Lab Services のテンプレート仮想マシン上で入れ子になった仮想化を手動で有効にする](how-to-enable-nested-virtualization-template-vm-ui.md)」を参照してください。  インストラクションでは、Hyper-V 仮想マシンからインターネットにアクセスできるようにネットワークをセットアップする方法についても取り上げています。
