DNS 區域用來主控特定網域的 DNS 記錄。 若要啟動裝載您的網域，您需要建立 DNS 區域。 在網域的 DNS 區域內會針對特定的網域建立所有 DNS 記錄。 

例如，網域 」 contoso.com 」 可能包含數字的 DNS 記錄，例如 「 mail.contoso.com 」 （適用於郵件伺服器） 和 「 www.contoso.com 」 （適用於網站）。 


## <a name="names"></a>關於 DNS 區域名稱
 
- 區域的名稱必須是唯一內 [資源] 群組中，而且必須已存在區域。 否則，操作會失敗。

- 可重複使用不同的資源群組或不同的 Azure 訂閱中相同的區域名稱。 

- 多個區域共用相同的名稱，每個執行個體指派不同的名稱伺服器位址，並從上層網域委派只有一個執行個體。 如需詳細資訊，請參閱[代理人 Azure dns 網域](../articles/dns/dns-domain-delegation.md)。