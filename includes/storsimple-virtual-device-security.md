<!--v-sharos 10/13/2105 virtual device security-->

StorSimple 仮想デバイスを使用する場合、セキュリティに関する次の考慮事項に留意してください。

* 仮想デバイスは Microsoft Azure サブスクリプションを通じてセキュリティで保護されます。 つまり、仮想デバイスを使用している場合に Azure サブスクリプションが侵害されると、仮想デバイス上に格納されたデータも影響を受けます。
* Azure StorSimple に格納されたデータの暗号化に使われる証明書の公開キーは、Azure クラシック ポータルに対して安全に公開され、秘密キーは StorSimple デバイス上に保持されます。 StorSimple 仮想デバイスでは、公開キーと秘密キーの両方が Azure に格納されます。
* 仮想デバイスは、Microsoft Azure データセンターでホストされます。



<!--HONumber=Nov16_HO3-->


