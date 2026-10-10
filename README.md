# Plano de Testes

# Plano de Testes — Plataforma Fatec Zona Leste
## 1 Identificação e contexto
* Sistema: Plataforma Fatec Zona Leste
* Versão: 1.0
* Autores: Gabriel, Rafael, Luiz, Bruno e Lucas.
* Contexto: Esse documento é o plano de testes da plataforma, com aprofundamento específico no fluxo de Autenticação e Identidade (RF-01 a RF-04). O objetivo é garantir a segurança do login único, a correta emissão e expiração do cookie de plataforma do logout global.
## 2 Itens de teste
* Autenticador
* Identidade
* Gestão de Sessão
* Logout
## 3 Escopo
### Dentro:
Testes de unidade e integração dos requisitos RF-01 a RF-04, validação do limitador de requisições por tentativas sucessivas de login falhas, comportamento do relógio na manutenção e queda da sessão autenticada e verificação de redirecionamento apenas para hosts catalogados.
### Fora:
Qualquer execução contra o ambiente de produção real (fatec.app.br), módulo android do TCC e o sistema oficial SIGA, testes de DDOS e estresse.
## 4 Referências

## 5 Análise de risco do produto
## 6 Estratégia por nível
## 7 Critérios (entrada, saída, suspensão)
## 8 Ambiente e dados de teste
## 9 Cronograma e marcos
## 10 Papéis e responsabilidades
## 11 Entregáveis
## 12 Rastreabilidade
## 13 Métricas e relatório
