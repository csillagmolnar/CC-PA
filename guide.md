# API penetration testing

## Guide line
    1. Describe the structure of the current network and machine setups
    2. Reconnaissance - DEMO?
    3. Attack - DEMO
    4. Detection of penetration - DEMO
    5. Appropiate security measures
    
## Tools
    1. Offensive machine setup
        - Burp Suite
        - Postman
        - Foxyproxy
        - mitmproxy2swagger
        - kiterunner
        - gobuster
        - OWASP ZAP
        
## Documentation
1. Describe the structure of the current network and machine setups

    LAN: 10.10.24.0/24
    
    WAN: 10.10.0.0/24
    
    - Defensive machine (10.10.24.103)
        - crAPI install    
            Github repository: https://github.com/OWASP/crAPI	
            
            Docker install:
                Docker is a platform that enables developers to automate the deployment of applications within lightweight, portable containers.
               
                `$ sudo apt install -y docker.io`
                `$ sudo systemctl enable docker –now`

        - change website’s IP address
             ![Crapiweb](images/dockeryml_crapiweb.png)
             ![Mailhog](images/dockeryml_mailhog.png)
            
            Finally, you need to rebuild the crapi-web container using the following command:
            
            `$sudo docker compose -f docker-compose.yml --compatibility up -d`
    
    - Firewall (10.10.24.1/10.10.0.15)
    
        - set a HAproxy
            ![HA_server](images/HA_realservers.png)
            ![HA_backend](images/HA_backend.png)
            ![HA_public](images/HA_publicservice.png)
        - appropiate floating rules to HAproxy
            ![Opnsense floating ruls](images/opnsense_floating_rules.png)
            
            And now our website is available from WAN
        - allow ping
            ![NMAP ping error](images/nmap_error_msg.png) 
            
            ![Opnsense new floating rule](images/opnsense_pingrule1.png)
            ![Opnsense new floating rule](images/opnsense_pingrule2.png)
    - Offensive machine (10.10.0.138)
        - Foxyproxy
            
            It heps you configure your web browser to use a forward proxy. 
            
            Postman and Burp Suite have their own proxy, with Foxyproxy it is very easy to switch between them.
            
            ![Foxy-postman](images/foxyproxy_postman.png)
            ![Foxy-burp](images/foxyproxy_burp.png)
            
        - Add Burp Suite certificate to the browser (mitm proxy)
        - Install postman, mitmproxy2swagger, kiterunner, gobuster, OWASP ZAP
            
2. Reconnaissance - Demo
    - Nmap
        General detection:
            `nmap -sC -sV 10.10.0.15`
            
            ![Nmap general](images/nmap_general.png)
            
        All port scan:
            ![Nmap all port](images/nmap_allport.png)
            
            Check every port on browser
            
        Check a specific port's service:
            ![Nmap 8025 service](images/nmap_service_8025.png)
            
   
    - Gobuster (directory brute-force)
        ![Gobuster directories](images/gobuster_result.png)
        
    - Kiterunner (API endpoints)
        ![Kiterunner small](images/kiterunner_smallresult.png)
        ![Kiterunner result](images/kiterunner_wellknown.png)
        
    - Dev tool + mitmproxy2swagger
        Filter : api 
        ![Dev tool request header](images/devtool_headers2.png)
        ![Dev tool request header2](images/devtool_headers.png)
        
        Start capture everything on the website
        
        Network -> Settings -> Persist logs
        
        Use everything on the website
        
        Save the HAR file
        
        Create swagger file:
        
        `$ sudo mitmproxy2swagger -i crapi.har -o crapi.yml -p http://10.10.0.15 --format har` 
        
        ![spec.yml ignore lines](images/specyml_ignore.png)
        
        ![mitmproxy2swagger error msg](images/mitm2swagger_terminalerror.png)
        
        ![mitm2s solved](images/mitm2swagger_codeproblem.png)

    - Swagger
        Check documentation        
        
        ![OpenAPI doc](images/swagger_openapi.png) 
        
    

            

