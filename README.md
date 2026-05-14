![SuzanoPython003](https://github.com/user-attachments/assets/8511a4d4-7ad9-4f78-9a38-7ac458afe649)

# 🤖 Copiloto com Fluxo de Conversa Personalizado — Microsoft Copilot Studio

![Platform](https://img.shields.io/badge/Plataforma-Microsoft_Copilot_Studio-0078D4?style=flat&logo=microsoft&logoColor=white)
![GenAI](https://img.shields.io/badge/IA-Generativa_(GenAI)-6C3AC4?style=flat&logo=openai&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg)
![Status](https://img.shields.io/badge/Status-Concluído-success)
![Bootcamp](https://img.shields.io/badge/Bootcamp-Suzano_Python_Developer_%232-purple)

> Protótipo funcional de agente conversacional bancário construído no Microsoft Copilot Studio — com fluxo de diálogo customizado, GenAI calibrado por nível de qualidade e tratamento de erro orientado à experiência do usuário. Arquitetura replicável para atendimento ao cliente em instituições financeiras, healthtechs e govtechs.

---

## 1. Problema de Negócio

Equipes de atendimento ao cliente em bancos e fintechs enfrentam dois gargalos simultâneos: **alto volume de consultas repetitivas** (saldo, extrato, limites) que consomem agentes humanos, e **chatbots genéricos** que frustram usuários por não seguirem o contexto financeiro da conversa.

O desafio técnico deste projeto é demonstrar que é possível:

- **Substituir fluxos lineares genéricos** por tópicos com lógica de diálogo orientada ao domínio bancário.
- **Calibrar a qualidade das respostas de IA generativa** conforme a criticidade da operação — respostas diretas para consultas simples, completas para orientações complexas.
- **Personalizar o tratamento de falhas de compreensão** para manter a confiança do usuário em vez de devolver mensagens de erro técnicas.
- **Prototipar e publicar** um agente conversacional funcional sem infraestrutura de backend dedicada.

---

## 2. Contexto

O projeto foi desenvolvido como desafio prático do **Bootcamp Suzano – Python Developer #2** (DIO), com escopo deliberadamente focado em um domínio financeiro concreto: consulta de saldo bancário via linguagem natural.

O caso de uso escolhido (`ConsultaSaldo`) é propositalmente simples, mas requer decisões de design não triviais:

- Como coletar dado sensível (CPF) dentro do fluxo sem expor o usuário a ambiguidades?
- Qual nível de GenAI é adequado para uma consulta transacional versus uma orientação financeira?
- Como comunicar falha de compreensão sem aumentar a fricção do atendimento?

Essas decisões são as mesmas enfrentadas em implementações reais de copilotos bancários.

---

## 3. Premissas

- O ambiente de execução é o Microsoft Copilot Studio com licença Microsoft 365 ativa.
- O fluxo `ConsultaSaldo.json` utiliza saldo fixo (`R$ 1.250,00`) como mock — em produção, este nó seria substituído por chamada autenticada à API de core bancário.
- A coleta de CPF no fluxo é ilustrativa; em produção exigiria canal seguro (HTTPS) e conformidade com LGPD, incluindo mascaramento e não-persistência da variável.
- GenAI está habilitado com qualidade configurada como "alta" — adequado para respostas contextuais; para operações puramente transacionais (débito, transferência), o nível seria rebaixado para "baixa" (resposta direta, menor risco de alucinação).

---

## 4. Estratégia da Solução

A construção seguiu uma sequência de decisões técnicas deliberadas:

**Passo 1 — Copiloto em branco (não a partir de template)**

Templates pré-configurados do Copilot Studio incluem tópicos de sistema que interferem no fluxo customizado. Partir do zero garante controle total sobre quais tópicos estão ativos e quais frases de gatilho estão mapeadas.

**Passo 2 — Modelagem do tópico `ConsultaSaldo`**

O tópico foi estruturado com quatro nós sequenciais:

```
[Gatilho por frase]
        │
        ▼
[Mensagem: solicita CPF]
        │
        ▼
[Ask: captura variável cpfUsuario]
        │
        ▼
[Mensagem: consultando...]
        │
        ▼
[Mensagem: retorna saldo + oferta de continuidade]
```

Frases de gatilho cobrem variações naturais da intenção: *"Qual meu saldo?"*, *"Quero ver meu extrato"*, *"Quanto tenho na conta?"*, *"Saldo da minha conta"*.

**Passo 3 — Personalização da mensagem de erro do sistema**

A mensagem padrão de falha de compreensão foi substituída por: *"Ops! Não entendi. Pode reformular sua pergunta?"* — linguagem que preserva a percepção de competência do agente e direciona o usuário à próxima ação sem expor terminologia técnica.

**Passo 4 — Calibração do GenAI**

GenAI habilitado com qualidade "alta" no tópico `ConsultaSaldo` — adequado porque o contexto de saldo pode exigir explicações sobre tarifas, limites e datas de crédito. O trade-off é latência ligeiramente maior na resposta.

**Passo 5 — Publicação e validação no simulador**

Todos os fluxos foram testados no simulador nativo do Copilot Studio antes da publicação, validando as frases de gatilho, o caminho feliz e os caminhos de erro.

---

## 5. Decisões Técnicas e Trade-offs

**Por que Microsoft Copilot Studio e não um framework de chatbot tradicional (Rasa, Dialogflow)?**

Copilot Studio entrega fluxo visual de diálogo, integração nativa com ecossistema Microsoft 365 e GenAI sem necessidade de infraestrutura dedicada. Para o escopo do projeto — protótipo funcional de domínio bancário — o time-to-market é ordens de grandeza menor. O trade-off é menor controle sobre o modelo subjacente e dependência do ecossistema Microsoft.

**Por que JSON como formato de exportação do tópico?**

O Copilot Studio exporta tópicos em JSON, o que permite versionamento no Git, revisão de código e reimportação em outros ambientes — práticas de engenharia de software aplicadas a ativos de IA conversacional. É o equivalente a tratar fluxos de diálogo como Infrastructure as Code.

**Por que tópico com nós explícitos em vez de resposta livre via GenAI?**

Consulta de saldo é uma operação transacional com dados sensíveis. Resposta livre via GenAI aumenta o risco de alucinação (retornar um saldo inventado) e de desvio do fluxo de coleta do CPF. Nós explícitos garantem previsibilidade — GenAI é usado apenas para enriquecer a linguagem da resposta, não para determinar o fluxo lógico.

---

## 6. Estrutura do Projeto

```
copiloto-com-fluxo-conversa/
├── docs/
│   └── resumo-aprendizado.md     # Guia passo a passo das configurações no Copilot Studio
├── fluxos/
│   └── ConsultaSaldo.json        # Exportação do tópico — importável via Tópicos > Importar
├── imagens/
│   ├── criacao-copilot.png       # Tela de criação do copiloto em branco
│   ├── topico-customizado.png    # Configuração do tópico ConsultaSaldo
│   ├── mensagem-erro.png         # Personalização da mensagem de falha
│   └── ajuste-genai.png          # Configuração de qualidade do GenAI
└── README.md
```

---

## 7. Como Executar

**Pré-requisitos:** Conta Microsoft 365 com acesso ao Copilot Studio.

```
1. Acesse: https://copilotstudio.microsoft.com
2. Crie um novo copiloto → "Copiloto em branco"
3. Configure a saudação inicial
4. Vá em Tópicos > Importar → selecione fluxos/ConsultaSaldo.json
5. Personalize a mensagem de erro: Configurações > Mensagens do sistema
6. No tópico ConsultaSaldo, ative "Aprimorar com GenAI" → qualidade: Alta
7. Teste no simulador e publique
```

O guia detalhado de cada etapa está em [`docs/resumo-aprendizado.md`](docs/resumo-aprendizado.md).

---

## 8. Resultados

O protótipo entrega um agente conversacional bancário funcional capaz de:

- Reconhecer intenção de consulta de saldo em **4 variações de linguagem natural** diferentes.
- Conduzir o usuário por um fluxo estruturado de coleta de dado (CPF) sem ambiguidade.
- Retornar resposta financeira contextualizada com GenAI habilitado.
- Comunicar falha de compreensão com linguagem orientada à continuidade do atendimento.

Do ponto de vista de engenharia de produto, o que este projeto demonstra é mais relevante do que o escopo do protótipo: **a capacidade de tomar decisões arquiteturais conscientes em plataforma de IA conversacional** — escolhendo quando usar fluxo determinístico e quando delegar ao GenAI, e documentando o racional de cada decisão.

---

## 9. Aprendizados

**O que foi mais valioso:**

A distinção entre fluxo determinístico e resposta generativa não é binária. O padrão mais robusto é usar nós explícitos para controle de fluxo (coleta de dados, validação, roteamento) e GenAI para enriquecimento da linguagem da resposta — nunca para decisões transacionais. Essa separação de responsabilidades no design conversacional é o equivalente ao princípio de separação de camadas em arquitetura de software.

**O que faria diferente:**

Adicionaria um nó de validação do CPF coletado (verificar formato antes de avançar) e implementaria uma variável de sessão para não solicitar o CPF novamente caso o usuário faça uma segunda consulta na mesma conversa.

---

## 10. Próximos Passos

- [ ] Substituir o saldo mockado por chamada autenticada à API REST de core bancário
- [ ] Adicionar validação de formato de CPF no nó `ask` antes de avançar no fluxo
- [ ] Implementar variável de sessão para persistir CPF durante a conversa
- [ ] Adicionar tópicos para consulta de extrato, limite de crédito e agendamento de pagamento
- [ ] Publicar o copiloto no canal Microsoft Teams para uso interno em ambiente corporativo
- [ ] Implementar conformidade LGPD: mascaramento de CPF nos logs e política de não-persistência

---

## Tecnologias Utilizadas

| Tecnologia | Uso no Projeto |
|---|---|
| Microsoft Copilot Studio | Plataforma de criação e publicação do agente conversacional |
| GenAI (integrado ao Copilot Studio) | Enriquecimento de linguagem nas respostas do tópico |
| JSON | Versionamento e portabilidade dos fluxos de diálogo |
| Markdown | Documentação técnica e guia de configuração |

---

## Autor

**Sérgio Santos**
Senior Data Engineer & Cloud Architect

[![Portfólio](https://img.shields.io/badge/Portfólio-Sérgio_Santos-111827?style=for-the-badge&logo=githubpages&logoColor=00eaff)](https://portfoliosantossergio.vercel.app)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Sérgio_Santos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/santossergioluiz)
[![GitHub](https://img.shields.io/badge/GitHub-Santosdevbjj-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Santosdevbjj)

---

## Licença

Distribuído sob licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.
