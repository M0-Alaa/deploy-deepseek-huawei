# deploy-deepseek-huaweiCloud
Deploy DeepSeek on Huawei Cloud using Ollama and deploy Open WebUI
# Deploy DeepSeek on Huawei Cloud with Docker & Open WebUI

This guide explains how to deploy [DeepSeek](https://ollama.com/library/deepseek) on Huawei Cloud using an ECS instance, Docker, and Open WebUI.

---

## Step 1: Log in to Huawei Cloud Console
1. Log in to your Huawei Cloud account.
2. From the **Services List**, search for **VPC** and select the **VPC Service**.

---

## Step 2: Create a VPC
1. On the VPC console page, click **Create**.
2. Fill in:
   - **VPC Name**: `vpc-project`
   - **Subnet Name**: (choose any meaningful name)
3. Click **Create Now** to create the VPC.

---

## Step 3: Create an ECS Instance
1. Return to the Huawei Cloud console and search for **ECS**.
2. Click **Buy ECS**.
3. Choose the following specifications:
   - **Flavor**: `m7n.2xlarge.8` (memory-optimized, 64 GiB RAM)
   - **Billing Mode**: Pay per use
   - **OS**: Ubuntu Server 22.04
   - **EIP**: Automatic assignment
   - **System Disk**: Ultra High I/O, 200 GiB
4. Submit and create the ECS instance.

---

## Step 4: Create a Security Group
While creating the ECS instance:
1. Click **Create Security Group**.
2. Add inbound rule to allow SSH:
   - **Port Range**: 22  
   - **Source**: `0.0.0.0/0`  
   *(needed for MobaXterm/SSH access)*

---

## Step 5: Retrieve ECS Public IP and Connect via CLI
1. Go to the ECS Console and locate your instance.
2. Copy the **Public IP** address.
3. Open your SSH client (MobaXterm, PuTTY, etc.) and connect:
   ```bash
   ssh -i <your-key.pem> root@<ecs-public-ip>

---

## Step 6: Install Docker on the ECS Instance

1. Remove any old/conflicting packages:

`for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done`


2. Set up Docker’s apt repository:

```sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
```echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update'''
```

3. Install Docker packages:

`sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`


4. Verify Docker is running:

`sudo systemctl status docker`


If it’s not running:

`sudo systemctl start docker`


5. Test installation:

`sudo docker run hello-world`

---

## Step 7: Pull and Run Ollama Image

1. Run the Ollama Docker image:

`docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama`


2. Pull and run the DeepSeek model:

  - Browse the Ollama Library.

  - Example for 'deepseek-r1:7b':

`docker exec -it <container_id> ollama run deepseek-r1:7b`

  - Type '/bye' to exit.

---

## Step 8: Set Up Open WebUI
```
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main 
```

---

## Step 9: Access Open WebUI

- Copy the Public IP of your ECS instance.

- Open your browser and go to:

`http://<ecs-public-ip>:3000`


- Click Get Started and create an account (name, email, password).

---

## Step 10: Start Chatting 🎉

- You can now chat with DeepSeek using the Open WebUI interface.

---

## Live_Demo for the Project
- https://youtu.be/khlOO3TlFUQ?si=-tSSCm2gP07FFK_3
