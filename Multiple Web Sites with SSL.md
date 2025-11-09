## **(ak hi ip multi site with diffrent port with ssh certificate )**

**Editing the Virtual Host Configurations**

- **Edit the configuration file** (e.g., `sitel.conf` or any in `/etc/httpd/conf.d/`):
    
    ```
    vim /etc/httpd/conf.d/sitel.conf
    
    ```
    

---

### **Virtual Host for Port 8080 (SSL)**

- **Add Listen directive for port 8080**:
    
    ```
    Listen 8080 https
    
    ```
    
- **SSL Virtual Host Configuration for Port 8080**:
    
    ```
    <VirtualHost 192.168.1.42:8080>
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/ca.crt
        SSLCertificateKeyFile /etc/pki/tls/private/ca.key
        ServerName ai.local
        DocumentRoot /var/www/html/site1/
        <Directory /var/www/html/site1/>
            DirectoryIndex index.html
        </Directory>
        ServerAlias www.ai.local
    </VirtualHost>
    
    ```
    

---

### **Virtual Host for Port 8443 (SSL)**

- **Edit or create configuration file for second site**:
    
    ```
    vim /etc/httpd/conf.d/site2.conf
    
    ```
    
- **Add Listen directive for port 8443**:
    
    ```
    Listen 8443 https
    
    ```
    
- **SSL Virtual Host Configuration for Port 8443**:
    
    ```
    <VirtualHost 192.168.1.42:8443>
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/ca.crt
        SSLCertificateKeyFile /etc/pki/tls/private/ca.key
        ServerName infosec.local
        DocumentRoot /var/www/html/site2/
        <Directory /var/www/html/site2/>
            DirectoryIndex index.html
        </Directory>
        ServerAlias www.infosec.local
    </VirtualHost>
    
    ```
    

> Note: Fixed typo in DocumentRoot from /var/ww/html/site2/ → /var/www/html/site2/
> 
> 
> **Note**: Fixed `<<VirtualHost>` → `</VirtualHost>`
> 
> **Note**: Added proper `<Directory>` block with `DirectoryIndex`
> 

---

## **Firewall Configuration**

- **Allow traffic on port 8080 (SSL)**:
    
    ```
    firewall-cmd --permanent --add-port=8080/tcp
    
    ```
    
- **Allow traffic on port 8443 (SSL)**:
    
    ```
    firewall-cmd --permanent --add-port=8443/tcp
    
    ```
    
- **Reload the firewall to apply changes**:
    
    ```
    firewall-cmd --reload
    
    ```
    

---

## **Restart Apache**

- **Restart Apache service to apply all changes**:
    
    ```
    systemctl restart httpd.service
    
    ```
    

---

## **Verify Ports Are Open**

- **Check if httpd is listening on custom SSL ports (8080 & 8443)**:
    
    ```
    netstat -nltup | grep httpd
    
    ```
    

> Expected Output Example:
> 
> 
> ```
> tcp6  0  0 :::8080  :::*  LISTEN  1234/httpd
> tcp6  0  0 :::8443  :::*  LISTEN  1234/httpd
> 
> ```
> 

> Alternative Command (modern systems with ss):
> 

```
ss -tuln | grep -E ':8080|:8443'

```

---

## **Final Checks**

- **Test Access via Browser**:
    - Site 1: `https://ai.local:8080`
    - Site 2: `https://infosec.local:8443`
- **Verify SSL is Active**:
    - Look for **padlock icon** in browser address bar.
    - Click padlock → Check certificate details.
- **Verify Certificates**:
    - Ensure `/etc/pki/tls/certs/ca.crt` and `/etc/pki/tls/private/ca.key` are valid and match.
    - Use `openssl` to verify:
        
        ```
        openssl x509 -in /etc/pki/tls/certs/ca.crt -text -noout
        
        ```
        

---

## **Summary of All Commands (Copy-Paste Ready)**

```
Listen 8080 https
Listen 8443 https

```

```
<VirtualHost 192.168.1.42:8080>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/ca.crt
    SSLCertificateKeyFile /etc/pki/tls/private/ca.key
    ServerName ai.local
    DocumentRoot /var/www/html/site1/
    <Directory /var/www/html/site1/>
        DirectoryIndex index.html
    </Directory>
    ServerAlias www.ai.local
</VirtualHost>

```

```
<VirtualHost 192.168.1.42:8443>
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/ca.crt
    SSLCertificateKeyFile /etc/pki/tls/private/ca.key
    ServerName infosec.local
    DocumentRoot /var/www/html/site2/
    <Directory /var/www/html/site2/>
        DirectoryIndex index.html
    </Directory>
    ServerAlias www.infosec.local
</VirtualHost>

```

```
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --permanent --add-port=8443/tcp
firewall-cmd --reload

```

```
systemctl restart httpd.service

```

```
netstat -nltup | grep httpd

```

> All data preserved, corrected, and structured
> 
> 
> **No command or detail missed**
> 
> **Ready for copy-paste execution**
>
