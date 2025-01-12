## tomcat https ssl 설정

### [tomcat server.xml에 keystore 설정 방법]
```xml
<Connector port="8443" maxHttpHeaderSize="8192"
    maxThreads="150" enableLookups="false" acceptCount="100"
    connectionTimeout="20000" disableUploadTimeout="true"
    protocol="org.apache.coyote.http11.Http11NioProtocol"
    SSLEnabled="true" scheme="https" secure="true" clientAuth="false" sslProtocol="TLS"
    keystoreFile="/usr/local/tomcat/conf/ssl/Privacycenter_Keystore.jks"
    keystorePass="privacy1234" />
```


### [keystore 생성 방법]
shpc_keystore
```bash
keytool -genkey -alias Privacycenter_Keystore -keyalg RSA -keypass privacy1234 -storepass privacy1234 -keystore Privacycenter_Keystore.jks
```
