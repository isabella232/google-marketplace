# Google Cloud Launcher

## Deploying

To deploy Kong Enterprise use Google Cloud Launcher located at https://console.cloud.google.com/launcher/details/konghq-public/kongenterprise21
![GoogleCloudLauncher](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/GoogleCloudLauncher.png)

Click "Launch"

![KongDeployment](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/KongDeployment.png)

After choosing the Project, Deployment Name, Zone, Machine Type, Boot Disk and Network settings click "Deploy".

![KongDeployment2](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/KongDeployment2.png)

Your Kong Enterprise 2.1 is deployed and available. Now you need to inject you Kong Enterprise 2.1 license to get it running.

![SSH](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/SSH.png)

 Click "SSH" to open a terminal.

![Terminal](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/Terminal.png)


Proceed with the following steps:

1. Copy <b>kong.conf</b> configuration file
<pre>
sudo cp /etc/kong/kong.conf.default /etc/kong/kong.conf
</pre>


sudo vi /etc/kong/kong.conf

update kong.conf with the following:

In the NGINX section
admin_api_uri = http://<vm-IPv4 Public IP>:8001
admin_listen = 0.0.0.0:8001 reuseport backlog=16384, 0.0.0.0:8444 http2 ssl reuseport backlog=16384



In the DATASTORE section
database = postgres

pg_user = kong
pg_password = kong
pg_database = kong



In the KONG MANAGER section
admin_gui_listen = 0.0.0.0:8002, 0.0.0.0:8445 ssl
admin_gui_url = http://<vm-IPv4 Public IP>:8002




2. Copy your license.json file in the /etc/kong directory



3. Run "sudo /usr/local/bin/kong migrations bootstrap -c /etc/kong/kong.conf"

4. Start Kong Enterprise with "sudo /usr/local/bin/kong start -c /etc/kong/kong.conf"

Please modify the network settings to allow and disallow certain HTTP/HTTPS protocols.











Starting Kong Enterprise
Edit the /etc/environment file

sudo vi /etc/environment

add these lines
LANGUAGE=en_US.utf-8
LC_ALL=en_US.UTF-8
LC_CTYPE=UTF-8
LANG=en_US.utf-8

Exit the terminal and login again

In your $HOME directory change the .bashrc file adding the following line in the end:
ulimit -n 4096

Edit kong.conf file
sudo cp /etc/kong/kong.conf.default /etc/kong/kong.conf
sudo vi /etc/kong/kong.conf

update kong.conf with:
pg_host = 1.2.3.4 (use the PosgreSQL host IP)
pg_user = kong
pg_password = kong
admin_listen = 0.0.0.0:8001, 0.0.0.0:8444 ssl


Edit the license file
sudo vi /etc/kong/license.json
{"license":{"signature":"36be3821628dc14259433b157036d5b6ebb79530a4109f2960b22ab9861be73270cf1d714cca045204a7d9b1be39d797c3213c76ae91e6c64262d870c4979d1c","payload":{"customer":"Kong_SE_Demo","license_creation_date":"2019-11-03","product_subscription":"Kong Enterprise Edition","admin_seats":"5","support_plan":"None","license_expiration_date":"2020-12-12","license_key":"0011K000022IA3HQAW_a1V1K000007KlJMUA0"},"version":1}}



sudo chmod -R 777 /usr/local/kong


Define Kong Admin password
export KONG_PASSWORD=kong

Initialize Kong's database
sudo /usr/local/bin/kong migrations bootstrap -c /etc/kong/kong.conf

Start Kong
sudo /usr/local/bin/kong start -c /etc/kong/kong.conf

Check if Kong is running
$ sudo /usr/local/bin/kong health
nginx.......running

Kong is healthy at /usr/local/kong


Send a request to Kong
curl -i http://localhost:8001
http :8001
http --verify=no https://localhost:8443
http --verify=no https://localhost:8444


If you want to stop Kong, run:
sudo kong stop






