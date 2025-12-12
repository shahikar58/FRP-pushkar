# FRP-pushkar
#  FRP (Fast Reverse Proxy) with AWS EC2



## 🔹 1. What is FRP?


- **FRP (Fast Reverse Proxy)** is an open-source tool used to expose local servers behind NAT/firewalls to the public internet.

- 
- Works as a **client–server model**:
- 
  - **frps** → Runs on a public server (e.g., AWS EC2)
  - 
  - **frpc** → Runs locally (on your machine) and forwards traffic to frps
 
  - 
- Common use cases: hosting local websites, APIs, SSH, RDP, or game servers publicly.

---



## 🔹 2. Architecture



- **frpc (client)**: Defines which local service to expose.

- 
- **frps (server)**: Listens for connections from frpc and forwards them.  

---




## 🔹 3. Installing FRP

### On AWS EC2 (Server)


1. Launch an **EC2 instance** (Ubuntu recommended).
  
2. Update and install tools:
   ```
   sudo apt update && sudo apt upgrade -y
   
   wget https://github.com/fatedier/frp/releases/download/v0.60.0/frp_0.60.0_linux_amd64.tar.gz
   
   tar -xvzf frp_0.60.0_linux_amd64.tar.gz
   cd frp_0.60.0_linux_amd64
   ```


3. Configure frps.ini:

```
   [common]
bind_port = 7000
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = password123
```


4. Start FRP server:

```
 ./frps -c frps.ini
```   




5. Open required security group ports in AWS:

7000 (FRP communication)

7500 (Dashboard, optional)

Ports of your app (e.g., 8080, 22, etc.)



On Local Machine (Client)

1. Download FRP (same version as server).


2. Extract and enter folder:
   
```
tar -xvzf frp_0.60.0_linux_amd64.tar.gz
cd frp_0.60.0_linux_amd64
```

3. Configure frpc.ini:

```
[common]
server_addr = <AWS_EC2_Public_IP>
server_port = 7000

[web]
type = tcp
local_ip = 127.0.0.1
local_port = 8080
remote_port = 8080
```


local_port: Port of service running locally (e.g., web app on 8080).

remote_port: Port on EC2 that will expose the service.



4. Start FRP client:

```
./frpc -c frpc.ini
```


🔹 4. Accessing Local Service

Open in browser:

```
http://<AWS_EC2_Public_IP>:8080
```

FRP forwards requests from EC2 → local system.




🔹 5. Useful Commands

Run FRP in background:

```
nohup ./frps -c frps.ini > frps.log 2>&1 &
nohup ./frpc -c frpc.ini > frpc.log 2>&1 &
```

Stop running FRP:

```
pkill frps
pkill frpc
```



🔹 6. Security Tips

Change default ports (7000, 7500).

Use strong dashboard password.

Restrict access to FRP ports via AWS Security Groups.

Prefer using domain + SSL (with Nginx/Certbot) for production.



---




🔹 7. Quick Example

👉 Suppose you have a local Flask app on port 5000.

In frpc.ini:

```
[flask_app]
type = tcp
local_ip = 127.0.0.1
local_port = 5000
remote_port = 9000
```

Access from anywhere:
```
http://<AWS_EC2_IP>:9000
```


---



✅ With this setup, you’ve exposed a local development server to the internet securely via FRP on AWS EC2.
