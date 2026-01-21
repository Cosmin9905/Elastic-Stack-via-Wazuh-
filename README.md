# install Elastic Stack (via Wazuh) on ubuntu 24.04 and kali linux

Wazuh provides an all-in-one installer script that deploys the full stack, including the Wazuh manager, indexer (replacing Elasticsearch), Filebeat, and dashboard (replacing Kibana), effectively delivering Elastic Stack functionality via its integrated components. This works on Ubuntu 24.04, a supported Debian-based distribution. Kali Linux supports manual package installation, with the all-in-one script viable per community guides, though it's primarily for production servers rather than pentesting distros like Kali.[^1_1][^1_2][^1_3]

## Prerequisites

Both distributions require a system with at least 4 CPU cores and 8 GB RAM for all-in-one setups; adjust for production scale. Update packages first: run `sudo apt update && sudo apt upgrade -y` on Ubuntu 24.04 or Kali. Ensure firewall ports like 1514/UDP, 1515/TCP, 514/UDP, 55000/TCP, and 9200/TCP are open for Wazuh components.[^1_4][^1_2][^1_3]

## Ubuntu 24.04 Steps

Download the installer: `curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh && chmod +x wazuh-install.sh`. Run the all-in-one deployment: `sudo ./wazuh-install.sh -a`. Access the dashboard at `https://<IP>` with admin credentials printed at the end (default password expires after first login).[^1_5][^1_2][^1_3][^1_4]

## Kali Linux Steps

Update system: `sudo apt update && sudo apt upgrade -y`. Download and execute the all-in-one script identically to Ubuntu: `curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh && chmod +x wazuh-install.sh && sudo ./wazuh-install.sh -a`. Verify services with `systemctl status wazuh-*`; access dashboard as above, noting Kali's rolling nature may need dependency tweaks.[^1_6][^1_7][^1_3]

## Verification

Check services: `systemctl status wazuh-manager filebeat wazuh-dashboard`. Test indexer connection: `curl -k -u admin:<password> https://localhost:9200`. Import dashboards via the UI and monitor alerts in the Wazuh dashboard.[^1_2][^1_4][^1_1]
<span style="display:none">[^1_10][^1_11][^1_12][^1_8][^1_9]</span>

<div align="center">⁂</div>

[^1_1]: https://www.linkedin.com/pulse/how-i-deployed-full-wazuh-stack-manager-elasticsearch-handaya-i6bgc

[^1_2]: https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html

[^1_3]: https://www.linkedin.com/pulse/installing-wazuh-server-kali-linux-muhammad-abdullah-al-mahdi-wz8hc

[^1_4]: https://documentation.wazuh.com/current/integrations-guide/elastic-stack/index.html

[^1_5]: https://itnixpro.com/install-and-setup-wazuh-server-with-elk-stack-on-ubuntu/

[^1_6]: https://www.linkedin.com/posts/montasirmamun_installing-wazuh-on-a-kali-linux-machine-activity-7187055859712102400-516p

[^1_7]: https://github.com/mriazx/wazuh-setup

[^1_8]: https://www.youtube.com/watch?v=i9AY4fWISBM

[^1_9]: https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elastic-stack-on-ubuntu-22-04

[^1_10]: https://www.youtube.com/watch?v=gGSPwKORsaQ

[^1_11]: https://www.reddit.com/r/Wazuh/comments/1iedzh3/help_needed_setting_up_wazuh_on_ubuntu_arm_mac/

[^1_12]: https://www.youtube.com/watch?v=jyuyIixOT84


---

# Prepare system requirements and hardware sizing for Wazuh with ELK

Wazuh with ELK (or its integrated Wazuh indexer and dashboard) requires sizing based on agents monitored, events per second (EPS), and retention period, typically 90 days. Minimum setups suit small environments under 100 agents, while larger ones demand distributed clusters. Official guidelines provide hardware tiers from basic to recommended, with disk scaling by alert volume.[^2_1][^2_2]

## Minimum Requirements

All-in-one deployments (Wazuh server, indexer, dashboard on one host) need at least 2 CPU cores and 2 GB RAM for the manager, but 4 vCPUs, 8 GB RAM, and 50 GB disk support up to 100 agents with 90-day retention. Disk usage is ~0.1 GB per 90 days for servers (0.25 APS) or 0.04 GB for workstations (0.1 APS).[^2_2][^2_3][^2_1]

## Recommended Hardware

For production, use 8 CPU cores, 8-16 GB RAM, and 100-200 GB SSD per node; scale to multi-node for >100 agents. Wazuh manager alone: 4 cores, 16 GB RAM, 1 TB disk; separate indexer nodes: 8 cores, 32 GB RAM, 1 TB SSD each.[^2_4][^2_5][^2_1][^2_2]

## Sizing Table

| Deployment Type | CPU Cores | RAM (GB) | Disk (GB, 90 days) | Agents Supported |
| :-- | :-- | :-- | :-- | :-- |
| All-in-One Small | 4 | 8 | 50 | <100 [^2_2] |
| All-in-One Medium | 8 | 8 | 100-200 | 100-1000 [^2_2][^2_5] |
| Distributed Manager | 4-8 | 16 | 1000 | 1000+ [^2_4] |
| Indexer Node (per) | 8 | 32 | 1000 | Scales by cluster [^2_5] |

## Scaling Factors

Estimate disk by APS: servers (0.25), workstations (0.1), networks (0.5); multiply by agents and retention. Use SSDs for performance; monitor EPS and add cluster nodes (3+ for HA) for high loads. Wazuh offers a sizing calculator for precise estimates.[^2_6][^2_1]
<span style="display:none">[^2_10][^2_7][^2_8][^2_9]</span>

<div align="center">⁂</div>

[^2_1]: https://documentation.wazuh.com/current/installation-guide/wazuh-server/index.html

[^2_2]: https://documentation.wazuh.com/current/quickstart.html

[^2_3]: https://groups.google.com/g/wazuh/c/G19PxZnc92o

[^2_4]: https://github.com/wazuh/wazuh-documentation/issues/2125

[^2_5]: https://github.com/wazuh/wazuh-dashboard-plugins/issues/1905

[^2_6]: https://groups.google.com/g/wazuh/c/5vFednZa_fU

[^2_7]: https://www.reddit.com/r/Wazuh/comments/xv0yrp/minimum_wazuh_req/

[^2_8]: https://documentation.wazuh.com/current/installation-guide/index.html

[^2_9]: https://www.reddit.com/r/Wazuh/comments/1lx722k/wazuh_sizing_formula/

[^2_10]: https://github.com/mriazx/wazuh-setup


---

# Add Ubuntu 24.04 repository and install Wazuh manager steps

To add the Wazuh repository on Ubuntu 24.04 and install the Wazuh manager, first install prerequisites and import the GPG key for secure package verification. These steps prepare the system for the Wazuh manager package from the official stable repository. Enable services afterward for operation.[^3_1]

## Prerequisites

Run these to update and install required tools:
`sudo apt update`
`sudo apt install gnupg apt-transport-https -y`[^3_2][^3_1]

## Add Repository

Import the GPG key:
`curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg`
Add the repository:
`echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee -a /etc/apt/sources.list.d/wazuh.list`
Update packages:
`sudo apt update`[^3_3][^3_2][^3_1]

## Install Wazuh Manager

Install the package:
`sudo apt install wazuh-manager -y`
Enable and start the service:
`sudo systemctl daemon-reload`
`sudo systemctl enable wazuh-manager`
`sudo systemctl start wazuh-manager`
Verify status:
`sudo systemctl status wazuh-manager`[^3_2][^3_1]

## Post-Installation

Disable repository updates to prevent accidental changes: `sudo sed -i "s/^deb /#deb /" /etc/apt/sources.list.d/wazuh.list && sudo apt update`. Open firewall ports like 1514/UDP, 1515/TCP if needed.[^3_4][^3_1]
<span style="display:none">[^3_10][^3_11][^3_5][^3_6][^3_7][^3_8][^3_9]</span>

<div align="center">⁂</div>

[^3_1]: https://www.linkedin.com/pulse/how-i-deployed-full-wazuh-stack-manager-elasticsearch-handaya-i6bgc

[^3_2]: https://syncbricks.com/installing-wazuh-on-ubuntu-server-24-04/

[^3_3]: https://kifarunix.com/install-wazuh-siem-server-on-ubuntu-24-04/

[^3_4]: https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html

[^3_5]: https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html

[^3_6]: https://kifarunix.com/easy-way-to-install-wazuh-agents-on-ubuntu-debian/

[^3_7]: https://documentation.wazuh.com/current/installation-guide/wazuh-server/index.html

[^3_8]: https://vpsie.com/knowledge-base/how-to-install-wazuh-on-ubuntu/

[^3_9]: https://www.youtube.com/watch?v=gGSPwKORsaQ

[^3_10]: https://github.com/wazuh/wazuh/issues/23133

[^3_11]: https://www.youtube.com/watch?v=wlvXSkXnYz4


---

# Configure Filebeat on Kali Linux as Wazuh agent

To configure Filebeat on Kali Linux as a Wazuh agent, first install the Wazuh agent package, which includes Filebeat for forwarding logs to the Wazuh indexer. Edit the Filebeat configuration to point to your Wazuh server/indexer, deploy certificates for secure communication, and start services. This setup sends agent events and alerts to the central indexer.[^4_1][^4_2]

## Prerequisites

Ensure the Wazuh repository is added (as previously done for manager):
`sudo apt update && sudo apt install gnupg apt-transport-https lsb-release -y`
`curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg`
`echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list`
`sudo apt update`[^4_2]

## Install Wazuh Agent

Deploy the agent with manager details (replace `<MANAGER_IP>` with your Wazuh manager IP):
`WAZUH_MANAGER="<MANAGER_IP>" sudo apt install wazuh-agent -y`
This installs the agent and Filebeat. Enable and start:
`sudo systemctl daemon-reload`
`sudo systemctl enable wazuh-agent`
`sudo systemctl start wazuh-agent`[^4_3][^4_2]

## Configure Filebeat

Download Wazuh Filebeat config:
`sudo curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.x/tpl/wazuh/filebeat/filebeat.yml`
Edit `/etc/filebeat/filebeat.yml`: Set `output.elasticsearch.hosts` to your indexer IP:9200 (e.g., `hosts: ["<INDEXER_IP>:9200"]`). Create keystore:
`sudo filebeat keystore create`
`echo admin | sudo filebeat keystore add username --stdin --force`
`echo admin | sudo filebeat keystore add password --stdin --force` (use your indexer credentials).[^4_1][^4_4]

## Deploy Certificates

Extract certs from `wazuh-certificates.tar` (generated on manager):
`sudo mkdir /etc/filebeat/certs`
`sudo tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./<NODE_NAME>.pem ./<NODE_NAME>-key.pem ./root-ca.pem`
`sudo mv /etc/filebeat/certs/<NODE_NAME>.pem /etc/filebeat/certs/filebeat.pem`
`sudo mv /etc/filebeat/certs/<NODE_NAME>-key.pem /etc/filebeat/certs/filebeat-key.pem`
`sudo chmod 500 /etc/filebeat/certs && sudo chmod 400 /etc/filebeat/certs/*`[^4_4][^4_1]

## Start and Verify

Enable/start Filebeat:
`sudo systemctl daemon-reload`
`sudo systemctl enable filebeat`
`sudo systemctl start filebeat`
Test output: `sudo filebeat test output`
Check agent status on dashboard. Disable repo: `sudo sed -i "s/^deb/#deb/" /etc/apt/sources.list.d/wazuh.list && sudo apt update`[^4_1][^4_4][^4_2]
<span style="display:none">[^4_10][^4_11][^4_5][^4_6][^4_7][^4_8][^4_9]</span>

<div align="center">⁂</div>

[^4_1]: https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html

[^4_2]: https://documentation.wazuh.com/current/integrations-guide/elastic-stack/index.html

[^4_3]: https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html

[^4_4]: https://www.linkedin.com/pulse/how-i-deployed-full-wazuh-stack-manager-elasticsearch-handaya-i6bgc

[^4_5]: https://documentation.wazuh.com/current/deployment-options/deploying-with-ansible/roles/wazuh-filebeat.html

[^4_6]: https://logit.io/docs/integrations/wazuh/

[^4_7]: https://www.youtube.com/watch?v=RiD041ElUqU

[^4_8]: https://documentation.wazuh.com/current/deployment-options/offline-installation/step-by-step.html

[^4_9]: https://github.com/Curious4Tech/Wazuh-Deployment-Guide-Secure-Endpoint-Monitoring-for-Linux-and-Windows

[^4_10]: https://www.youtube.com/watch?v=RE27kuIe1b8

[^4_11]: https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html


---

# Install and configure Elasticsearch 8.x on Ubuntu 24.04 securely

Elasticsearch 8.x installs securely by default on Ubuntu 24.04 with TLS encryption and basic authentication enabled via auto-generated certificates and an elastic superuser password. Use the official APT repository for the latest stable version, compatible across Ubuntu releases. Post-installation involves service management and API testing with the provided credentials.[^5_1][^5_2]

## Prerequisites

Update packages: `sudo apt update && sudo apt upgrade -y`. Install tools: `sudo apt install curl gpg apt-transport-https lsb-release -y`. Increase vm.max_map_count to 262144: `sudo sysctl -w vm.max_map_count=262144` and make permanent by adding `vm.max_map_count=262144` to `/etc/sysctl.conf`.[^5_2][^5_1]

## Add Repository

Import GPG key: `curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg`. Add repo file:

```
cat <<EOF | sudo tee /etc/apt/sources.list.d/elasticsearch.sources
Types: deb
URIs: https://artifacts.elastic.co/packages/8.x/apt
Suites: stable
Components: main
Architectures: amd64
Signed-By: /usr/share/keyrings/elasticsearch-keyring.gpg
EOF
```

Update: `sudo apt update`.[^5_1][^5_2]

## Install Elasticsearch

Install package: `sudo apt install elasticsearch -y`. Note the generated elastic password displayed during install. Enable and start: `sudo systemctl daemon-reload && sudo systemctl enable --now elasticsearch`.[^5_2][^5_1]

## Security Configuration

```
Security is auto-enabled with TLS certs in `/etc/elasticsearch/certs/`. Test API: `curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic:<PASSWORD> https://localhost:9200` (replace <PASSWORD>). For remote access, edit `/etc/elasticsearch/elasticsearch.yml`: set `network.host: 0.0.0.0` or specific IP, then `sudo systemctl restart elasticsearch`. Open port 9200 in UFW: `sudo ufw allow from <CLIENT_IP> to any port 9200`.[^5_3][^5_1][^5_2]
```


## Verification

Check status: `sudo systemctl status elasticsearch`. View logs: `sudo journalctl -u elasticsearch`. Reset password if needed: `sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic`.[^5_1][^5_2]
<span style="display:none">[^5_10][^5_11][^5_4][^5_5][^5_6][^5_7][^5_8][^5_9]</span>

<div align="center">⁂</div>

[^5_1]: https://linuxcapable.com/how-to-install-elasticsearch-8-on-ubuntu-linux/

[^5_2]: https://documentation.wazuh.com/current/integrations-guide/elastic-stack/index.html

[^5_3]: https://docs.vultr.com/how-to-install-elasticsearch-on-ubuntu-24-04

[^5_4]: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-22-04

[^5_5]: https://discuss.elastic.co/t/installing-elasticsearch-on-ubuntu-24/372215

[^5_6]: https://phoenixnap.com/kb/install-elasticsearch-ubuntu

[^5_7]: https://oneuptime.com/blog/post/2026-01-15-install-elasticsearch-ubuntu/view

[^5_8]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package

[^5_9]: https://www.fosstechnix.com/how-to-install-elastic-stack-on-ubuntu-24-04/

[^5_10]: https://www.youtube.com/watch?v=NptsWT5ykJs\&vl=en

[^5_11]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/installing-elasticsearch

