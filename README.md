
##  Some basic commands and setup procedures are shadowed  here   like terraform init and apply and all assuming that you already know basic to  more than intermediate knowledge about prerequisites. 

 ## Stacks :

Component Overview:
—
1. **Terraform* - Infrastructure provisioning
2. **Docker* - Containerisation
3. **NGINX Reverse Proxy* - Entry point for all external traffic
4. **Authelia SSO* - Centralized authentication and authorization
5. **Redis* - Session storage for Authelia
6. **Gitea* - Git repository management
7. **Grafana* - Data visualization and dashboards
8. **Prometheus* - Metrics collection and storage
9. **Promtail* - Log collection agent
10. **Loki* - Log aggregation and storage
—-

**NODE EXPORTER IS OPTIONAL*

   ##changing the image version and  other configurations which is not inteded to change may brake the OIDC auth and  result in  the cluster and container failure.##

# Production Readiness & Important Notes

## 🚀 **Production Status: 75% Ready**

This setup provides a solid foundation for production use but requires additional hardening and configuration for full production deployment.

## ⚠️ **Production Tweaks & Modifications Needed**

### 🏗️ **Infrastructure Management**
- **Terraform State Backend**: Currently uses local state file for demonstration purposes
  - **Required for Production**: Configure remote backend (S3)
  - **Recommendation**: Use state locking with DynamoDB (AWS) or equivalent
  - **Security**: Enable state file encryption

### 🔐 **Security Hardening**
- **Docker Compose Secrets**: Passwords and secrets are currently in plain text
  - **Required for Production**: Implement Docker Secrets or external secret management
  - **Options**: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, Kubernetes Secrets
  - **Implementation**: Use `docker secret create` and reference in compose files

### 🔑 **Configuration Security**
- **Exposed Secrets**: All configuration hashes and secrets are visible for educational purposes and better understanding and cloning this setup 
  - **Required for Production**: 
    - Regenerate all passwords,and secret hashes
    - Use environment variables or secret management systems
    - Implement proper secret rotation policies
    - Enable audit logging for secret access

### 🛡️ **Additional Production Considerations**
- **SSL Certificates**: Implement proper certificate management SAN Standard (Let's Encrypt, Cloudflare, AWS )
- **Backup Strategy**: Configure automated backups for databases and repositories
- **

## 🔧 **Known Limitations**

### PKCE Support in Gitea
```yaml
# Current Authelia configuration for Gitea
clients:
  - client_id: 'gitea'
    require_pkce: false  # Disabled due to Gitea limitation
```




```yaml
clients:
  - client_id: 'gitea'
    client_name: 'Gitea'
    # Remove client_secret for public client
    public: true                              # Change to PUBLIC client
    authorization_policy: 'one_factor'
    redirect_uris:
      - 'https://ec2-52-66-251-211.ap-south-1.compute.amazonaws.com/gitea/user/oauth2/authelia/callback'
    scopes:
      - 'openid'
      - 'profile'
      - 'groups'
      - 'email'
    userinfo_signing_algorithm: none
    response_types:
      - 'code'
    grant_types:
      - 'authorization_code'
    response_modes:
      - 'form_post'
      - 'query'
      - 'fragment'
    token_endpoint_auth_method: 'none'        # Change to 'none' for public clients
```



 **PKCE challenge method is  available for public_clients only ..**

**Issue**: Gitea currently does not support PKCE (Proof Key for Code Exchange) when acting as an OAuth2 client.

**Impact**: Slightly reduced security for the OAuth2 flow between Gitea and Authelia.

**Status**: This is a known limitation in Gitea's OAuth2 client implementation as per official documentation.

**Future**: PKCE support may be added in future Gitea releases. Monitor [Gitea GitHub Issues](https://github.com/go-gitea/gitea/issues) for updates.

## 📚 **Educational Purpose Disclaimer**

> **Note**: This repository is designed for learning and demonstration purposes. All secrets, hashes, and configuration values are intentionally visible to help users understand the complete setup process.

**For Production Deployment:**
1. Generate new secrets and configuration hashes
2. Implement proper secret management
3. Follow security best practices outlined above
4. Conduct security testing and penetration testing 
5. Implement monitoring  with alert rules 

## 🎉 **Final Note**

**THIS WAS FUN! 😄**

This project demonstrates a comprehensive infrastructure setup with modern DevOps practices including:
- Infrastructure as Code (Terraform)
- Containerized Services (Docker Compose)
- Single Sign-On (Authelia) OIDC Integration for maximize security with PKCE S256 encryption//one-factor --this is a simple yet robust SSO auth setup for beginning. PKCE is known to prevent authorization infiltration and middle attacks for authentication methods 
- Self-hosted Git repositary (Gitea)
- Observability Stack (Prometheus, Grafana, Loki,Promatail)
- Reverse Proxy (NGINX)
- Fast Session Management (Redis)

Feel free to use this as a foundation for your own infrastructure projects!

---

## 📞 **Support & Contributions**

- 🐛 **Issues**: Please report any bugs or issues
- 🚀 **Enhancements**: Contributions and improvements are welcome
- 📖 **Documentation**: Help improve documentation for better clarity

**Happy DevOps! 🚀**
 
 --- 
### 7. Access URLS will be kept for 4 to 5 days 
```bash
🌐 Gitea: https://ec2-52-66-251-211.ap-south-1.compute.amazonaws.com/gitea    ##admin/admin123
📊 Grafana: https://ec2-52-66-251-211.ap-south-1.compute.amazonaws.com/grafana   ##admin/admin123
🔐 Authelia: https://ec2-52-66-251-211.ap-south-1.compute.amazonaws.com/authelia/  
```
- ##chalenges  No  serious issues were encountered  only minor issues were there and solved it .
 in example image compatibility -bugs  for some image versions and official documentation is incomplete and not accurat

## Infrastructure Setup Screenshots
![Infrastructure](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/Infrastructure.png)
### Docker Containers
![Docker Containers](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/DOCKER%20containers.png)

### Terraform Infrastructure
![Terraform Infrastructure](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/Terraform-infra.png)

## Authentication & SSO Setup

### SSO Homepage
![SSO Homepage](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/SSO%20HOMEPAGE.png)

### Authelia Dashboard
![Authelia Dashboard](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/authelia%20dashboard.png)

### Authentication Setup Readiness
![Auth Setup Readiness](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/auth%20setup%20readyness.png)

### Configuration Preview
![Preview Configuration Default](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/Preview%20conf%20default%20.png)

## Gitea Git Server

### Gitea Instance
![Gitea Instance](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/gitea%20instance.png)

### Gitea Web UI
![Gitea Web UI](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/gitea%20WEBUI.png)

### Gitea Dashboard
![Gitea Dashboard](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/gitea%20dashboard.png)

### Gitea SSO Integration
![Gitea SSO](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/gitea%20SSO.png)

## Monitoring & Observability

### Grafana SSO
![Grafana SSO](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/GRAFANA%20SSO.png)

### Grafana Sign-in Authentication
![Grafana Sign-in Auth](https://raw.githubusercontent.com/PrasadP744/sre-task/main/images/grafana%20sign%20in%20auth%20.png)
 


