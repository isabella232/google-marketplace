# Google Cloud Launcher

## Deploying

To deploy Kong Enterprise use Google Cloud Launcher located at https://console.cloud.google.com/launcher/details/konghq-public/kongenterprise21
![GoogleCloudLauncher](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/GoogleCloudLauncher.png)

Click "Launch"

![KongDeployment](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/KongDeployment.png)

After choosing the Project, Deployment Name, Zone, Machine Type, Boot Disk and Network settings click "Deploy". We recommend to use a static External IP Address.

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


2. Update <b>kong.conf</b>
 
 <pre>
 sudo vi /etc/kong/kong.conf
</pre>

In the NGINX section
<pre>
admin_api_uri = http://<vm-IPv4 Public IP>:8001
admin_listen = 0.0.0.0:8001 reuseport backlog=16384, 0.0.0.0:8444 http2 ssl reuseport backlog=16384
</pre>

In the DATASTORE section
<pre>
database = postgres

pg_user = kong
pg_password = kong
pg_database = kong
</pre>

In the KONG MANAGER section
<pre>
admin_gui_listen = 0.0.0.0:8002, 0.0.0.0:8445 ssl
admin_gui_url = http://<vm-IPv4 Public IP>:8002
</pre>


3. Copy your license.json file in the /etc/kong directory

4. Bootstrap the database
<pre>
sudo /usr/local/bin/kong migrations bootstrap -c /etc/kong/kong.conf
</pre>

5. Start Kong Enterprise
<pre>
sudo /usr/local/bin/kong start -c /etc/kong/kong.conf
</pre>

6. Check Kong Enterprise status
<pre>
$ sudo /usr/local/bin/kong health
nginx.......running

Kong is healthy at /usr/local/kong
</pre>

7. Send a request to Kong
<pre>
curl http://localhost:8001
</pre>

8. Check Kong Manager
<p>
Redirect your browser to
<pre>
`http://<vm-IPv4 Public IP>:8002`
</pre>
![KongManager](https://github.com/Kong/gcp-marketplace/blob/main/screenshots/KongManager.png)



Please modify the network settings to allow and disallow certain HTTP/HTTPS protocols.

If you want to stop Kong, run:
<pre>
sudo kong stop
</pre>






