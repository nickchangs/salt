安装  
yum -y install salt-api  


設定api.conf 並放入/etc/salt/master.d/api.conf  
external_auth:  
  pam:  
    salt:  
      - .*  
  
rest_cherrypy:
  port: 8838
  host: 0.0.0.0     
  # disable_ssl: True   ## 如果不需要 https，直接使用 http ，可以去掉本行最前面的注释  
  ssl_crt: /etc/ssl/certs/saltcert.pem
  ssl_key: /etc/ssl/certs/salt.pem
  
製作憑證  
openssl genrsa -out /etc/ssl/certs/salt.pem 4096
openssl req -new -x509 -key /etc/ssl/certs/salt.pem -out /etc/ssl/certs/saltcert.pem -days 1826

加入使用者及密碼  
useradd -M salt
passwd salt




產生key  
curl -ki https://127.0.0.1:8838/login -H "Accept: application/json"  -d username='salt'  -d password='salt2018' -d eauth='pam'



查詢minion的狀況  
curl -k https://127.0.0.1:8838/minions/021-dyj-pay-02 \
     -H "Accept: application/x-yaml" \
     -H "X-Auth-Token: a8c74849dd65494095d3b255972ab47268f29b3d"
	 
	 
	 
測試minion的回應   
curl -k https://127.0.0.1:8838/ \
     -H "Accept: application/x-yaml" \
     -H "X-Auth-Token: a8c74849dd65494095d3b255972ab47268f29b3d" \
     -d client='local' \
     -d tgt='*fe*' \
     -d fun='test.ping'
	 
	 
執行cmd.run  
curl -k https://127.0.0.1:8838/ \
        -H "Accept: application/x-yaml" \
        -H "X-Auth-Token: a8c74849dd65494095d3b255972ab47268f29b3d" \
        -d client=local \
        -d tgt='*fe*' \
        -d fun='cmd.run' \
        -d arg="hostname"
		

分析log  
curl -k https://127.0.0.1:8838/ \
        -H "Accept: application/json" \
        -H "X-Auth-Token: a8c74849dd65494095d3b255972ab47268f29b3d" \
        -d client=local \
        -d tgt='*fe*01' \
        -d fun='cmd.script' \
        -d arg='salt://scripts//analyze_nginx_access_logs.sh' \
		-d arg='now_hours'
