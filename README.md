# VerxDesafio
Desafio para migração de sistema legado para uma infraestrutura híbrida.
Solução Arquitetural para Migração de Sistema Legado para Modelo Híbrido com Azure
1. Contexto e Objetivo

O desafio consiste em migrar um sistema legado para um modelo híbrido, integrando infraestrutura on-premises e a nuvem pública Azure. A solução atende aos requisitos de dimensionamento, FinOps, automação via IaC, e inclui um diagrama da topologia com justificativas tecnológicas. Diferenciais como Disaster Recovery, monitoramento e referência ao modelo OSI foram incorporados para robustez.

2. Dimensionamento de Recursos
2.1 Requisitos Estimados

Com base em sistemas legados típicos, assumimos:

    Carga de Trabalho: 50 usuários concorrentes, com picos de 20% adicionais.
    
2.2 Configuração de Recursos

    On-Premises:
        Servidores de Aplicação: 2 VMs com 4 vCPUs, 16 GB RAM cada.
        Banco de Dados: 1 servidor físico com 8 vCPUs, 32 GB RAM, armazenamento de 500 GB.
        Escalabilidade Horizontal: Adição de VMs sob demanda via Hyper-V.
    Azure:
        Azure Virtual Machines: 2 VMs D2s v5 (2 vCPUs, 8 GB RAM).
        Azure SQL Database: General Purpose, 2 vCores, 10 GB de armazenamento;
        Armazenamento: Azure Blob Storage para arquivos estáticos, Premium SSD (P10, 500 IOPS) para discos das VMs.
        Reserva de Capacidade: 20% de buffer em CPU e memória para imprevistos.

3. Estratégias de FinOps
3.1 Otimização de Custos

    Azure Reservations: Compromisso de 1 ano para VMs, reduzindo custos em ~30%.
    Shape: Utilizar o shape adequado conforme necessidade da aplicação (CPU ou Memória).
    Blob Storage Lifecycle: Transição de arquivos para Cool tier após 90 dias.
    Right-Sizing: Monitoramento via Azure Advisor para ajustar recursos subutilizados.
    Automation Account: Desligar os recursos em horários sem utilização.
    Tagging: Tags obrigatórias (ex.: "Projeto", "Ambiente") para rastreamento granular de custos.

3.2 Governança

    Azure Cost Management: Relatórios mensais para identificar desvios.
    Budget Alerts: Alertas para gastos acima de 80% do orçamento.
    Reserved Instances: Reserva de Azure SQL Database para uso contínuo, com economia de ~25%.

4.  Justificativa das Escolhas Tecnológicas

    Azure: Escolhida por sua integração com sistemas legados (Azure Hybrid Benefit), suporte a ambientes híbridos (VPN Gateway, Azure Arc) e ferramentas de FinOps (Cost Management, Advisor). Alternativas como AWS ou GCP foram consideradas, mas Azure oferece maior compatibilidade com stacks Microsoft, comuns em sistemas legados.
    VMs D2s v5: Custo-benefício ideal, com suporte a cargas variáveis.
    Azure SQL Database: Compatibilidade com bancos relacionais legados, backups automáticos e escalabilidade gerenciada.
    VMware vSphere: Mantém consistência com infraestrutura on-premises existente.
    Terraform: Automação de IaC para consistência entre ambientes.
    Ansible: Configuração de servidores para padronização.

5. Automação via IaC
5.1 Terraform

Exemplo de código para provisionar VMs e Azure SQL Database:
<xaiArtifact artifact_id="06196707-71be-4427-9392-559f55089f79" artifact_version_id="ff4f0710-c28e-43d5-b7ca-8f4af66a4402" title="main.tf" contentType="text/x-terraform"> provider "azurerm" { features {} }

resource "azurerm_virtual_network" "main" {
name                = "hybrid-vnet"
address_space       = ["10.0.0.0/16"]
location            = "eastus"
resource_group_name = "hybrid-rg"
}

resource "azurerm_linux_virtual_machine" "app" {
count               = 2
name                = "app-vm-${count.index}"
resource_group_name = "hybrid-rg"
location            = "eastus"
size                = "Standard_D2s_v5"
admin_username      = "adminuser"
admin_password      = var.admin_password
}

resource "azurerm_sql_database" "db" {
name                = "hybrid-db"
resource_group_name = "hybrid-rg"
location            = "eastus"
server_name         = azurerm_sql_server.main.name
}

6. Diferenciais
6.1 Plano de Disaster Recovery

    RPO/RTO: Objetivo de RPO de 15 minutos e RTO de 1 hora.
    Geo-Replication: Azure SQL Database com réplica em outra região.
    Backups: Backups automáticos de VMs e SQL Database, armazenados em Blob Storage com retenção de 30 dias.
    Failover: Scripts Terraform para promover réplica SQL como primária.
    Testes: Simulações trimestrais de failover.

6.2 Monitoramento

    Azure Monitor: Métricas de CPU, memória e latência, com alertas para >80% de uso.
    Prometheus + Grafana: Implantado on-premises para monitoramento detalhado de VMs.
    Logs: Centralização via Azure Log Analytics e ELK Stack local.

7. Conclusão

A solução híbrida com Azure equilibra desempenho, custo e resiliência, utilizando a nuvem para escalabilidade e on-premises para baixa latência. Ferramentas como Terraform e Ansible garantem automação, enquanto FinOps e monitoramento otimizam recursos. Diferenciais como DR e modelo OSI agregam robustez, atendendo aos requisitos do desafio.
