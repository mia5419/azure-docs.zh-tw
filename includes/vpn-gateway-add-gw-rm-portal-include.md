1. 在入口網站中，按一下左側的 **+** 並在搜尋中輸入「虛擬網路閘道」。 在搜尋傳回的結果中找出**虛擬網路閘道**，然後按一下該項目。 在 [虛擬網路閘道] 刀鋒視窗上，按一下刀鋒視窗底部的 [建立]。 這會開啟 [建立虛擬網路閘道] 刀鋒視窗。
2. 在 [建立虛擬網路閘道] 刀鋒視窗上，填入您虛擬網路閘道的值。

    ![建立虛擬網路閘道刀鋒視窗欄位](./media/vpn-gateway-add-gw-rm-portal-include/createvng.png "建立虛擬網路閘道刀鋒視窗欄位")
3. **名稱**：為您的閘道命名。 這與為閘道子網路命名不同。 這是您要建立之閘道物件的名稱。
4. 閘道類型︰選取 [VPN]。 VPN 閘道使用 **VPN** 虛擬網路閘道類型。 
5. **VPN 類型**：選取針對您的組態指定的 VPN 類型。 大部分組態需要路由式 VPN 類型。
6. **SKU**︰從下拉式清單中選取閘道 SKU。 下拉式清單中所列的 SKU 取決於您選取的 VPN 類型。
7. **位置**：調整 [位置] 欄位以指向您的虛擬網路所在的位置。 如果位置並未指向您的虛擬網路所在的區域，則此虛擬網路不會出現在 [選擇虛擬網路] 下拉式清單中。
8. 選擇您要新增此閘道的虛擬網路。 按一下 [虛擬網路] 以開啟 [選擇擇虛擬網路] 刀鋒視窗。 選取 VNet。 如果您沒看到您的 VNet，請確定 [位置] 欄位是指向您的虛擬網路所在的區域。
9. 選擇公用 IP 位址。 按一下 [公用 IP 位址] 以開啟 [選擇公用 IP 位址] 刀鋒視窗。 按一下 [+新建] 以開啟 [建立公用 IP 位址] 刀鋒視窗。 輸入公用 IP 位址的名稱。 此刀鋒視窗會建立將動態獲派公用 IP 位址的公用 IP 位址物件。<br>按一下 [確定] 將變更儲存至此刀鋒視窗。
10. **訂用帳戶**：請確認已選取正確的訂用帳戶。
11. **資源群組**：此設定取決於您選取的虛擬網路。 
12. 指定上述設定之後，請勿調整 [位置]。
13. 確認設定。 如果您希望閘道顯示在儀表板上，可以選取刀鋒視窗底部的 [釘選到儀表板]  。
14. 按一下 [建立]  即可開始建立閘道。 設定會經過驗證，您將會在儀表板上看到 [部署虛擬網路閘道] 圖格。 建立閘道可能需要長達 45 分鐘。 您可能需要重新整理入口網站頁面，才能看到完成的狀態。
    
    ![部署虛擬網路閘道](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "部署虛擬網路閘道")
15. 建立閘道之後，您可以查看入口網站中的虛擬網路，來檢視已指派給閘道的 IP 位址。 閘道將會顯示為已連線的裝置。 您可以按一下連接的裝置 (虛擬網路閘道) 以檢視詳細資訊。



<!--HONumber=Jan17_HO3-->


