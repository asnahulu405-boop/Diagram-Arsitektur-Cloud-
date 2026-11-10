graph TD
    %% User Layer
    subgraph Users ["Pengguna Layanan Kampus"]
        M[Mahasiswa / Dosen]
    end

    %% Security & Edge Layer
    subgraph Edge ["Security & Content Delivery"]
        DNS[Cloud DNS]
        WAF[Cloud WAF / DDoS Protection]
        CDN[Content Delivery Network - CDN]
    end

    %% Cloud Infrastructure VPC
    subgraph VPC ["Virtual Private Cloud (VPC)"]
        
        %% Public Subnet
        subgraph PublicSubnet ["Public Subnet"]
            ALB[Application Load Balancer]
            NAT[NAT Gateway]
        end

        %% Private Subnet (Application)
        subgraph PrivateSubnetApp ["Private Subnet - App Layer"]
            ASG[Auto Scaling Group / Kubernetes Cluster]
            App1[App Instance 1]
            App2[App Instance 2]
            App3[App Instance N]
        end

        %% Private Subnet (Database & Cache)
        subgraph PrivateSubnetDB ["Private Subnet - Data Layer"]
            Redis[(Redis Cache)]
            PrimaryDB[(Primary DB - Write)]
            ReplicaDB[(Read Replica DB - Read)]
        end
    end

    %% DevOps & Monitoring
    subgraph DevOpsTools ["DevOps & Operations"]
        CI[GitHub Actions CI/CD]
        Prometheus[Prometheus & Grafana]
    end

    %% Connections
    M --> DNS
    DNS --> WAF
    WAF --> CDN
    CDN --> ALB
    ALB --> ASG
    ASG --> App1
    ASG --> App2
    ASG --> App3
    
    App1 --> Redis
    App2 --> Redis
    App3 --> Redis

    App1 --> PrimaryDB
    App2 --> PrimaryDB
    App3 --> PrimaryDB

    App1 --> ReplicaDB
    App2 --> ReplicaDB
    App3 --> ReplicaDB

    PrimaryDB -. Synchronization .-> ReplicaDB
    NAT --> PrivateSubnetApp
    CI -. Auto Deploy .-> ASG
    Prometheus -. Monitor .-> ASG
    Prometheus -. Monitor .-> PrimaryDB
