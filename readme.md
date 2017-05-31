


               How to make Website secure with https


this tutorial is with tomcat server and certbot formerly known as lets encrypt

1.stop currently running tomcat

2.install certbot from their official site via terminal

$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install certbot 


3. sudo certbot certonly --standalone -d stage1.test.com -d www.stage1.test.com


4. create password

openssl pkcs12 -export -in /etc/letsencrypt/live/test.com/fullchain.pem -inkey privkey.pem -out pkcs.p12 -name teststage


5. cd ~
**generate keystore

 keytool -importkeystore -deststorepass <passsword> -destkeypass <password> -destkeystore keystore.jks -srckeystore pkcs.p12 -srcstoretype PKCS12 -srcstorepass <password> -alias teststage


6. Auto redirect to https

** modify server.xml in tomcat/conf

<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol" URIEncoding="UTF-8"
maxThreads="150" SSLEnabled="true" scheme="https" secure="true" clientAuth="false"
sslProtocol="TLS" keystoreFile="/home/vraiqueue/keystore.jks"
keystorePass="vqtest" keyAlias="vraistage" keyPass="vqtest"/>


7. modify web.xml at the end

<security-constraint>
           <web-resource-collection>
                <web-resource-name>Entire Application</web-resource-name>
                <url-pattern>/*</url-pattern>
           </web-resource-collection>
           <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
           </user-data-constraint>
        </security-constraint>


8. to autorenew certificate

commands: crontab -e

 **add this line

1 0 * * 5 certbot renew --post-hook "systemctl reload tomcat"


9. to check 

command: crontab -l


