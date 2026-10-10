# Plano de Testes - Plataforma Fatec Zona Leste (Módulo Identidade)

### 1. Identificação e contexto

* **Sistema:** Plataforma Fatec Zona Leste (cinco aplicações e banco unificado).


* **Versão:** 1.0. 
* **Base:** Especificação de requisitos (Apêndice C).


* **Contexto:** Planejamento de testes focados na camada de identidade, sessão, tempo e *logout* global da plataforma.

* **Responsáveis:** Bruno Lira, Gabriel Andrew, Luiz Alvarenga, Rafael Melo.
* **Ultima atualização**: 09/10/2026

### 2. Itens de teste

* **Módulo:** Autenticador

**Itens:** Login automatizado, Login manual, Limitador de Tentativas, Redirecionamento pós-login, Emissão e validação do *cookie* de plataforma (assinatura Ed25519),
cadastro por convite, recuperação de senha, provedor OAuth2.1 (fluxo de autorização), assinatura da impersonação, exclusividade de chave privada.


* **Módulo:** Administrativo

**Itens:** Edição de pessoas e perfis, Edição de Cursos e Áreas, Convites, Leitura de Grades Curriculares, Importação de Turmas, 
Catálogo de aplicativos, Conferência de Vínculos (falha fechada), API Interna, Início da Impersonação, Revogação de acessos,
Gravação no domínio (apenas em admin_*).


* **Módulo:** TCC

**Itens:** Ciclo do TCC (transições de estado), Leitura do cadastro geral (admin_*), Gravação no Cadastro Geral via API Interna,
Recebimento de Revogação, Validação de Identidade (cookie com chave pública), Gravação no domínio (apenas em tcc_*) 

* **Módulo:** Aula

**Itens:** Criação de atividade, entrega de arquivos, reenvio, data limite, elegilibidade de entrega, entrega em grupo, painel do professor,
validação de identidade, gravação no domínio (apenas em aula_*).

* **Módulo:** Minha Conta

**Itens:** Gravação e Exibição de Dados Pessoais, Geração e Revogação de Chaves de Integração (MCP), Bloqueio de Dados Pessoais na impersonação,
gravação no domínio (apenas em admin_*).

* **Itens Transversais**: Esses itens dizem respeito a mais de um módulo específico, por isso foram adicionados em uma seção a parte

**Itens**: contrato de cookie da plataforma, logout global, contrato de API interna, latência em cada módulo.


### 3. Escopo

* **Dentro do Escopo:** Requisitos RF-01 a RF-04 (Identidade), comportamentos de tempo/relógio, autenticação transversal entre módulos e portão de redirecionamento.


* **Fora do Escopo:** Ambiente de produção (`fateczl.app.br`) como alvo de execução (regra absoluta de proibição), testes de carga massiva, servidor SIGA, e módulo Android.



### 4. Referências

* Especificação de Requisitos - Apêndice C.


* Norma ISO/IEC/IEEE 29119-3.


* OWASP Authentication Cheat Sheet.



### 5. Análise de risco do produto

Esta seção justifica a estratégia. O foco é a quebra de autenticação entre fronteiras e controle de estado temporal.

| ID | Risco | Prob. | Impacto | Resposta (Teste) |
| --- | --- | --- | --- | --- |
| **R01** | Redirecionamento aberto malicioso usando host fora do catálogo (RF-03) | Média | Alto | Teste da validação do catálogo na resposta de login.|
| **R02** | Sessão continuar viva num módulo após *logout* em outro (RF-02) | Alta | Alto | Teste de estados cruzados na requisição seguinte ao *logout*.|
| **R03** | Enumeração de contas ativas através da mensagem de erro (RF-01) | Média | Médio | Validar se a recusa devolve estritamente a mensagem genérica.|
| **R04** | Ataque de força bruta por ausência de CAPTCHA (RNF-11) | Alta | Alto | Testar o limite de requisições por IP/Conta na tela de login.|
| **R05** | Sequestro de sessão com *cookie* vencido (RF-04) | Alta | Alto | Teste com relógio controlado avançando mais de 15 minutos.|

### 6. Estratégia por nível

* **Unidade:** Validação criptográfica do Ed25519, validação de limites de tempo do *cookie*, função de verificação do catálogo de *hosts*.


* **Integração:** Queda de sessão cruzada (Módulo A invalidando a sessão e Módulo B respeitando), bloqueio do limitador de requisições.


* **Sistema:** Fluxo de navegação visual, garantir que *cookie* de outra pessoa derrube a sessão.



### 7. Critérios

* **Entrada:** Ambiente conteinerizado de testes isolado montado, com linguagem PHP 8.4 e banco MariaDB em memória. Massa de contas fictícias criadas (via convites).


* **Saída:** Execução de 100% dos testes do recorte com aprovação. Cobertura de ramos mínima de 90% nas classes do limitador, validação Ed25519 e verificação de *host*.


* **Suspensão:** Suspensão imediata se houver falha de ambiente (ex: banco de dados cair) ou se ocorrer bloqueio na geração do *cookie* inicial.



### 8. Ambiente e dados de teste

* **Infraestrutura:** Servidor de homologação local (sem acesso à produção), banco unificado com prefixo por domínio (ex: `admin_`, `tcc_`).


* **Crucial:** O ambiente deve possuir **relógio controlável** (Mock de tempo) para testar vencimento de sessão sem precisar aguardar os 15 minutos reais.


* **Massa de Dados:** Contas criadas estritamente pelo fluxo de convite fictício.



### 9. Cronograma e marcos

* **Semana 1-2:** Escrita, aprovação do plano e subida de containers.


* **Semana 3-4:** Escrita dos casos de teste de unidade e testes de relógio (tempo de *cookie*).


* **Semana 5-6:** Testes de integração de *logout* global e fronteiras de domínio.


* **Semana 7:** Execução da regressão final e emissão do relatório.



### 10. Papéis e responsabilidades

* **Líder de teste:** Organização, priorização dos riscos e comunicação.


* **Analistas (2):** Escrita dos casos (tabela abaixo) e testes focados em estado.


* **Automatizador:** Criação de *scripts* para avançar o relógio e simular chamadas de API.


* **Revisor:** Validação dos testes escritos contra os requisitos.



### 11. Entregáveis

* Este documento de planejamento versionado, Matriz de Rastreabilidade, Tabela de Casos de Teste e registros de defeitos.
* **Repositório:** Pasta `/plano-de-testes` no GitHub do grupo.



### 12. Rastreabilidade e Casos de Teste (O Recorte)

A matriz amarra os requisitos (RF-01 a RF-04) aos riscos levantados na seção 5. Abaixo estão os casos de teste profundos criados utilizando as técnicas recomendadas, garantindo apenas uma entrada inválida por vez:

| ID | Requisito | Técnica | Pré-condição | Passos / Entrada | Resultado Esperado |
| --- | --- | --- | --- | --- | --- |
| **CT-01** | RF-01 | Partição | Nenhuma (Visitante) | Acessar o `www` e enviar credenciais válidas. | Sessão criada no `www`, *cookie* gerado em Ed25519 para a pessoa e perfil.|
| **CT-02** | RF-01 | Suposição Erro | Nenhuma (Visitante) | Enviar e-mail inexistente, mas com formato válido. | Recusado com "mensagem genérica", sem distinguir que o e-mail não existe.|
| **CT-03** | RF-01 | Limite | Nenhuma (Visitante) | Errar credenciais sequencialmente até exceder a trava. | Bloqueado pelo limitador de requisições, sem exibição de CAPTCHA.|
| **CT-04** | RF-02 | Trans. Estado | Usuário logado em 2 módulos. | Acionar o botão "Sair" a partir do módulo `Aula`, tentar atualizar página no `TCC`. | *Cookie* apagado; na requisição seguinte o usuário é tratado como Visitante.|
| **CT-05** | RF-03 | Partição | Nenhuma (Visitante) | Endereço de *login* no `www` com parâmetro apontando para o módulo `Administrativo`. | Redirecionamento aceito, acessa o módulo Autenticado.|
| **CT-06** | RF-03 | Partição | Nenhuma (Visitante) | Parâmetro de destino para um *host* externo (ex: `[https://site-malicioso.com](https://site-malicioso.com)`). | Ação recusada; defesa contra redirecionamento aberto ativada.|
| **CT-07** | RF-04 | Limite | Sessão viva (*cookie*). | O relógio do sistema de testes avança para 14 min e 50 s; usuário faz uma requisição. | Sessão mantida e *cookie* é **reemitido** devido à proximidade de vencimento.|
| **CT-08** | RF-04 | Limite | Sessão viva (*cookie*). | Relógio avança para 15 min e 01 s sem requisições intermediárias. | *Cookie* vence; sessão é imediatamente derrubada na próxima chamada.|
| **CT-09** | RF-04 | Trans. Estado | Sessão viva (*cookie*). | O módulo recebe requisição contendo um *cookie* válido pertencente a *outra* pessoa. | A sessão original é derrubada sempre.|

### 13. Métricas e relatório

* Casos planejados x executados, relatórios de defeitos focados no módulo de identidade (severidade) e índice de quebra da matriz de rastreabilidade.