
# Desafio de Migração para Modelo Híbrido

Este repositório apresenta uma solução arquitetural para migrar um sistema legado para um modelo híbrido utilizando Azure, com foco em dimensionamento, FinOps, automação via IaC e resiliência.

## Estrutura do Repositório
- **docs/**: Documentação detalhada da arquitetura, topologia e estratégias de FinOps.
- **iac/**: Scripts Terraform e Ansible para automação da infraestrutura.
- **README.md**: Visão geral do projeto.

## Como Navegar
1. Leia `docs/architecture.md` para entender a solução completa.
2. Visualize o diagrama em `docs/topology_diagram.png`.
3. Explore o código IaC em `iac/terraform/` e `iac/ansible/`.

## Pré-requisitos
- Terraform v1.5+
- Ansible v2.9+
- Azure CLI
- Conta Azure com permissões para criar recursos

## Como Executar
1. Clone o repositório:
   ```bash
   git clone https://github.com/SEU_USUARIO/hybrid-migration-challenge.git
   ```
2. Configure as credenciais Azure no Terraform.
3. Execute o Terraform:
   ```bash
   cd iac/terraform
   terraform init
   terraform apply
   ```
4. Aplique o playbook Ansible:
   ```bash
   cd iac/ansible
   ansible-playbook deploy.yml
   ```

## Contato
Para dúvidas, entre em contato via [seu.email@exemplo.com](mailto:seu.email@exemplo.com).
