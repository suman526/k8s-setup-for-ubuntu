# k8s-setup-for-ubuntu
These repo already include all steps for Ubuntu 24.04 / noble, repo issue, containerd, kubeadm visibility
Kubernetes Cluster Setup on AWS (kubeadm + Calico)
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

<img width="920" height="519" alt="Screenshot (550)" src="https://github.com/user-attachments/assets/e8173922-850e-410f-8060-08be6bd86811" />

<img width="482" height="60" alt="Screenshot (551)" src="https://github.com/user-attachments/assets/70be86a2-0501-4b99-809c-9710ea739c70" />

<img width="800" height="450" alt="image" src="https://github.com/user-attachments/assets/a373b190-bd3f-44c5-b1d1-ea2d44fd9a8b" />

<img width="522" height="100" alt="Screenshot (548)" src="https://github.com/user-attachments/assets/34242bc1-b64d-4bab-9698-8f8d4e8334ff" />

<img width="920" height="519" alt="Screenshot (550)" src="https://github.com/user-attachments/assets/5db93073-710d-48f7-b75d-633c8dcbc7eb" />

<img width="482" height="60" alt="Screenshot (551)" src="https://github.com/user-attachments/assets/86b22936-2edb-4319-a942-77149e516a75" />

<img width="1350" height="211" alt="Screenshot (552)" src="https://github.com/user-attachments/assets/0869e269-271c-49d4-84d3-3df38b081b03" />

<img width="1366" height="141" alt="Screenshot (553)" src="https://github.com/user-attachments/assets/b1837a94-41e6-47c0-8856-3f90b2b4fa57" />

<img width="1349" height="97" alt="Screenshot (554)" src="https://github.com/user-attachments/assets/14789d22-dbcd-4e54-aa3d-4459c0a782f1" />

<img width="701" height="72" alt="Screenshot (555)" src="https://github.com/user-attachments/assets/4a3e6b8f-375c-46f7-87e2-3683ecd16de4" />

<img width="701" height="72" alt="Screenshot (555)" src="https://github.com/user-attachments/assets/6e188077-73e6-4ef2-8abd-81a27b3104f4" />

<img width="1021" height="276" alt="Screenshot (556)" src="https://github.com/user-attachments/assets/63b853a0-a678-4aed-9d68-19b7aa5fea0d" />

<img width="1058" height="419" alt="Screenshot (576)" src="https://github.com/user-attachments/assets/e940c921-f1f9-48f9-b308-0114a61b3d8c" />

<img width="1366" height="603" alt="Screenshot (577)" src="https://github.com/user-attachments/assets/a7324a4a-6c05-4d64-9b5f-b255c78642e0" />

<img width="962" height="99" alt="Screenshot (578)" src="https://github.com/user-attachments/assets/f55a32bd-bfaf-47db-ad5e-5d705fa166fd" />

<img width="1093" height="79" alt="Screenshot (579)" src="https://github.com/user-attachments/assets/0708fc3b-c0b4-412a-b667-80623e21fcbd" />

<img width="1092" height="95" alt="Screenshot (580)" src="https://github.com/user-attachments/assets/476fefab-21ca-48cd-b8b7-ffdbbb49673c" />

<img width="1088" height="130" alt="Screenshot (581)" src="https://github.com/user-attachments/assets/0e35f702-c3a2-45c6-b438-e063ffa8a9bb" />

<img width="1187" height="450" alt="Screenshot (582)" src="https://github.com/user-attachments/assets/b46221fb-0c0a-4806-9b36-63dc93e3c26c" />

<img width="1366" height="768" alt="Screenshot (583)" src="https://github.com/user-attachments/assets/1fcd43b8-bb0a-4320-a1a5-f685f58c598b" />

<img width="1350" height="325" alt="Screenshot (587)" src="https://github.com/user-attachments/assets/bc855d8f-f390-42ff-8c0b-b6126557a1c6" />

<img width="1344" height="412" alt="Screenshot (588)" src="https://github.com/user-attachments/assets/33eb60a3-2496-4267-a977-2278c58d211d" />

<img width="1348" height="130" alt="Screenshot (589)" src="https://github.com/user-attachments/assets/08137f21-1ef4-45a4-9bf6-7f0e277e7fb4" />

<img width="1354" height="99" alt="Screenshot (590)" src="https://github.com/user-attachments/assets/72ac2e1e-5b7c-4276-a1e5-e35c2bd58d39" />

<img width="706" height="144" alt="Screenshot (591)" src="https://github.com/user-attachments/assets/423d8035-ddd5-49e8-ab94-31d3fd2622cd" />















