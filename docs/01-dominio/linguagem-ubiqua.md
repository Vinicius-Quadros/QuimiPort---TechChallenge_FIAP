# Linguagem Ubíqua — QuimiPort

Este glossário define os termos do domínio do QuimiPort. Todos os membros do
grupo devem usar esses termos de forma consistente na documentação, no
código e nas conversas sobre o projeto.

| Termo | Definição |
|---|---|
| **Produto Químico** | Substância química cadastrada no sistema, com nome e Classificação de Risco. Pode estar ativo ou inativo. Só produtos ativos podem ser associados a novas cargas. |
| **Carga Química** | Registro de uma movimentação de determinada quantidade de um produto químico. É o agregado central do sistema: concentra produto, quantidade, documentação, Responsável Técnico, status, inspeção, liberação/bloqueio e histórico. |
| **Classificação de Risco** | Categoria que indica o nível de periculosidade (ex.: inflamável, corrosivo, tóxico). É um atributo obrigatório tanto do Produto Químico quanto da Carga Química associada. |
| **Responsável Técnico** | Pessoa habilitada, vinculada obrigatoriamente a toda Carga Química, responsável por validar tecnicamente a operação. |
| **Documento da Carga** | Documentação obrigatória anexada à carga (ex.: ficha de segurança, laudo). Sem a documentação obrigatória completa, a carga não pode ser liberada. |
| **Inspeção** | Verificação técnica realizada sobre a carga antes de uma liberação. Uma carga em inspeção não pode ser finalizada sem antes passar por liberação. |
| **Área de Armazenamento** | Local físico onde a carga fica armazenada antes/durante a movimentação portuária. |
| **Status da Carga** | Estado atual da carga dentro do fluxo (ex.: Registrada, Em Inspeção, Liberada, Bloqueada, Cancelada). Define quais ações são permitidas em cada momento. |
| **Liberar** | Ação que autoriza a carga a seguir para movimentação portuária. Só é permitida se documentação, Classificação de Risco, Responsável Técnico e demais regras estiverem satisfeitos. |
| **Bloquear** | Ação que impede a carga de entrar em movimentação. Uma carga bloqueada não pode ser movimentada até ser desbloqueada ou liberada. |
| **Cancelar** | Ação que encerra definitivamente uma carga. Uma carga cancelada não pode mais ser liberada. |
| **Movimentação Portuária** | Operação física de deslocar a carga dentro do porto. Só pode ocorrer com cargas liberadas (nunca bloqueadas ou canceladas). |
| **Operador Portuário** | Usuário responsável por registrar e movimentar as cargas. |
| **Analista de Documentação** | Usuário responsável por validar e anexar a documentação obrigatória. |
| **Analista de Qualidade** | Usuário responsável por acompanhar as regras de segurança e qualidade do processo. |
| **Gestor Operacional** | Usuário que acompanha o status geral das cargas e toma decisões operacionais. |
| **Administrador do Sistema** | Usuário responsável pela gestão de cadastros e configurações gerais do sistema. |

> **Nota:** os termos "Classe de Risco" (produto) e "Classificação de Risco"
> (carga), citados separadamente no enunciado do desafio, foram unificados
> aqui em um único conceito — **Classificação de Risco** — por representarem
> a mesma ideia de nível de periculosidade, aplicada tanto ao produto quanto
> à carga.
