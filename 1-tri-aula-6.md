# Relatório Técnico do Projeto

Entrega: 14/04 (Individual)

## 1. Identificação
- Nome: Mateus Stainer Rakoski
- Nome do projeto: Testes do DivulgaIF

---

## 2. Fontes do Projeto
Liste todas as fontes utilizadas no desenvolvimento do projeto.

### Artigos:

- A Multi-agent LLM-based JUnit Test Generation with Strong Oracles
https://www.researchgate.net/publication/392371126_A_Multi-agent_LLM-based_JUit_Test_Generation_with_Strong_Oracles (principal)

- https://www.mdpi.com/1999-4893/18/11/717

- https://josif.ifsuldeminas.edu.br/ojs/index.php/anais/article/view/2762/2278

### Documentação

https://docs.github.com/pt/copilot/tutorials/copilot-chat-cookbook/testing-code
https://docs.github.com/pt/copilot/tutorials/customization-library/prompt-files/generate-unit-tests
https://docs.github.com/pt/copilot/tutorials/write-tests

## 3. Contexto, Problema e Objetivo (até 1/2 página)

No cenário atual do desenvolvimento de software, a agilidade na entrega de novas funcionalidades frequentemente entra em conflito com a manutenção da qualidade e da estabilidade do sistema. O projeto DivulgaIF, ao consolidar-se como uma plataforma de disseminação de informações, exige uma infraestrutura robusta que suporte o crescimento do número de usuários e a complexidade das interações de dados sem comprometer a integridade das informações.

O desenvolvimento de sistemas sem uma cultura de testes automatizados resulta em um ciclo de vida de software reativo, onde falhas são descobertas apenas em ambiente de produção. A ausência de uma suíte de testes abrangente no backend e nas camadas de persistência (SQL) gera dificuldades em:

- Identificação de Bugs: Falhas lógicas e regressões que consomem tempo excessivo de depuração manual.

- Segurança: Vulnerabilidades em endpoints que podem expor dados sensíveis por falta de validação rigorosa.

- Performance: Queries SQL ineficientes que elevam o consumo de recursos computacionais e aumentam o tempo de resposta para o usuário final.

- Débito Técnico: Estruturas de arquitetura rígidas que dificultam a escalabilidade e a refatoração segura do código.

Relevância

A implementação de testes não é apenas uma etapa acessória, mas um requisito fundamental para qualquer sistema que almeje tolerância a falhas e escalabilidade. Em um ecossistema acadêmico e informativo, a confiabilidade dos dados é crucial. Testes automatizados garantem que modificações futuras no sistema não quebrem funcionalidades existentes (regressão), permitindo que a aplicação cresça de forma sustentável. Além disso, a otimização de consultas ao banco de dados reflete diretamente na redução de custos operacionais e na melhoria da experiência do usuário, tornando o sistema mais fluido e eficiente.

Objetivo

O objetivo central deste projeto é desenvolver e implementar uma arquitetura de testes automatizados para o backend e para as camadas de integração SQL do sistema DivulgaIF. Pretende-se:

- Validar as regras de negócio através de testes unitários e de integração.

- Garantir a segurança e a performance das transações no banco de dados.

- Assegurar que a comunicação entre a API e a persistência de dados ocorra de forma otimizada e livre de gargalos lógicos.

## 4. Como fez? De onde veio a ideia? (até 1 página)

A ideia veio do grupo Thread, que fez uma IA generativa que criava queries SQL. Pensamos em usar as IAs então para fazerem queries e testes com SQL e JUnit5 no Java, aproveitando a ideia que eles tiveram E o que fizemos no front do divulgaif, com testes unitários em Jest com IA.

Utilizamos o artigo "A Multi-agent LLM-based JUnit Test Generation with Strong Oracles" que cita 3 passos principais para a criação e geração de testes com JUnit5, se tornando o padrão ouro para tal prática.  

Além disso, também utilizamos a documentação oficial do github sobre escrita de testes unitários com o copilot, já que foi nosso principal meio de escrita do arquivo em primeiro lugar, prática recomendada pelo artigo citado anteriormente.

Após isso, usamos o claude code e suas "skills" com o plugin de skill creator para se "tornarem" os outros 5 agentes citados no artigo citado usado como base.

É mais ou menos assim:



Explique:

- Quais conceitos, técnicas ou abordagens foram utilizados: 
- O que as fontes indicam como boas práticas para o problema

Importante:
- Escreva com suas próprias palavras
- Não copie trechos das fontes
- Relacione a teoria com o que será feito no projeto

---

## 5. Desenvolvimento (até 1 página)

### 5.1 O que foi feito
Descreva objetivamente a solução desenvolvida.

- O que o sistema faz?
- Quais funcionalidades foram implementadas?

---

### 5.2 Em que se baseou
Explique:

- Quais decisões técnicas foram tomadas
- Em quais fontes ou conceitos essas decisões se basearam
- Por que essas escolhas foram feitas

---

### 5.3 Papel individual

- O que fez no projeto
- Qual foi sua contribuição técnica
- Como seu trabalho e suas decisões influenciaram o resultado final

---

## 6. Resultados e Análise (até 1 página)

Apresente:

- O que funcionou
- O que não funcionou
- Evidências dos resultados (ex: testes, prints, comportamento do sistema)

Analise:

- O resultado foi o esperado?
- Quais dificuldades surgiram?
- O que impactou o desempenho ou funcionamento?

Evite apenas descrever. É necessário analisar.

---

## 7. Conclusão

Descreva:

- O que a equipe aprendeu
- Quais foram as principais limitações
- O que fariam diferente em uma próxima versão

---

## Observações Gerais

- O relatório deve ser claro, objetivo e técnico
- Evite textos genéricos
- Evite cópia de fontes
- Priorize explicações com base no que foi feito
- Demonstre entendimento, não apenas execução