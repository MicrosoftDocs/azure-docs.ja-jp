## <a name="sample-scenario"></a>サンプル シナリオ
NSG の管理方法をわかりやすく説明するために、この記事では次のシナリオを使用します。

![VNet のシナリオ](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

このシナリオでは、以下の説明のように、 **TestVNet** 仮想ネットワークの各サブネットに対して NSG を作成します。 

* **NSG-FrontEnd**。 以下の 2 つの規則を含むフロントエンド NSG が *FrontEnd* サブネットに適用されます。    
  * **rdp-rule**。 この規則は *FrontEnd* サブネットに対する RDP トラフィックを許可します。
  * **web-rule**。 この規則は *FrontEnd* サブネットに対する HTTP トラフィックを許可します。
* **NSG-BackEnd**。 以下の 2 つの規則を含むバックエンド NSG が *BackEnd* サブネットに適用されます。    
  * **sql-rule**。 この規則は、 *FrontEnd* サブネットからの SQL トラフィックのみを許可します。
  * **web-rule**。 この規則は、 *BackEnd* サブネットからのすべてのインターネット経由トラフィックを拒否します。

これらの規則の組み合わせによって、DMZ のようなシナリオが作成されます。このシナリオではバックエンドのサブネットはフロントエンドのサブネットから SQL トラフィックの受信トラフィックのみを受信でき、インターネットへはアクセスできません。一方、フロントエンドのサブネットはインターネットと通信でき、受信 HTTP 要求のみを受信します。

上記のシナリオをデプロイするには、 [このリンク](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)に従って、 **[Azure へのデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。 下記のサンプル手順では、テンプレートを使用して、 **RG NSG**というリソース グループ名をデプロイしています。 



<!--HONumber=Nov16_HO3-->


