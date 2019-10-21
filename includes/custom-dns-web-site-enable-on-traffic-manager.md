---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059732"
---
ドメイン名のレコードが反映されると、カスタム ドメイン名を使用して対象の Azure App Services の Web アプリにアクセスできることをブラウザーで確認できます。

> [!NOTE]
> CNAME が DNS に反映されるまで多少の時間がかかります。 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> などのサービスを使用して、CNAME を利用できるかどうかを確認できます。
> 
> 

カスタム ドメイン名がトラフィック マネージャーにルーティングされているため、Web アプリがトラフィック マネージャーのエンドポイントとしてまだ追加されていない場合は、名前を適切に解決するためにエンドポイントとして追加する必要があります。 これにより、トラフィック マネージャーによって対象の Web アプリにルーティングされます。 トラフィック マネージャーのプロファイルに Web アプリをエンドポイントとして追加する方法については、「 [エンドポイントの追加と削除](../articles/traffic-manager/traffic-manager-endpoints.md) 」を参照してください。

> [!NOTE]
> エンドポイントを追加する際に Web アプリが表示されない場合は、Web サイトが **Standard** App Service プラン モードに構成されていることを確認します。 Web アプリをトラフィック マネージャーで操作するには、 **Standard** モードを使用する必要があります。
> 
> 

1. ブラウザーで、[Azure Portal](https://portal.azure.com) を開きます。
2. **[Web Apps]** タブで、Web アプリの名前をクリックし、 **[設定]** 、 **[カスタム ドメイン]** の順に選択します。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. **[カスタム ドメイン]** ブレードで **[ホスト名の追加]** をクリックします。
4. **[Hostname]\(ホスト名\)** テキスト ボックスを使用して、この Web アプリに関連付けるカスタム ドメイン名を入力します。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. **[Validate (検証)]** をクリックしてドメイン名の構成を保存します。
6. **[検証]** をクリックすると、ドメインの検証ワークフローが開始されます。 このワークフローは、ドメインの所有権に加えて、ホスト名を利用できるかどうかを確認して、結果 (成功またはエラー) を報告します。エラーの場合は、エラーの詳細とエラーの解決に役立つガイダンスが表示されます。    
7. 検証が成功すると、 **[Add hostname (ホスト名の追加)]** ボタンがアクティブになり、ホスト名を割り当てることができます。 次に、ブラウザーでカスタム ドメイン名に移動します。 実行中のアプリがカスタム ドメイン名を使用していることを確認できます。 
   
   構成が完了すると、カスタム ドメイン名が Web アプリの **[ドメイン名]** セクションに表示されます。

この時点でブラウザーに Traffic Manager ドメイン名を入力し、対象の Web アプリに正常にアクセスできることを確認できます。

