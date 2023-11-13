# API penetration testing

## Guide line
    1. Describe the structure of the current network and machine setups
    2. Reconnaissance and attack
    3. Detection of penetration
    4. Appropiate security measures
    
## Tools
    1. Offensive machine setup
        - Burp Suite
        - Postman
        - Foxyproxy
        - mitmproxy2swagger
        - kiterunner
        - gobuster
        
## Documentation
1. Describe the structure of the current network and machine setups

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
    - Offensive machine (10.10.0.138)
        - Foxyproxy
            
            It heps you configure your web browser to use a forward proxy. 
            
            Postman and Burp Suite have their own proxy, with Foxyproxy it is very easy to switch between them.
            
            ![Foxy-postman](images/foxyproxy_postman.png)
            ![Foxy-burp](images/foxyproxy_burp.png)
            
    
