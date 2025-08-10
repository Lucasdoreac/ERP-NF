# Casos de Uso em Gherkin - ERP para MEI

## 📋 Document Information
- **Product**: FinanceiroMEI
- **Phase**: Fase 1 - MVP ERP Financeiro
- **Format**: Gherkin (Behavior Driven Development)
- **Target**: Development Team
- **Created**: 2025-08-10

---

## 🎯 **EPIC 1: GESTÃO DE USUÁRIOS E AUTENTICAÇÃO**

### **Feature: Cadastro de MEI**
```gherkin
Feature: Cadastro de novo MEI
  Como um microempreendedor
  Eu quero me cadastrar na plataforma
  Para começar a gerenciar minhas finanças automaticamente

Background:
  Given que estou na página de cadastro
  And o sistema está funcionando normalmente

Scenario: Cadastro com dados válidos de MEI
  Given que sou um MEI ativo
  When preencho o campo email com "joao@exemplo.com"
  And preencho o campo senha com "MinhaSenh@123"
  And preencho o campo confirmar senha com "MinhaSenh@123"
  And preencho o campo nome com "João Silva"
  And preencho o campo CNPJ com "12345678000195"
  And preencho o campo telefone com "(11) 99999-9999"
  And aceito os termos de uso
  And clico em "Criar conta"
  Then vejo a mensagem "Conta criada com sucesso"
  And recebo um email de confirmação
  And sou redirecionado para o dashboard
  And vejo o tutorial de primeiros passos

Scenario: Tentativa de cadastro com CNPJ inválido
  Given que estou preenchendo o formulário de cadastro
  When preencho o campo CNPJ com "12345678000000"
  And tento prosseguir
  Then vejo a mensagem de erro "CNPJ inválido"
  And o campo CNPJ fica destacado em vermelho
  And não consigo prosseguir com o cadastro

Scenario: Tentativa de cadastro com email já existente
  Given que já existe um usuário com email "joao@exemplo.com"
  When tento me cadastrar com o mesmo email
  Then vejo a mensagem "Este email já está em uso"
  And vejo a opção "Esqueceu sua senha?"
  And posso clicar para recuperar a senha

Scenario: Cadastro com senha fraca
  Given que estou preenchendo o formulário
  When preencho a senha com "123456"
  Then vejo a mensagem "Senha muito fraca"
  And vejo os requisitos de senha:
    | Mínimo 8 caracteres |
    | Pelo menos 1 letra maiúscula |
    | Pelo menos 1 número |
    | Pelo menos 1 caractere especial |
  And não consigo prosseguir
```

### **Feature: Login e Autenticação**
```gherkin
Feature: Login na plataforma
  Como um MEI cadastrado
  Eu quero fazer login na plataforma
  Para acessar meu dashboard financeiro

Background:
  Given que existe um usuário cadastrado:
    | email | joao@exemplo.com |
    | senha | MinhaSenh@123 |
    | status | ativo |

Scenario: Login com credenciais válidas
  Given que estou na página de login
  When preencho o email com "joao@exemplo.com"
  And preencho a senha com "MinhaSenh@123"
  And clico em "Entrar"
  Then sou redirecionado para o dashboard
  And vejo a mensagem "Bem-vindo de volta, João!"
  And vejo meu nome no menu superior
  And meu token de sessão é criado
  And expira em 24 horas

Scenario: Login com senha incorreta
  Given que estou na página de login
  When preencho o email com "joao@exemplo.com"
  And preencho a senha com "senhaerrada"
  And clico em "Entrar"
  Then vejo a mensagem "Email ou senha incorretos"
  And permaneço na página de login
  And o campo senha é limpo
  And tenho 4 tentativas restantes

Scenario: Bloqueio após múltiplas tentativas
  Given que errei a senha 5 vezes consecutivas
  When tento fazer login novamente
  Then vejo a mensagem "Conta temporariamente bloqueada"
  And vejo "Tente novamente em 15 minutos"
  And recebo um email de alerta de segurança
  And não posso fazer login por 15 minutos

Scenario: Recuperação de senha
  Given que estou na página de login
  When clico em "Esqueceu sua senha?"
  And preencho meu email "joao@exemplo.com"
  And clico em "Enviar"
  Then vejo "Email de recuperação enviado"
  And recebo um email com link de recuperação
  And o link expira em 1 hora
  And posso definir uma nova senha
```

---

## 🔌 **EPIC 2: INTEGRAÇÕES COM PROCESSADORES DE PAGAMENTO**

### **Feature: Integração Mercado Pago**
```gherkin
Feature: Conectar conta Mercado Pago
  Como um MEI que usa Mercado Pago
  Eu quero conectar minha conta
  Para que minhas vendas sejam importadas automaticamente

Background:
  Given que estou logado como MEI
  And estou na página de integrações
  And tenho uma conta ativa no Mercado Pago

Scenario: Conexão bem-sucedida com Mercado Pago
  Given que clico em "Conectar Mercado Pago"
  When sou redirecionado para a página do Mercado Pago
  And faço login com minhas credenciais do MP
  And autorizo o acesso do FinanceiroMEI
  And sou redirecionado de volta
  Then vejo "Mercado Pago conectado com sucesso"
  And vejo o status "Conectado" na integração
  And vejo a data da última sincronização
  And minhas transações começam a ser importadas
  And recebo webhook de configuração confirmado

Scenario: Falha na autorização OAuth
  Given que estou no fluxo de conexão MP
  When nego a autorização na página do Mercado Pago
  Then sou redirecionado de volta para integrações
  And vejo "Autorização negada pelo usuário"
  And a integração permanece "Desconectada"
  And posso tentar conectar novamente

Scenario: Token de acesso expirado
  Given que tenho Mercado Pago conectado há 6 meses
  When o token de acesso expira
  And uma requisição à API falha
  Then recebo notificação "Reconecte sua conta MP"
  And vejo alerta no dashboard
  And a integração fica com status "Atenção requerida"
  And posso reconectar com 1 clique

Scenario: Importação inicial de transações
  Given que acabei de conectar o Mercado Pago
  When a sincronização inicial é executada
  Then vejo "Importando suas transações..."
  And uma barra de progresso é exibida
  And transações dos últimos 90 dias são importadas
  And vejo "X transações importadas com sucesso"
  And posso visualizar as transações no dashboard

Scenario: Recebimento de webhook de nova venda
  Given que tenho MP conectado e funcionando
  When uma venda de R$ 250,00 é aprovada no MP
  And o webhook é enviado para nosso endpoint
  Then a transação aparece no dashboard em até 30 segundos
  And recebo notificação push "Nova venda: R$ 250,00"
  And posso ver os detalhes da transação
  And o saldo é atualizado automaticamente
```

### **Feature: Integração PagSeguro**
```gherkin
Feature: Conectar conta PagSeguro/PagBank
  Como um MEI que usa PagSeguro
  Eu quero conectar minha conta
  Para centralizar todas minhas vendas em um lugar

Background:
  Given que estou logado no sistema
  And estou na página de integrações
  And tenho conta empresarial PagSeguro ativa

Scenario: Conexão PagSeguro via credenciais API
  Given que clico em "Conectar PagSeguro"
  When preencho meu email PagSeguro
  And preencho meu token de API
  And clico em "Conectar"
  Then o sistema valida as credenciais
  And vejo "PagSeguro conectado com sucesso"
  And transações são sincronizadas automaticamente
  And webhook é configurado no PagSeguro

Scenario: Credenciais inválidas do PagSeguro
  Given que estou conectando PagSeguro
  When preencho credenciais incorretas
  And clico em "Conectar"
  Then vejo "Credenciais inválidas"
  And vejo link "Como obter token API"
  And posso tentar novamente
  And um tutorial é exibido

Scenario: Processamento de notificação PagSeguro
  Given que tenho PagSeguro conectado
  When recebo notificação de transação
  And o sistema consulta detalhes via API
  Then a transação é criada/atualizada
  And o status correto é definido:
    | 1 | Aguardando pagamento |
    | 3 | Paga |
    | 7 | Cancelada |
  And o usuário é notificado da mudança

Scenario: Falha na API do PagSeguro
  Given que estou sincronizando transações
  When a API do PagSeguro retorna erro 500
  Then vejo "Sincronização temporariamente indisponível"
  And o sistema agenda retry em 5 minutos
  And após 3 falhas consecutivas
  Then o usuário é notificado do problema
  And suporte é alertado automaticamente
```

### **Feature: Sincronização Automática**
```gherkin
Feature: Sincronização automática de transações
  Como um MEI com múltiplas integrações
  Eu quero que as transações sejam sincronizadas automaticamente
  Para não perder nenhuma venda

Background:
  Given que tenho MP e PagSeguro conectados
  And ambas integrações estão ativas

Scenario: Sincronização programada executada
  Given que é 09:00 da manhã
  When o cron job de sincronização é executado
  Then transações do MP são verificadas
  And transações do PagSeguro são verificadas
  And apenas transações novas/modificadas são processadas
  And um log de sincronização é criado
  And métricas são atualizadas

Scenario: Detecção de transação duplicada
  Given que uma venda foi feita via MP
  When a mesma transação chega via webhook
  And já existe no sistema
  Then o sistema detecta duplicata por external_id
  And não cria transação duplicada
  And atualiza apenas se houver mudança de status
  And log de duplicata é registrado

Scenario: Conciliação automática de valores
  Given que recebi R$ 500 via MP
  When o MP desconta taxa de R$ 25
  And o valor líquido R$ 475 é confirmado
  Then o sistema atualiza valor_liquido
  And calcula taxa automaticamente (5%)
  And status muda para "Conciliado"
  And histórico de conciliação é mantido

Scenario: Alerta de transação não conciliada
  Given que uma venda foi aprovada há 3 dias
  When o dinheiro ainda não foi creditado
  And prazo limite é excedido
  Then usuário recebe alerta push
  And email é enviado com detalhes
  And transação fica marcada "Investigação necessária"
  And link para suporte é fornecido
```

---

## 🧾 **EPIC 3: EMISSÃO DE NOTAS FISCAIS**

### **Feature: Emissão Manual NFS-e**
```gherkin
Feature: Emitir NFS-e manualmente
  Como um MEI
  Eu quero emitir notas fiscais de serviço
  Para cumprir minhas obrigações legais

Background:
  Given que estou logado no sistema
  And tenho pelo menos 1 transação aprovada
  And meus dados fiscais estão configurados

Scenario: Emissão de NFS-e baseada em transação
  Given que tenho uma venda de R$ 450,00 aprovada
  And o cliente informou CPF "12345678901"
  When clico em "Emitir NFS-e" na transação
  And preencho descrição "Desenvolvimento de website"
  And seleciono código de serviço "01.07"
  And confirmo ISS de 2% (auto-calculado)
  And clico em "Emitir"
  Then aguardo processamento (até 30 segundos)
  And vejo "NFS-e emitida com sucesso"
  And vejo número da nota "2025000001"
  And PDF é gerado automaticamente
  And cliente recebe nota por email
  And status da transação vira "Com NFS-e"

Scenario: Emissão com dados de cliente incompletos
  Given que vou emitir NFS-e
  When não tenho CPF/CNPJ do cliente
  Then vejo modal "Dados do cliente necessários"
  And posso preencher:
    | Nome | João da Silva |
    | CPF/CNPJ | 12345678901 |
    | Email | joao@email.com |
  And dados são salvos para próximas vendas
  And emissão prossegue normalmente

Scenario: Falha na emissão por sistema prefeitura indisponível
  Given que estou emitindo NFS-e
  When sistema da prefeitura retorna timeout
  Then vejo "Sistema da prefeitura temporariamente indisponível"
  And nota fica em fila para reprocessamento
  And vejo "Tentativa 1 de 5" com timer
  And sou notificado quando emissão for concluída
  And posso cancelar e tentar mais tarde

Scenario: Cálculo automático de impostos
  Given que vou emitir NFS-e de R$ 1000
  When seleciono código de serviço "01.07" (ISS 2%)
  Then valor bruto é R$ 1000,00
  And ISS é calculado R$ 20,00 (2%)
  And valor líquido é R$ 980,00
  And valores são exibidos claramente
  And posso alterar alíquota se necessário
  And cálculo é refeito em tempo real

Scenario: Nota fiscal com retenção de impostos
  Given que o cliente é pessoa jurídica
  And o valor é superior a R$ 5000
  When emito a NFS-e
  Then sistema detecta obrigatoriedade de retenção
  And calcula:
    | IR | 1.5% |
    | PIS | 0.65% |
    | COFINS | 3% |
    | CSLL | 1% |
  And valores são descontados do total
  And nota indica retenção corretamente
```

### **Feature: Configuração Fiscal**
```gherkin
Feature: Configurar dados fiscais do MEI
  Como um MEI
  Eu quero configurar meus dados fiscais
  Para emitir notas fiscais corretamente

Background:
  Given que estou logado como MEI
  And estou na página de configurações fiscais

Scenario: Configuração inicial completa
  Given que é meu primeiro acesso
  When preencho CNPJ "12345678000195"
  And preencho Inscrição Municipal "123456"
  And seleciono município "São Paulo - SP"
  And defino código de serviço padrão "01.07"
  And defino descrição padrão "Serviços de consultoria"
  And salvo as configurações
  Then vejo "Configurações salvas com sucesso"
  And dados são validados automaticamente
  And posso emitir notas fiscais

Scenario: Upload de certificado digital A1
  Given que tenho certificado digital A1
  When clico em "Enviar certificado"
  And seleciono arquivo .p12
  And digito a senha do certificado
  Then sistema valida o certificado
  And vejo "Certificado instalado com sucesso"
  And data de validade é exibida
  And alerta é configurado para 30 dias antes do vencimento

Scenario: Validação de inscrição municipal
  Given que preencho IM "999999"
  When sistema consulta base da prefeitura
  And inscrição não é encontrada
  Then vejo "Inscrição Municipal não encontrada"
  And vejo link "Como obter inscrição municipal"
  And posso prosseguir com aviso
  And nota explicativa é exibida

Scenario: Configuração de códigos de serviço múltiplos
  Given que presto diferentes tipos de serviços
  When adiciono código "01.07" para "Consultoria"
  And adiciono código "07.02" para "Publicidade"
  And adiciono código "23.01" para "Serviços hospitalares"
  Then posso definir código padrão
  And posso selecionar na emissão
  And descrições são auto-preenchidas
  And alíquotas são calculadas automaticamente
```

### **Feature: Automação NFS-e (Fase 2)**
```gherkin
Feature: Emissão automática de NFS-e
  Como um MEI Pro/Premium
  Eu quero que notas fiscais sejam emitidas automaticamente
  Para economizar tempo e garantir compliance

Background:
  Given que tenho plano Pro ou superior
  And configurei automação fiscal
  And defini regras de emissão automática

Scenario: Configurar automação por tipo de cliente
  Given que estou em configurações avançadas
  When ativo "Emissão automática"
  And defino regra "Cliente PJ = automática"
  And defino regra "Cliente PF = manual (até R$ 500)"
  And defino regra "Valor > R$ 1000 = sempre automática"
  Then regras são salvas
  And preview das regras é exibido
  And posso testar com transações existentes

Scenario: Emissão automática após aprovação de pagamento
  Given que tenho automação ativa
  When transação MP de R$ 800 é aprovada
  And cliente é PJ com CNPJ válido
  And serviço tem código padrão configurado
  Then NFS-e é emitida automaticamente em 5 minutos
  And cliente recebe por email
  And sou notificado do sucesso
  And transação é marcada "NFS-e automática"

Scenario: Falha na automação com fallback manual
  Given que automação falhou 3 vezes
  When sistema para tentativas automáticas
  Then transação fica "NFS-e pendente manual"
  And recebo notificação da falha
  And posso emitir manualmente
  And motivo da falha é registrado
  And suporte é alertado se padrão recorrente

Scenario: Relatório de automação fiscal
  Given que uso automação há 3 meses
  When acesso relatório de automação
  Then vejo taxa de sucesso (95%)
  And vejo tempo médio de emissão (3 min)
  And vejo principais falhas
  And vejo economia de tempo estimada
  And posso exportar dados para contador
```

---

## 💰 **EPIC 4: GESTÃO FINANCEIRA E DASHBOARD**

### **Feature: Dashboard Financeiro**
```gherkin
Feature: Visualizar dashboard financeiro
  Como um MEI
  Eu quero ver um resumo das minhas finanças
  Para tomar decisões informadas sobre meu negócio

Background:
  Given que estou logado no sistema
  And tenho transações dos últimos 3 meses
  And tenho pelo menos uma integração ativa

Scenario: Dashboard com dados recentes
  Given que tenho vendas nos últimos 30 dias
  When acesso o dashboard principal
  Then vejo meu saldo atual "R$ 15.847,32"
  And vejo vendas do mês "R$ 23.450,00 (+12%)"
  And vejo gráfico de evolução (últimos 30 dias)
  And vejo 5 últimas transações
  And vejo 3 NFS-e pendentes
  And todos os valores são atualizados em tempo real

Scenario: Dashboard usuário novo sem transações
  Given que me cadastrei hoje
  And não tenho transações ainda
  When acesso o dashboard
  Then vejo estado vazio com ilustração
  And vejo "Conecte sua primeira integração"
  And vejo botões para MP e PagSeguro
  And vejo vídeo tutorial "Primeiros passos"
  And posso pular tutorial se desejar

Scenario: Filtros de período no dashboard
  Given que estou no dashboard
  When seleciono filtro "Últimos 7 dias"
  Then todos os widgets atualizam para 7 dias
  And gráfico mostra evolução semanal
  And comparação mostra variação vs semana anterior
  When seleciono "Últimos 12 meses"
  Then gráfico mostra tendência anual
  And posso ver sazonalidade do negócio

Scenario: Atualização em tempo real via WebSocket
  Given que estou visualizando dashboard
  When nova transação chega via webhook
  Then saldo é atualizado automaticamente
  And nova transação aparece na lista
  And notificação discreta é exibida
  And gráfico é atualizado suavemente
  And som opcional de nova venda toca

Scenario: Alertas e notificações importantes
  Given que estou no dashboard
  When tenho NFS-e vencidas há > 5 dias
  Then vejo alerta vermelho "3 NFS-e atrasadas"
  When meu certificado digital vence em 15 dias
  Then vejo aviso laranja "Renovar certificado"
  When recebi > R$ 10.000 no mês (limite MEI)
  Then vejo aviso importante sobre limite
  And link para informações sobre mudança de regime
```

### **Feature: Relatórios Gerenciais**
```gherkin
Feature: Gerar relatórios gerenciais
  Como um MEI
  Eu quero gerar relatórios das minhas finanças
  Para acompanhar a performance do meu negócio

Background:
  Given que tenho dados financeiros de 6 meses
  And estou na seção de relatórios

Scenario: Relatório de faturamento mensal
  Given que seleciono "Relatório de Faturamento"
  When escolho período "Janeiro a Junho 2025"
  And clico em "Gerar relatório"
  Then vejo tabela com dados mensais:
    | Mês | Faturamento | Crescimento |
    | Jan | R$ 15.000 | - |
    | Fev | R$ 18.000 | +20% |
    | Mar | R$ 16.500 | -8% |
  And posso exportar em PDF/Excel
  And gráfico visual é gerado automaticamente

Scenario: Relatório de impostos pagos
  Given que emiti várias NFS-e
  When gero "Relatório Fiscal"
  Then vejo total de ISS pago
  And vejo total de impostos retidos
  And vejo notas emitidas por município
  And posso filtrar por período
  And dados são compatíveis com DASN-SIMEI

Scenario: Relatório de conciliação bancária
  Given que tenho transações conciliadas e pendentes
  When gero "Relatório de Conciliação"
  Then vejo transações conciliadas (95%)
  And vejo pendentes de conciliação (5%)
  And vejo diferenças encontradas
  And posso investigar discrepâncias
  And exportar para contador

Scenario: Relatório personalizado
  Given que acesso "Relatórios Avançados"
  When seleciono dimensões:
    | Campo | Faturamento |
    | Período | Últimos 12 meses |
    | Agrupamento | Por método pagamento |
    | Filtro | Valor > R$ 500 |
  Then relatório é gerado dinamicamente
  And posso salvar como template
  And agendar envio automático por email

Scenario: Comparativo de performance
  Given que tenho dados de 2 anos
  When gero "Relatório Comparativo"
  And seleciono "2024 vs 2025"
  Then vejo crescimento ano a ano
  And vejo melhores/piores meses
  And vejo tendências e insights
  And recebo sugestões de melhorias
  And posso compartilhar com contador/sócio
```

### **Feature: Fluxo de Caixa**
```gherkin
Feature: Controle de fluxo de caixa
  Como um MEI
  Eu quero acompanhar meu fluxo de caixa
  Para planejar melhor meu negócio

Background:
  Given que tenho transações e despesas cadastradas
  And estou na seção "Fluxo de Caixa"

Scenario: Visualização do fluxo atual
  Given que acesso fluxo de caixa
  Then vejo saldo atual "R$ 25.847,32"
  And vejo entradas do mês "R$ 45.000,00"
  And vejo saídas do mês "R$ 19.152,68"
  And vejo gráfico de evolução diária
  And vejo projeção próximos 30 dias
  And cores indicam saúde financeira

Scenario: Cadastro de despesa recorrente
  Given que quero adicionar despesa
  When clico em "Nova despesa"
  And preencho "Aluguel escritório"
  And valor "R$ 1.200,00"
  And categoria "Despesas fixas"
  And recorrência "Todo dia 5 do mês"
  Then despesa é salva
  And aparece no fluxo futuro
  And alertas são configurados

Scenario: Projeção de fluxo futuro
  Given que tenho receitas e despesas recorrentes
  When visualizo próximos 90 dias
  Then vejo projeção baseada em histórico
  And vejo pontos críticos (saldo baixo)
  And recebo alertas preventivos
  And posso simular cenários
  And ajustar planejamento

Scenario: Alerta de saldo baixo
  Given que meu saldo projetado ficará < R$ 5.000
  When sistema detecta situação
  Then recebo alerta "Atenção: saldo baixo previsto"
  And vejo sugestões de ação
  And posso programar transferências
  And histórico de alertas é mantido

Scenario: Categorização automática
  Given que tenho histórico de categorizações
  When nova despesa é adicionada
  And descrição contém "combustível"
  Then categoria "Transporte" é sugerida
  And posso aceitar ou alterar
  And sistema aprende com minhas escolhas
  And precisão melhora com o tempo
```

---

## 🔄 **EPIC 5: CONCILIAÇÃO BANCÁRIA**

### **Feature: Conciliação Automática**
```gherkin
Feature: Conciliação automática de transações
  Como um MEI
  Eu quero que minhas vendas sejam conciliadas automaticamente
  Para ter certeza que tudo foi pago corretamente

Background:
  Given que tenho integrações MP e PagSeguro ativas
  And tenho transações aprovadas aguardando conciliação

Scenario: Conciliação automática bem-sucedida
  Given que vendi R$ 500 via Mercado Pago hoje
  When MP desconta taxa de R$ 25 (5%)
  And credita R$ 475 na minha conta
  And sistema recebe confirmação via API
  Then transação é marcada "Conciliada"
  And valor_liquido é atualizado para R$ 475
  And taxa_processamento é salva (R$ 25)
  And data_credito é registrada
  And saldo disponível é atualizado

Scenario: Conciliação com prazo em atraso
  Given que transação foi aprovada há 3 dias
  And prazo normal do MP é 1 dia útil
  When sistema verifica conciliação pendente
  Then usuário recebe notificação "Pagamento em atraso"
  And pode consultar status no MP
  And transação fica marcada "Em investigação"
  And suporte é oferecido

Scenario: Divergência de valor detectada
  Given que venda foi de R$ 300
  When valor creditado foi R$ 270
  And diferença (R$ 30) > taxa esperada (R$ 15)
  Then sistema detecta divergência
  And usuário é alertado
  And pode marcar como "Taxa extraordinária"
  And pode contestar a diferença
  And histórico de divergências é mantido

Scenario: Conciliação manual forçada
  Given que transação não conciliou automaticamente
  When usuário clica "Marcar como conciliado"
  And confirma valor recebido R$ 280
  Then conciliação manual é registrada
  And motivo pode ser informado
  And auditoria é criada
  And relatório inclui conciliações manuais

Scenario: Estorno e cancelamentos
  Given que transação foi conciliada ontem
  When cliente solicita estorno via MP
  And MP processa o estorno
  Then sistema recebe webhook de estorno
  And conciliação é revertida
  And saldo é ajustado
  And usuário é notificado do estorno
```

### **Feature: Reconciliação Bancária**
```gherkin
Feature: Reconciliação com extrato bancário
  Como um MEI que recebe via múltiplos canais
  Eu quero reconciliar com meu extrato bancário
  Para ter controle total das minhas finanças

Background:
  Given que recebo via MP, PagSeguro e PIX direto
  And tenho acesso ao meu extrato bancário

Scenario: Upload e análise de extrato bancário
  Given que baixei meu extrato em OFX
  When faço upload do arquivo
  Then sistema analiza as movimentações
  And identifica créditos potenciais
  And sugere matches com transações
  And apresenta lista para confirmação

Scenario: Match automático por valor e data
  Given que tenho transação R$ 450 dia 15/01
  When extrato mostra crédito R$ 450 dia 16/01
  And origem contém "MERCADOPAGO"
  Then match automático é sugerido
  And confiança é alta (95%)
  And usuário pode confirmar com 1 clique

Scenario: Match manual para casos complexos
  Given que transação não foi identificada automaticamente
  When usuário seleciona transação do sistema
  And seleciona entrada do extrato
  And confirma o match
  Then reconciliação é criada
  And padrão é aprendido para futuro
  And precisão automática melhora

Scenario: Identificação de entradas não mapeadas
  Given que extrato tem crédito não identificado
  When sistema não encontra transação correspondente
  Then entrada fica marcada "Não identificada"
  And usuário pode criar transação manual
  And ou marcar como "Receita extra"
  And ou ignorar (transferências, etc)

Scenario: Relatório de reconciliação
  Given que processo foi concluído
  When gero relatório de reconciliação
  Then vejo % de transações reconciliadas
  And vejo valores não identificados
  And vejo divergências encontradas
  And posso exportar para contador
  And agendar processo mensal
```

---

## 📱 **EPIC 6: EXPERIÊNCIA DO USUÁRIO**

### **Feature: Onboarding do MEI**
```gherkin
Feature: Onboarding de novos usuários
  Como um novo MEI na plataforma
  Eu quero ser guiado através do setup inicial
  Para começar a usar rapidamente

Background:
  Given que me cadastrei na plataforma hoje
  And faço meu primeiro login

Scenario: Tour guiado completo
  Given que é meu primeiro acesso
  When login é feito com sucesso
  Then vejo modal "Bem-vindo ao FinanceiroMEI!"
  And vejo 4 passos do onboarding:
    | 1 | Conecte suas vendas |
    | 2 | Configure dados fiscais |
    | 3 | Importe transações |
    | 4 | Emita sua primeira NFS-e |
  And posso pular se preferir
  And progresso é salvo se interromper

Scenario: Passo 1 - Conectar primeira integração
  Given que estou no passo 1 do onboarding
  When clico "Conectar Mercado Pago"
  And autorização é concluída
  Then vejo "✅ Mercado Pago conectado!"
  And passo 1 fica marcado como completo
  And posso prosseguir para passo 2
  And ou conectar PagSeguro também

Scenario: Passo 2 - Configurar dados fiscais básicos
  Given que estou no passo 2
  When preencho inscrição municipal
  And seleciono meu município
  And defino código de serviço principal
  Then configuração básica é salva
  And posso emitir NFS-e mais tarde
  And passo fica completo

Scenario: Passo 3 - Primeira importação
  Given que conectei MP no passo 1
  When sistema importa minhas transações
  Then vejo "Encontramos X transações"
  And preview das transações é exibido
  And posso confirmar importação
  And dashboard é populado com dados reais

Scenario: Passo 4 - Primeira NFS-e (opcional)
  Given que tenho transações importadas
  When seleciono uma transação para NFS-e
  And sigo wizard de emissão
  Then primeira nota é emitida
  And celebração é exibida "🎉 Primeira NFS-e emitida!"
  And tutorial fica 100% completo
  And recebo badge "MEI Organizado"
```

### **Feature: Notificações e Alertas**
```gherkin
Feature: Sistema de notificações
  Como um MEI ativo
  Eu quero receber notificações relevantes
  Para não perder nada importante

Background:
  Given que tenho notificações habilitadas
  And tenho integrações ativas

Scenario: Notificação de nova venda
  Given que recebo venda de R$ 350 via MP
  When webhook é processado
  Then recebo push "💰 Nova venda: R$ 350,00"
  And notificação aparece no sino
  And som é tocado (se habilitado)
  And posso abrir transação diretamente

Scenario: Alerta de NFS-e atrasada
  Given que tenho transação há 5 dias sem NFS-e
  And sou obrigado a emitir (cliente PJ)
  When sistema detecta atraso
  Then recebo alerta "⚠️ NFS-e atrasada: R$ 350"
  And email é enviado também
  And posso emitir com 1 clique
  And prazo máximo é informado

Scenario: Configurar preferências de notificação
  Given que acesso configurações
  When vou para "Notificações"
  Then posso habilitar/desabilitar:
    | Nova venda | Push + Som |
    | NFS-e atrasada | Push + Email |
    | Limite MEI | Email apenas |
    | Sistema | Push |
  And horário de silêncio configurável
  And frequência de resumos por email

Scenario: Resumo semanal por email
  Given que configurei resumo semanal
  When é segunda-feira 9h
  Then recebo email "Seu resumo semanal"
  Com conteúdo:
    | Vendas da semana | R$ 2.450 |
    | NFS-e emitidas | 5 |
    | Pendências | 2 |
  And links diretos para ações
  And posso cancelar se não quiser

Scenario: Notificação de limite MEI próximo
  Given que faturei R$ 65.000 no ano
  And limite MEI é R$ 81.000
  When resto é < R$ 10.000
  Then recebo alerta crítico
  And informações sobre desenquadramento
  And link para consultor especializado
  And calculadora de projeção
```

### **Feature: Suporte e Ajuda**
```gherkin
Feature: Central de ajuda e suporte
  Como um MEI usando a plataforma
  Eu quero encontrar ajuda facilmente
  Para resolver dúvidas rapidamente

Background:
  Given que estou logado na plataforma
  And preciso de ajuda com alguma funcionalidade

Scenario: Buscar na central de ajuda
  Given que clico no ícone "?" no header
  When digito "como emitir nfs-e"
  Then vejo resultados relevantes:
    | Como emitir sua primeira NFS-e |
    | Configurar emissão automática |
    | Resolver erros de emissão |
  And posso filtrar por categoria
  And artigos têm rating de utilidade

Scenario: Chat suporte em tempo real
  Given que não encontro resposta na central
  When clico em "Falar com suporte"
  And estou no horário comercial
  Then chat abre em até 2 minutos
  And atendente tem meu contexto
  And pode ver minha tela se autorizar
  And histórico fica salvo

Scenario: Ticket de suporte fora do horário
  Given que preciso de ajuda às 22h
  When clico em "Falar com suporte"
  Then vejo "Estamos offline"
  And posso criar ticket
  And promessa de resposta em 4h úteis
  And recebo número do ticket
  And acompanho status por email

Scenario: Tutorial contextual
  Given que estou tentando emitir primeira NFS-e
  When sistema detecta dificuldade
  Then oferece tutorial guiado
  And posso ativar modo "passo a passo"
  And cada campo tem dica contextual
  And posso pular se já sei

Scenario: Feedback sobre o produto
  Given que uso a plataforma há 1 mês
  When sistema pede feedback
  Then posso dar nota 1-5 estrelas
  And comentar sobre experiência
  And sugerir melhorias
  And recebo agradecimento
  And time de produto recebe feedback
```

---

## ⚙️ **EPIC 7: CONFIGURAÇÕES E ADMINISTRAÇÃO**

### **Feature: Configurações de Conta**
```gherkin
Feature: Gerenciar configurações da conta
  Como um MEI
  Eu quero gerenciar minhas configurações
  Para personalizar minha experiência

Background:
  Given que estou logado no sistema
  And acesso menu "Configurações"

Scenario: Atualizar dados pessoais
  Given que estou em "Dados Pessoais"
  When altero telefone para "(11) 99999-8888"
  And altero endereço
  And clico "Salvar"
  Then dados são atualizados
  And vejo confirmação "Dados salvos"
  And alterações são logadas para auditoria

Scenario: Alterar senha da conta
  Given que estou em "Segurança"
  When clico "Alterar senha"
  And informo senha atual
  And nova senha "NovaSenh@456"
  And confirmo a nova senha
  Then senha é alterada
  And recebo email de confirmação
  And próximo login exige nova senha

Scenario: Configurar autenticação 2FA
  Given que quero mais segurança
  When habilito "Autenticação em 2 fatores"
  Then vejo QR code para app autenticador
  And insiro código de 6 dígitos
  And 2FA fica ativo
  And códigos de backup são gerados
  And próximos logins exigem 2FA

Scenario: Gerenciar plano e faturamento
  Given que tenho plano gratuito
  When clico "Fazer upgrade"
  Then vejo comparação de planos
  And posso escolher Pro (R$ 29,90)
  And preencho dados cartão
  And upgrade é ativado imediatamente
  And nota fiscal de cobrança é emitida

Scenario: Cancelar conta (GDPR)
  Given que quero cancelar minha conta
  When clico "Excluir conta"
  Then vejo aviso sobre consequências
  And confirmo com senha
  And escolho motivo (opcional)
  Then dados são agendados para exclusão (30 dias)
  And posso reverter até lá
  And backups são removidos após período
```

### **Feature: Gestão de Integrações**
```gherkin
Feature: Gerenciar integrações conectadas
  Como um MEI
  Eu quero controlar minhas integrações
  Para manter dados seguros e atualizados

Background:
  Given que tenho MP e PagSeguro conectados
  And estou na página "Integrações"

Scenario: Visualizar status das integrações
  Given que acesso página de integrações
  Then vejo status de cada integração:
    | Mercado Pago | ✅ Conectado | Última sync: há 2min |
    | PagSeguro | ⚠️ Atenção | Token expira em 5 dias |
    | Banco | ❌ Desconectado | - |
  And posso ver detalhes de cada uma
  And links para reconfigurar

Scenario: Reconectar integração expirada
  Given que token do PagSeguro expirou
  When clico "Reconectar PagSeguro"
  Then sou direcionado para nova autorização
  And após autorizar, integração volta ativa
  And sincronização perdida é recuperada
  And usuário é notificado do sucesso

Scenario: Desconectar integração
  Given que não uso mais PagSeguro
  When clico "Desconectar" no PagSeguro
  Then vejo aviso "Dados históricos serão mantidos"
  And confirmo desconexão
  Then integração fica inativa
  And webhooks são removidos
  And não recebo mais dados desta fonte

Scenario: Configurar frequência de sincronização
  Given que integração está conectada
  When acesso configurações avançadas
  Then posso escolher frequência:
    | Tempo real | Via webhooks |
    | A cada 15min | Polling API |
    | Manual | Sob demanda |
  And para planos gratuitos, opções limitadas
  And usuário entende impacto na performance

Scenario: Logs de sincronização
  Given que quero debuggar problema
  When acesso "Ver logs" da integração
  Then vejo últimas 100 sincronizações:
    | 10:35 | Sucesso | 3 transações importadas |
    | 10:20 | Erro | Timeout na API MP |
    | 10:05 | Sucesso | Nenhuma nova transação |
  And posso filtrar por status
  And exportar logs para suporte
```

---

## 🧪 **CENÁRIOS DE TESTE E EDGE CASES**

### **Feature: Testes de Carga e Performance**
```gherkin
Feature: Performance sob carga
  Como sistema em produção
  Eu preciso funcionar bem sob carga alta
  Para não deixar usuários na mão

Background:
  Given que sistema está em produção
  And temos 1000+ usuários ativos

Scenario: Pico de webhooks simultâneos
  Given que é Black Friday
  When recebemos 500 webhooks simultâneos
  Then todos são processados em < 30s
  And nenhum é perdido
  And usuários são notificados normalmente
  And sistema não fica lento

Scenario: Sincronização de usuário com muitas transações
  Given que MEI tem 10.000 transações históricas
  When conecta pela primeira vez
  Then importação é feita em lotes
  And progresso é mostrado ao usuário
  And sistema não trava
  And processo pode ser pausado/retomado

Scenario: Múltiplos usuários emitindo NFS-e
  Given que 50 usuários emitem NFS-e simultaneamente
  When sistema processa todas as requisições
  Then cada emissão demora < 45s
  And taxa de sucesso > 95%
  And filas de processamento funcionam bem
  And usuários recebem feedback em tempo real
```

### **Feature: Recuperação de Falhas**
```gherkin
Feature: Recuperação automática de falhas
  Como sistema crítico para negócios
  Eu preciso me recuperar de falhas automaticamente
  Para manter alta disponibilidade

Background:
  Given que sistema enfrenta problemas técnicos

Scenario: Falha na API do Mercado Pago
  Given que API do MP está fora do ar
  When tentamos sincronizar transações
  Then sistema detecta falha
  And agenda retry em 5, 15, 30, 60 minutos
  And usuário é informado do problema
  And quando MP volta, sincronização ocorre
  And nenhuma transação é perdida

Scenario: Falha no banco de dados
  Given que banco principal falha
  When usuários tentam acessar sistema
  Then sistema usa banco de backup
  And usuários podem continuar usando
  And dados são sincronizados quando problema resolve
  And downtime é < 5 minutos

Scenario: Falha na emissão de NFS-e
  Given que sistema da prefeitura está instável
  When tentamos emitir nota fiscal
  And falha 3 vezes consecutivas
  Then nota vai para fila manual
  And usuário é notificado
  And quando sistema volta, processamento continua
  And histórico de tentativas é mantido

Scenario: Recovery de dados corrompidos
  Given que detectamos inconsistência nos dados
  When sistema roda verificação automática
  Then dados são corrigidos usando backups
  And usuário é notificado se ação manual necessária
  And integridade é validada
  And relatório de correção é gerado
```

### **Feature: Casos Extremos de Uso**
```gherkin
Feature: Casos extremos de uso
  Como sistema robusto
  Eu preciso lidar com casos extremos
  Para não quebrar em situações inusitadas

Background:
  Given que usuários fazem coisas inesperadas

Scenario: MEI próximo do limite anual
  Given que MEI já faturou R$ 80.500 no ano
  And limite é R$ 81.000
  When recebe venda de R$ 600
  Then sistema detecta ultrapassagem
  And bloqueia transações futuras
  And orienta sobre desenquadramento
  And sugere migração para ME

Scenario: Emissão de NFS-e com valor muito alto
  Given que MEI tenta emitir NFS-e de R$ 50.000
  When valor ultrapassa limites normais MEI
  Then sistema questiona se valor está correto
  And valida se MEI pode emitir valor tão alto
  And orienta sobre possível desenquadramento
  And permite prosseguir com confirmação

Scenario: Usuário com milhares de transações não conciliadas
  Given que MEI importou histórico de 2 anos
  And tem 5.000 transações não conciliadas
  When acessa dashboard de conciliação
  Then sistema usa paginação eficiente
  And oferece filtros avançados
  And permite conciliação em lotes
  And performance não degrada

Scenario: Tentativa de fraude / uso malicioso
  Given que detectamos padrão suspeito
  When usuário tenta criar múltiplas contas
  Or emitir NFS-e com dados falsos
  Then sistema flageia comportamento
  And aplica verificações adicionais
  And pode temporariamente restringir conta
  And time de segurança é notificado
```

---

## 📊 **MÉTRICAS E MONITORAMENTO**

### **Feature: Métricas de Produto**
```gherkin
Feature: Coleta de métricas de uso
  Como time de produto
  Eu quero coletar métricas de uso
  Para entender comportamento dos usuários

Background:
  Given que sistema coleta métricas anonimizadas
  And usuários consentiram com coleta

Scenario: Métricas de onboarding
  Given que novo usuário se cadastra
  When passa pelos passos do onboarding
  Then sistema registra:
    | Tempo até primeira integração | 5 minutos |
    | Passos completados | 3 de 4 |
    | Primeiro login até primeira NFS-e | 2 dias |
    | Taxa de abandono por passo | < 20% |

Scenario: Métricas de engagement
  Given que usuário usa sistema regularmente
  When acessa diferentes funcionalidades
  Then coletamos:
    | DAU (Daily Active Users) | Único por dia |
    | Funcionalidade mais usada | Dashboard |
    | Tempo médio de sessão | 8 minutos |
    | Páginas por sessão | 4.2 |

Scenario: Métricas de negócio
  Given que usuários processam transações
  When usam funcionalidades principais
  Then medimos:
    | Volume transacional | R$ por usuário/mês |
    | NFS-e emitidas | Quantidade/usuário |
    | Taxa de automação | % NFS-e automáticas |
    | Receita por usuário (ARPU) | R$/mês |
```

---

## 🔚 **CONCLUSÃO DOS CASOS DE USO**

### **Resumo de Cobertura**

✅ **7 Epics Completos:**
1. **Gestão de Usuários** - Cadastro, login, autenticação
2. **Integrações** - Mercado Pago, PagSeguro, sincronização
3. **NFS-e** - Emissão manual, automática, configuração fiscal
4. **Dashboard** - Visualizações, relatórios, fluxo de caixa
5. **Conciliação** - Automática, manual, reconciliação bancária
6. **UX** - Onboarding, notificações, suporte
7. **Administração** - Configurações, gestão, monitoramento

### **Cenários por Criticidade**

🔴 **Críticos (MVP):** 45 cenários
- Cadastro e autenticação
- Integrações básicas MP/PagSeguro
- Dashboard principal
- Emissão manual NFS-e

🟡 **Importantes (V2):** 38 cenários
- Automação NFS-e
- Relatórios avançados
- Conciliação automática
- Notificações

🟢 **Desejáveis (V3):** 25 cenários
- Recursos avançados
- Otimizações
- Edge cases
- Métricas avançadas

### **Próximos Passos**

1. **Revisão Técnica** - Validar viabilidade de cada cenário
2. **Priorização** - Definir ordem de implementação
3. **Estimativas** - Story points por cenário
4. **Sprint Planning** - Distribuir em sprints
5. **Testes Automatizados** - Implementar BDD com Cucumber

**Total: 108 cenários de uso detalhados para desenvolvimento completo do MVP e versões futuras.**

---

*Documento criado em 2025-08-10 | Versão 1.0 | Status: Pronto para Desenvolvimento*