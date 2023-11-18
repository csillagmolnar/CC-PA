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
        
    2. Defensive machine
        - docker 
        
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
       
    - OWASP ZAP (vulnerability scanner)
        A generic vulnerability scan lead to a false-negative result, it can gives false secure feelings.
        
        Automated scanners detect API8:2023 Security Misconfiguration, e.g
            - cross-origin resource sharing policy is missing
            - transport layer security is weak or missing
            - verbose error messages 
        
        
        Unauthenticated scan - automated
        
        ![ZAP automated scan](images/zap_automated_scan.png)
        ![ZAP automates result](images/zap_automated_result.png)
        
        ![]()
        
        Authenticated scan can be done by manual explore
        ![ZAP manual](images/zap_manual.png)
        ![ZAP alerts](images/zap_alerts.png)
        
3. Attacks
- BOLA (Broken Object Level Authorization) + Excessive Data Exposure

    BOLA = access controls for resources (objects) are not properly implemented -> unauthorized access to a particular object or data manipulation
    
   - BOLA
        
       
   ![BOLA simple](images/bola_5.png)


   - BOLA + Excessive Data Exposure
   
    Go to 'Community' tab
    
    
    See GET request
   ![BOLA GET request](images/bola_1.png)
   
   Copy it to Postman to see response
   
   ![BOLA GET response](images/bola_2.png)
   
   When we go to a specific post, in the endpoint, we can see the user's ID and, as in the response the vehicle ID too
   
   ![BOLA post response](images/bola_3.png)
   
   Exploit BOLA by vehicle ID
   
   Now we can see where is a user's car
   
   ![BOLA ](images/bola_4.png)
    
    
   - How to defense against BOLA, Excessive Data Exposure?
    
        -BOLA: 
            - use Least Privilege principle
            - RBAC 
            - validate user input
    
    
        -EDE: 
            - Test the body of API's response, the body should not contain any sensitive information
            
- MASS  ASSIGNMENT
    - Improper data management
    - API uses the same objects when creating and updating records -> privilege escalation, bypass built-in sec tools
    - 'user' object -> replay POST request: true isAdmin during user creation
        - name
        - email
        - isAdmind
        
    - Bank API example:
        API allows users to update the email address, but this vulnerability might let the user to send a request that updates their account balance
        
     ![Mass get request](images/mass_1.png)
     ![Mass post request response](images/mass_2.png)
     ![Mass new post request w/ response](images/mass_3.png)
     ![New product, new balance in shop](images/mass_4.png)
        
    - Defense: object validation
    
- PAYLOAD INJECTION
    - Weak input validation
    - Rule: never trust in user's input
    - Alter data to manipulate queries
    - Manipulate back-end database, see sensitive data, and may be able to delete or modify them 
    - Possible injection endpoints -> user inputs
    
    1. Use Burp Suite to capture the request and use Foxyproxy
    ![Invalid coupon](images/injection_1.png)
    ![Burp request](images/injecton_2.png)
    
    2. Load the injection patterns to the Payload Settings and uncheck URL-encoding, after start attack
    ![Payload position](images/injection_4.png)
    
    3. 422 Status
    ![Attack error](images/injection_3.png)
    
    4. Delete double quotes
    ![New payload](images/injection_5.png)
    
    5.Get coupon code
    ![Get coupon](imamges/injection_6.png)
    
    6. Apply coupon
    ![Coupon](images/injection_7.png)


        
    

            

