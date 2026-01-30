# k8s-setup-for-ubuntu
These repo already include all steps for Ubuntu 24.04 / noble, repo issue, containerd, kubeadm visibility
Kubernetes Cluster Setup on AWS (kubeadm)
1. AWS EC2 Prerequisites
2. Create 3 EC2 instances:
3. Open the following ports in the same security group:
<img width="800" height="450" alt="image" src="https://github.com/user-attachments/assets/936e7e7e-e4c2-4b4f-a0c8-eb9b3b525d0d" />


<img width="800" height="181" alt="image" src="https://github.com/user-attachments/assets/ae044f22-b0d0-4052-8b9a-6e0caa00869a" />


2. Common Setup (Run on ALL 3 Nodes)
2.1 Set Hostnames

Master
```
sudo hostnamectl set-hostname k8s-master
```

Worker-1
```
sudo hostnamectl set-hostname k8s-worker1
```

Worker-2
```
sudo hostnamectl set-hostname k8s-worker2
```

Reboot:
```
sudo reboot
```

<img width="800" height="96" alt="image" src="https://github.com/user-attachments/assets/4e7e3a9b-bd59-404d-a5ed-20298e1e6346" />

2.2 Disable Swap (Required)
```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```
2.3 Load Kernel Modules
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

2.4 Kernel Networking Settings
```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

<img width="920" height="519" alt="Screenshot (550)" src="https://github.com/user-attachments/assets/e8173922-850e-410f-8060-08be6bd86811" />

3. Install Container Runtime (containerd)
```
sudo apt update
sudo apt install -y containerd
```

Configure containerd
```
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml


Enable SystemdCgroup

sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' \
/etc/containerd/config.toml
```
Restart:
```
sudo systemctl restart containerd
sudo systemctl enable containerd
```

<img width="482" height="60" alt="Screenshot (551)" src="https://github.com/user-attachments/assets/70be86a2-0501-4b99-809c-9710ea739c70" />

4. Install Kubernetes Components (ALL NODES)
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
```

Add Kubernetes repo:
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key |
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' |
sudo tee /etc/apt/sources.list.d/kubernetes.list
<img width="1350" height="211" alt="Screenshot (552)" src="https://github.com/user-attachments/assets/0869e269-271c-49d4-84d3-3df38b081b03" />
```

4. Install Kubernetes Components (ALL NODES)
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
```

Add Kubernetes repo:
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key |
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' |
sudo tee /etc/apt/sources.list.d/kubernetes.list
```
Install:
```
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
---

** When you run these commands in either master or worker node, you will encounter an error **

<img width="1077" height="511" alt="Screenshot (568)" src="https://github.com/user-attachments/assets/3f14a157-2532-4bbd-843c-ec833e428684" />

✅ DEFINITIVE FIX (Do this now)

🔹 Step 1: REMOVE the broken file (mandatory)
```
sudo rm -f /etc/apt/sources.list.d/kubernetes.list
```

Verify it’s gone:
```
ls /etc/apt/sources.list.d/
```

✅ You should NOT see kubernetes.list

🔹 Step 2: Re-create keyring directory (safe)
```
sudo mkdir -p /etc/apt/keyrings
```

🔹 Step 3: Download Kubernetes signing key
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Verify key exists:
```
ls -l /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

🔹 Step 4: Re-create repo file using tee only
⚠️ Do NOT edit manually, do NOT use nano
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

🔹 Step 5: VERIFY the file (very important)
```
cat /etc/apt/sources.list.d/kubernetes.list
```
✅ Output must be exactly ONE LINE:
```
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /
```

🔹 Step 6: Update APT (this should now work)
```
sudo apt clean
sudo apt update
```

🔹 Step 7: Install Kubernetes components
```
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

Verify:
```
kubeadm version
kubectl version --client
```
---

5. Initialize Kubernetes Master (MASTER NODE ONLY)
```
sudo kubeadm init \
--pod-network-cidr=192.168.0.0/16
```
IMPORTANT: Save the kubeadm join command shown in output.

<img width="1349" height="97" alt="Screenshot (554)" src="https://github.com/user-attachments/assets/14789d22-dbcd-4e54-aa3d-4459c0a782f1" />

save it!

5.1 Configure kubectl for ubuntu user
```
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
<img width="701" height="72" alt="Screenshot (555)" src="https://github.com/user-attachments/assets/4a3e6b8f-375c-46f7-87e2-3683ecd16de4" />
```

Verify:
```
kubectl get nodes
```

6. Install Calico CNI (MASTER)
```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

Wait:
```
kubectl get pods -n kube-system
```

All Calico pods must be Running.

<img width="1021" height="276" alt="Screenshot (556)" src="https://github.com/user-attachments/assets/63b853a0-a678-4aed-9d68-19b7aa5fea0d" />

7. Join Worker Nodes

Run the exact command printed during kubeadm init
(on Worker-1 and Worker-2):
```
sudo kubeadm join <MASTER_PRIVATE_IP>:6443 \
--token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>
```

8. Verify Cluster (MASTER)
```
kubectl get nodes -o wide
```

✅ KUBERNETES WORKER NODE SETUP (ERROR-PROOF)

🔹 STEP 1: Set Hostname (already set in our case)
```
sudo hostnamectl set-hostname k8s-worker
sudo reboot
```
(After reboot, reconnect via SSH)

🔹 STEP 2: Disable Swap (MANDATORY)
```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

Verify:

```
free -h
```
Swap must be 0.

🔹 STEP 3: Load Required Kernel Modules
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

<img width="1366" height="603" alt="Screenshot (577)" src="https://github.com/user-attachments/assets/a7324a4a-6c05-4d64-9b5f-b255c78642e0" />


🔹 STEP 4: Apply Kernel Network Settings
```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

Apply:
```
sudo sysctl --system
```

🔹 STEP 5: Install containerd
```
sudo apt update
sudo apt install -y containerd
```

Configure:
```
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

<img width="962" height="99" alt="Screenshot (578)" src="https://github.com/user-attachments/assets/f55a32bd-bfaf-47db-ad5e-5d705fa166fd" />

Enable systemd cgroup:
```
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' \
/etc/containerd/config.toml
```

<img width="1093" height="79" alt="Screenshot (579)" src="https://github.com/user-attachments/assets/0708fc3b-c0b4-412a-b667-80623e21fcbd" />

Restart:
```
sudo systemctl restart containerd
sudo systemctl enable containerd
```

<img width="1092" height="95" alt="Screenshot (580)" src="https://github.com/user-attachments/assets/476fefab-21ca-48cd-b8b7-ffdbbb49673c" />

Verify:
```
systemctl status containerd
```
<img width="1088" height="130" alt="Screenshot (581)" src="https://github.com/user-attachments/assets/0e35f702-c3a2-45c6-b438-e063ffa8a9bb" />

<img width="1187" height="450" alt="Screenshot (582)" src="https://github.com/user-attachments/assets/b46221fb-0c0a-4806-9b36-63dc93e3c26c" />

🔹 STEP 6: Add Kubernetes Repo (FIXED FOR UBUNTU 24.04)

Remove broken repo if exists
```
sudo rm -f /etc/apt/sources.list.d/kubernetes.list
```

Create keyrings dir
```
sudo mkdir -p /etc/apt/keyrings
```

Add Kubernetes signing key
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Add Kubernetes repo
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Verify:
```
cat /etc/apt/sources.list.d/kubernetes.list
```

Expected:

deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /

🔹 STEP 7: Install Kubernetes Components
```
sudo apt clean
sudo apt update
```

<img width="1350" height="325" alt="Screenshot (587)" src="https://github.com/user-attachments/assets/bc855d8f-f390-42ff-8c0b-b6126557a1c6" />

<img width="1344" height="412" alt="Screenshot (588)" src="https://github.com/user-attachments/assets/33eb60a3-2496-4267-a977-2278c58d211d" />

```
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

Verify:
```
kubeadm version
```

<img width="1348" height="130" alt="Screenshot (589)" src="https://github.com/user-attachments/assets/08137f21-1ef4-45a4-9bf6-7f0e277e7fb4" />

🔹 STEP 8:

Our Master node is initialized

🔹 STEP 9: Join Worker to Cluster

Run ONLY when master is ready

sudo kubeadm join <MASTER_PRIVATE_IP>:6443 \
--token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>

<img width="1354" height="99" alt="Screenshot (590)" src="https://github.com/user-attachments/assets/72ac2e1e-5b7c-4276-a1e5-e35c2bd58d39" />

🔹 STEP 10: Verify (ON MASTER)
```
kubectl get nodes
```

<img width="706" height="144" alt="Screenshot (591)" src="https://github.com/user-attachments/assets/423d8035-ddd5-49e8-ab94-31d3fd2622cd" />

















