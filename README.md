# Hyper-V Replica

設定

	o	仮想マシンのライブ マイグレーションの送受信をこのサーバーに許可する

		認証プロトコル

			o	Credential Security Support Provider (CredSSP) を使用する
			x	Kerberos を使用する

自己署名認証の作り方

	New-SelfSignedCertificate -DnsName "ws2019-1.ec.local" –CertStoreLocation "cert:\LocalMachine\My" -NotAfter "2119-01-01 00:00:00" -NotBefore "2019-01-01 00:00:00" -TestRoot
	New-SelfSignedCertificate -DnsName "ws2019-2.ec.local" –CertStoreLocation "cert:\LocalMachine\My" -NotAfter "2119-01-01 00:00:00" -NotBefore "2019-01-01 00:00:00" -TestRoot

- 参考  
	https://qiita.com/sat0tabe/items/e6b0a2913eb2cbddb4fd
	https://qiita.com/qyen/items/079591984b30edeacd5e
	https://qiita.com/qyen/items/933ec04b253fc2ee37c2
	https://michaelfirsov.wordpress.com/hyper-v-replica-in-windows-server-2016-configuring-certificate-based-authentication-part1/
	https://www.pdconsec.net/blogs/deliberations/end-to-end-hyper-v-replica-with-self-signed-certs


認証の有効期限を無視させる設定を施す

	reg add “HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Replication”        /v DisableCertRevocationCheck /d 1 /t REG_DWORD /f
	reg add “HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\FailoverReplication" /v DisableCertRevocationCheck /d 1 /t REG_DWORD /f

これ重要  
https://blogs.technet.microsoft.com/askcorejp/2017/02/24/hyper-v-replica-planed-failover/

----
フェールオーバー後にレプリケーションの方向を反転する
    本チェック ボックスをオンにした場合は、(6) までの処理が実行されます。

    本項目にチェックを入れた場合、前提条件の確認でレプリカ仮想マシンの状態を確認する処理が行われます。
    WORKGROUP 環境の場合は、レプリカ仮想マシンの状態を確認する処理にて行われる WinRM のリモート接続に Kerberos 認証ではなく NTLM 認証が用いられるため、WinRM の信頼されたホスト (TrustedHosts) にリモート コンピューターを指定する必要があります。

    TrustedHosts への追加は、管理者権限の PowerShell から以下のコマンドにて実施できます。
    > Set-Item WSMan:\localhost\Client\TrustedHosts -Value “<リモート コンピューター名>”
    ※ <リモート コンピューター名> にはプライマリ サーバーからはレプリカ サーバーを指定し、レプリカ サーバーからはプライマリ サーバーを指定します。

    なお、現在の設定の確認は以下のコマンドです。
    > Get-Item WSMan:\localhost\Client\TrustedHosts
----
