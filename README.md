Monitoramento e Resposta a Eventos Críticos na Azure

Este repositório foi criado para demonstrar como manter visibilidade, controle e resposta proativa sobre eventos críticos em um ambiente de nuvem, com foco na exclusão de máquinas virtuais (VMs) na Azure. O objetivo é fornecer um guia prático com resumos, anotações e dicas para estudo e implementação, ajudando a gerenciar ambientes de nuvem de forma eficiente e segura.
Objetivo
O desafio proposto é criar um material de apoio que:

Explique como monitorar eventos críticos, como a exclusão de uma VM.
Demonstre como configurar alertas e respostas automatizadas usando ferramentas da Azure.
Forneça boas práticas para garantir governança e segurança no ambiente de nuvem.
Sirva como referência para estudos e futuras implementações.

Contexto: Por que monitorar a exclusão de VMs?
A exclusão acidental ou não autorizada de uma VM pode causar interrupções significativas, perda de dados ou custos inesperados. Para mitigar esses riscos, é essencial:

Visibilidade: Identificar quem realizou a ação, quando e em qual recurso.
Controle: Implementar políticas de governança, como permissões baseadas em RBAC (Role-Based Access Control).
Resposta Proativa: Configurar alertas e automações para notificar ou reagir rapidamente a eventos críticos.

Passo a Passo: Monitoramento e Resposta na Azure
1. Habilitar o Azure Activity Log
O Azure Activity Log registra todas as operações administrativas realizadas em recursos da Azure, incluindo a exclusão de VMs.

Como acessar:
No portal Azure, vá para Monitor > Activity Log.
Filtre por Operação (ex.: "Delete Virtual Machine") ou Recurso.


Dica: Exporte os logs para um Log Analytics Workspace para análises avançadas usando KQL (Kusto Query Language).

Exemplo de consulta KQL para detectar exclusões de VMs:
AzureActivity
| where OperationNameValue == "MICROSOFT.COMPUTE/VIRTUALMACHINES/DELETE"
| project TimeGenerated, Caller, ResourceGroup, Resource

2. Configurar Alertas com Azure Monitor
O Azure Monitor permite criar alertas baseados em eventos do Activity Log.

Passos:
No portal, vá para Monitor > Alerts > Create > Alert Rule.
Escolha o Activity Log como fonte de dados.
Configure a condição para disparar quando a operação for "Delete Virtual Machine".
Crie um Action Group para enviar notificações (e-mail, SMS) ou acionar uma automação (ex.: Azure Function, Logic App).


Dica: Use um grupo de ação com múltiplos canais (e-mail + webhook) para garantir que a equipe seja notificada rapidamente.

Exemplo de script PowerShell para criar um alerta (veja o arquivo configure-alert.ps1 neste repositório):
# Configurar um alerta para exclusão de VMs
$actionGroupId = "/subscriptions/<sua-subscription-id>/resourceGroups/<resource-group>/providers/microsoft.insights/actionGroups/<action-group-name>"
$condition = @{
    "allOf" = @(
        @{
            "field" = "operationName"
            "equals" = "Microsoft.Compute/virtualMachines/delete"
        }
    )
}
New-AzActivityLogAlert -ResourceGroupName "<resource-group>" -Name "VMDeletionAlert" -Location "global" -Scope "/subscriptions/<sua-subscription-id>" -Condition $condition -ActionGroupId $actionGroupId

3. Automatizar Respostas com Azure Automation
Para uma resposta proativa, use Azure Automation ou Logic Apps para executar ações automáticas quando uma VM for excluída.

Exemplo de automação:
Criar um Logic App que, ao detectar a exclusão de uma VM, envie uma notificação para um canal do Microsoft Teams e registre o evento em um banco de dados.
Usar um runbook do Azure Automation para restaurar uma VM a partir de um backup (se disponível).


Dica: Sempre teste automações em um ambiente de desenvolvimento antes de aplicá-las em produção.

4. Boas Práticas para Governança

RBAC: Restrinja permissões de exclusão de VMs a usuários específicos usando papéis personalizados.
Exemplo: Crie um papel que permita apenas "Read" e "Write" em VMs, sem permissão para "Delete".


Azure Policy: Implemente políticas para exigir aprovação ou registrar exclusões.
Exemplo: Use uma política para bloquear exclusões de VMs sem uma tag específica.


Backups: Configure o Azure Backup para VMs críticas, garantindo recuperação rápida.
Monitoramento Contínuo: Revise regularmente os logs e ajuste alertas com base em novos padrões de uso.

Estrutura do Repositório

README.md: Este documento, com resumos e guias.
configure-alert.ps1: Script PowerShell para configurar alertas de exclusão de VMs.
kql-queries/: Exemplos de consultas KQL para análise de logs.
policies/: Exemplos de políticas do Azure Policy para governança.
docs/: Anotações adicionais sobre Azure Monitor, Automation e RBAC.

Dicas para Estudos

Certificações: Explore a certificação AZ-104: Microsoft Azure Administrator para aprofundar conhecimentos em gerenciamento de Azure.
Documentação Oficial: Consulte a documentação do Azure Monitor e Activity Log.
Comunidade: Participe de fóruns como o Microsoft Q&A para trocar experiências.
Prática: Crie um ambiente de teste na Azure (use a conta gratuita) para experimentar configurações de monitoramento.

Próximos Passos

Teste o script configure-alert.ps1 em seu ambiente Azure.
Adicione suas próprias consultas KQL na pasta kql-queries/.
Contribua com este repositório enviando pull requests com novas dicas ou exemplos!


Nota: Este repositório é um material de apoio e não substitui a documentação oficial da Microsoft. Sempre valide configurações em um ambiente de teste antes de aplicá-las em produção.
